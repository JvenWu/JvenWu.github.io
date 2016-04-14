## for IOS: Base Concept | 基本概念

### File
- .h文件 可定义类
- .m文件 实现相关类成员

### @interface
```objectivec
@interface ViewController : UIViewController //ViewController继承UIViewController类
<UIPickerViewDataSource,UIPickerViewDelegate>{ //实现UIPickerViewDataSource,UIPickerViewDelegate (Protocal,对应Java中接口
    NSArray *activities;
    NSArray *feelings;
    IBOutlet UIPickerView *dataPicker;
    IBOutlet UITextField *noteText;
}
@property (retain,nonatomic) UIPickerView *dataPicker;
@property (retain,nonatomic) UITextField *noteText;

- (IBAction)pressedButton:(id)sender;
- (IBAction)textDidDone:(id)sender;

@end
```
