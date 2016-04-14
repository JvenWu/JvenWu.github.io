## for IOS: Base Concept | 基本概念

### File
- .h文件 可定义类
- .m文件 实现相关类成员

### @interface
>可用于定义类,枚举,块等
```objectivec
@interface ViewController : UIViewController    //ViewController继承UIViewController类
<UIPickerViewDataSource,UIPickerViewDelegate>{  //实现UIPickerViewDataSource,UIPickerViewDelegate协议,对应Java中的接口
    NSArray *activities;    //Field,字段
    NSArray *feelings;
    IBOutlet UIPickerView *dataPicker;
    IBOutlet UITextField *noteText;
}
@property (retain,nonatomic) UIPickerView *dataPicker;  //属性
@property (retain,nonatomic) UITextField *noteText;

- (IBAction)pressedButton:(id)sender;   //方法
- (IBAction)textDidDone:(id)sender;

@end
```
- Class: ViewController
- BaseClass: UIViewController
- Protocal: UIPickerViewDataSource,UIPickerViewDelegate
- Field: activities,feelings
- Property: dataPicker,noteText
- Method: pressedButton,textDidDone
