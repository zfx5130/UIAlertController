# UIAlertController


  系统的UIAlertController & UIAlertAction, 因为要自定义UIAlertController样式，所以花费了一点时间研究AlertController,查看对应runtime属性，通过KVO形式修改了系统原生控件的相应属性，但由于暴露的属性太少，导致最后没有达到理想的效果,例如：alertAction的高度，圆角等不能修改（也许我没有找到合理的方式），最后决定放弃修改原生控件，自己封装自定义一个。不过有的小伙伴只需要对字体和颜色，或者添加简单图片的，完全可以在原有基础上修改，下面会列出具体的方法。
  
  - 控件字体颜色，字体大小
  - 给对应Button添加左图片
  - 添加自定义背景视图等
  - 修改后效果如下图：
  ![head](https://github.com/zfx5130/UIAlertController/blob/master/test.gif)
  
###### 修改字体颜色

  - 修改UIAlertController的Title,Message的颜色。查看runtime属性可知UIAlertController有这个两个属性，可以通过KVO方式修改字体颜色。
 
参考如下：
  
         NSAttributedString *_attributedMessage;
         NSAttributedString *_attributedTitle;
                  
                  
示例如下:

  	    1. let attributes = NSMutableAttributedString(string: "选择发布的内容") 
 	   2. attributes.addAttributes([NSForegroundColorAttributeName :  	   color], range: NSMakeRange(0, attributes.length))
	   3. alertController.setValue(attributes, forKey: "attributedTitle")
	 
- 修改UIAlertAction的title字体颜色，方式如上。
参考如下：

		 UIColor *_titleTextColor; 

示例如下：
     
      1. let picture = UIAlertAction(title: "相册照片", style: .Default, handler: { (action) -> Void in
            print("Ok Button Pressed")
        })
        
      2.let color = UIColor(red: 59 / 255.0, green: 80 / 255.0, blue: 105 / 255.0, alpha: 1.0)
      
      3.picture.setValue(color, forKey: "titleTextColor")
      
          
##### 修改字体大小
 	
 - 如果修改UIAlertController中title及和UIAlertActionTitle字体的大小，我查找相关资料，找到一种修改字体大小的方式，代码如下：
 	
 	
 		var apperanceLabel: UILabel
        if #available(iOS 9.0, *) {
            apperanceLabel = UILabel.appearanceWhenContainedInInstancesOfClasses([UIAlertController.self])
        } else {
            apperanceLabel = UILabel.my_appearanceWhenContainedIn(UIAlertController.self)
        }
        apperanceLabel.appearanceFont = UIFont.boldSystemFontOfSize(14)
        
ps:对于my_appearanceWhenContainedIn这个方法，我查找了相关资料，发现Swift中8.0没有原生的appearanceWhenContainedIn方法，而OC中却存在，所以想要实现此方法，需要些一个OC的Category,代码内容如下：

	//.h文件内容
	#import <Foundation/Foundation.h>
    #import <UIKit/UIKit.h>
	@interface UILabel (FontAppearance)
		
	@property (nonatomic, copy) UIFont * appearanceFont UI_APPEARANCE_SELECTOR;				
	+ (instancetype)my_appearanceWhenContainedIn:(Class<UIAppearanceContainer>)containerClass;
		
	#import "UILabel+ActionSheet.h"

	//.m文件内容
	@implementation UILabel (FontAppearance)

	-(void)setAppearanceFont:(UIFont *)font {
    if (font)
     	   [self setFont:font];
	}

	-(UIFont *)appearanceFont {
   	 return self.font;
	}

	+ (instancetype)my_appearanceWhenContainedIn:(Class<UIAppearanceContainer>)containerClass {
  	   return [self appearanceWhenContainedIn:containerClass, nil];
	}

#### 设置button左侧图片
 
 - 参考如下：
 
     UIImage *_image;
     UIColor *_imageTintColor;

 - 示例代码如下： 
 		 	let picture = UIAlertAction(title: "相册照片", style: .Default, handler: { (action) -> Void in
            print("Ok Button Pressed")
        })
        
       	 	let image2 = UIImage(named: "action_sheet_photo_image_icon")
        	picture.setValue(image2?.imageWithRenderingMode(.AlwaysOriginal), forKey: "image")
        


ps: 还有其他相关的可以通过KVO去修改设置的相关，大家可以根据需求，进行修改。如果想想参考源码，可点击这里查看
 [Demo示例](https://github.com/zfx5130/UIAlertController/blob/master/TestViewController.swift)
	
  
#### 参考资料

  - [UIAlertController runtime header](https://github.com/nst/iOS-Runtime-Headers/blob/master/Frameworks/UIKit.framework/UIAlertController.h)
  
  - [UIAlertAction runtime header](https://github.com/nst/iOS-Runtime-Headers/blob/master/Frameworks/UIKit.framework/UIAlertAction.h)
  
  