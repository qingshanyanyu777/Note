## WPF

Wpf 像素无关 winform是像素

## 布局控件

### Grid

### StackPanel

默认一行一列

~~~ xaml
<Window ---------------------------------
        WindowStartupLocation="CenterScreen" // 这里是设置的窗口启动在屏幕中间
        Title="MainWindow" Height="450" Width="800">
        <WindowChrome.WindowChrome>
        <WindowChrome Glas  sFrameThickness="0"/>
    </WindowChrome.WindowChrome>   //这几行是设置程序边框为0
    
    <!--背景颜色 渐变 画刷-->
<Grid.Background>
    <RadialGradientBrush> //从中心向外渐变    LinearGradientBrush
        <!--由内向外-->
        <GradientStop Color="red" Offset="0"></GradientStop>
        <GradientStop Color="Green" Offset="0.5"></GradientStop>
        <GradientStop Color="Blue" Offset="1"></GradientStop>
    </RadialGradientBrush>
</Grid.Background>
~~~

``` c#
&#13; //这是换行的标志
```

 