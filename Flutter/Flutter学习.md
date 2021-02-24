# Flutter Study

## 1. Flutter Widget

Flutter 中一切皆 Widget 呈现，通过 build 方法返回 Widget。

* 在 Flutter 中一切的显示都是 Widget ，Widget 是一切的基础，利用响应式模式进行渲染。通过 build 方法返回 Widget。

* 我们可以通过修改数据，再用 setState 设置数据，Flutter 会自动通过绑定的数据更新 Widget ， 所

  以你需要做的就是实现 **Widget** 界面，并且和数据绑定起来。

* Widget 分为 有状态 和 无状态 两种，在 Flutter 中每个⻚面都是一帧，无状态就是保持在那一帧，而 有状态的 Widget 当数据更新时，其实是创建了新的 Widget，只是 State 实现了跨帧的数据同步保存。

InkWell()

*new* Stack

*new* CorrectWrongOverlay

 void initState() {

​    super.initState();

 }

## 2. 无状态**StatelessWidget**

直接进入主题，如下下代码所示是无状态 Widget 的简单实现。继承 **StatelessWidget**，通过 **build** 方法返回一个布局好的控件。可能现在你还对 Flutter 的内置控件不熟悉，but **Don't worry , take it easy** ，后面我们就会详细介绍这里你只需要知道，一个无状态的 Widget 就是这么简单。

Widget 和 Widget 之间通过 child: 进行嵌套。其中有的 Widget 只能有一个 child，比如下方的 Container ;有的 Widget 可以多个 child ，也就是 children ，比如` Column 布局，下方代码便是

Container Widget 嵌套了 Text Widget。

```dart
class DemoWidget extends StatelessWidget {  
	final String text;
	
 //数据可以通过构造方法传递进来 
 DemoWidget(this.text);
 
 @override
 Widget build(BuildContext context) {
   //这里返回你需要的控件 
   //这里末尾有没有的逗号，对于格式化代码而已是不一样的。
   return Container(
 			//白色背景
 			color: Colors.white,
 			//Dart语法中，?? 表示如果text为空，就返回尾号后的内容。 
 			child: Text(text ?? "这就是无状态DMEO"),
		); 
	}
}
```

## 3. 有状态StatefulWidget

继续直插主题，如下代码，是有状态的widget的简单实现，你需要创建管理的是主要是 ， 通 过 State 的 build 方法去构建控件。在 State 中，你可以动态改变数据，在 之后，改变 的数据会触发 Widget 重新构建刷新，而下方代码中，是通过延两秒之后，让文本显示为 *"*这就变了数 值*"*。

如下代码还可以看出，State 中主要的声明周期有 :

- **initState** :初始化，理论上只有初始化一次，第二篇中会说特殊情况下。 

- **didChangeDependencies**:在 initState 之后调用，此时可以获取其他 State 。

- **dispose** :销毁，只会调用一次。

看到没，Flutter 其实就是这么简单!你的关注点只要在:创建你的 StatelessWidget 或者 而已。你需要的就是在 **build** 中堆积你的布局，然后把数据添加到 **Widget** 中，

最后通过`setState`改变数据，从而实现画面变化。

```dart
import 'dart:async';
import 'package:flutter/material.dart';
class DemoStateWidget extends StatefulWidget {
  final String text;
	//通过构造方法传值 
  DemoStateWidget(this.text);
	//主要是负责创建state
	@override
	_DemoStateWidgetState createState() => _DemoStateWidgetState(text);
}

class _DemoStateWidgetState extends State<DemoStateWidget> {
	String text; 
  _DemoStateWidgetState(this.text);
  
  @override
  void initState() {
    ///初始化，这个函数在生命周期中只调用一次 super.initState();
		///定时1秒
		new Future.delayed(const Duration(seconds: 1), () {
			setState(() {
				text = "这就变了数值";
			}); 
  	});
	}
  
  @override
  void dispose() {
		///销毁
		super.dispose(); 
  }
  
  @override
  void didChangeDependencies() {
		///在initState之后调 Called when a dependency of this [State] object changes.
		super.didChangeDependencies(); 
  }
  
  @override
  Widget build(BuildContext context) {
		return Container(
			child: Text(text ?? "这就是有状态DMEO"),
		); 
  }
}
```

## 4. Flutter布局

Flutter 中拥有需要将近30种内置的 布局Widget，其中常用有 *Container*、*Padding*、*Center*、*Flex*、 *Stack*、*Row*、*Column*、*ListView* 等，下面简单讲解它们的特性和使用。

| 类型      | 作用特点                                                     |
| --------- | ------------------------------------------------------------ |
| Container | 只有一个子 Widget。默认充满，包含了padding、margin、color、宽高、 decoration 等配置。 |
| Padding   | 只有一个子 Widget。只用于设置Padding，常用于嵌套child，给child设置 padding。 |
| Center    | 只有一个子 Widget。只用于居中显示，常用于嵌套child，给child设置居中。 |
| Stack     | 可以有多个子 Widget。 子Widget堆叠在一起。                   |
| Column    | 可以有多个子 Widget。垂直布局。                              |
| Row       | 可以有多个子 Widget。水平布局。                              |
| Expanded  | 只有一个子 Widget。在 Column 和 Row 中充满。                 |
| ListView  | 可以有多个子 Widget。自己意会吧。                            |

* Container :最常用的默认控件，但是实际上它是由多个内置控件组成的模版，只能包含一
   个 child ，支持 *padding,margin,color,*宽高*,decoration*(一般配置边框和阴影)等配置，在 Flutter 中，不是所有的控件都有 宽高、*padding*、*margin*、*color* 等属性，所以才会有 Padding、 Center 等 Widget 的存在。

  ```dart
  new Container(
  	///四周10大小的maring
  	margin: EdgeInsets.all(10.0), height: 120.0,
  	width: 500.0,
  	///透明黑色遮罩
  	decoration: new BoxDecoration(
  		///弧度为4.0
  		borderRadius: BorderRadius.all(Radius.circular(4.0)), ///设置了decoration的color，就不能		设置Container的color。
  		color: Colors.black,
  		///边框
  		border: new Border.all(color: Color(GSYColors.subTextColor), width: 0.3)
    ),
  	child:new Text("666666")
  );
  ```

* Column、Row 绝对是必备布局， 横竖布局也是日常中最常⻅的场景。如下方所示，它们常用的 有这些属性配置:主轴方向是 start 或 center 等;副轴方向方向是 start 或 center 等; mainAxisSize 是充满最大尺寸，或者只根据子 Widget 显示最小尺寸。

  ```dart
  //主轴方向，Column的竖向、Row我的横向 mainAxisAlignment: MainAxisAlignment.start, //默认是最大充满、还是根据child显示最小大小 mainAxisSize: MainAxisSize.max, //副轴方向，Column的横向、Row我的竖向 crossAxisAlignment :CrossAxisAlignment.center,
  ```

* Expanded 在 Column 和 Row 中代表着平均充满的作用，当有两个存在的时候默认均分充满。同 时⻚可以设置 flex 属性决定比例。

  ```dart
  new Column(
  	///主轴居中,即是竖直向居中
  	mainAxisAlignment: MainAxisAlignment.center, ///大小按照最小显示
  	mainAxisSize : MainAxisSize.min,
  	///横向也居中
  	crossAxisAlignment : CrossAxisAlignment.center, children: <Widget>[
    children: <Widget>[
  			///flex默认为1
        new Expanded(child: new Text("1111"), flex: 2,),
      	new Expanded(child: new Text("2222")),
    ],	
  );
  ```

  接下来我们来写一个复杂一些的控件，首先我们创建一个私有方法 _getBottomItem ，返回一个 Expanded Widget ，因为后面我们需要将这个方法返回的 Widget 在 Row 下平均充满。

  如代码中注释，布局内主要是现实一个居中的Icon图标和文本，中间间隔5.0的 padding:

  ```dart
  ///返回一个居中带图标和文本的Item 
  _getBottomItem(IconData icon, String text) {
  	///充满 Row 横向的布局 
    return new Expanded(
  		flex: 1, ///居中显示
  		child: new Center(
  			///横向布局 
      	child: new Row(
  				///主轴居中,即是横向居中
  				mainAxisAlignment: MainAxisAlignment.center, ///大小按照最大充满
  				mainAxisSize : MainAxisSize.max,
  				///竖向也居中
  				crossAxisAlignment : CrossAxisAlignment.center, 
          children: <Widget>[
  					///一个图标，大小16.0，灰色 
            new Icon(
  						icon,
  						size: 16.0,
  						color: Colors.grey,
  					),
  					///间隔
  					new Padding(padding: new EdgeInsets.only(left:5.0)), ///显示文本
  					new Text(
  						text,
  						//设置字体样式:颜色灰色，字体大小14.0
  						style: new TextStyle(color: Colors.grey, fontSize: 14.0), //超过的省略为...显示
  						overflow: TextOverflow.ellipsis,
  						//最⻓一行
  						maxLines: 1,
            ), 
          ],
  			), 
      ),
  	); 
  }
  ```

  接着我们把上方的方法，放到新的布局里，如下流程和代码:

  - 首先是Container 包含了Card，用于快速简单的实现圆角喝阴影。
  - 然后接下来包含了FlatButton实现了点击，通过Padding实现了边距。
  - 接着通过Column垂直包含了两个子Widget，一个是Container、一个Row。
  - Row内使用的就是_getBottomItem方法返回的Widget，效果如下。

  ```dart
  @override
  Widget build(BuildContext context) {
    return new Container(
      ///卡片包装
      child: new Card(
        ///增加点击效果
        child: new FlatButton(tom: 10.0),
        onPressed: (){print("点击了哦");},
        child: new Padding(
          padding: new EdgeInsets.only(left: 0.0, top: 10.0, right: 10.0, bot
          child: new Column(
            mainAxisSize: MainAxisSize.min,
            children: <Widget>[
              ///文本描述
              new Container(
                child: new Text(
                  "这是一点描述",
                  style: TextStyle(
                    color: Color(GSYColors.subTextColor),
                    fontSize: 14.0,
                  ),
                  ///最⻓三行，超过 ... 显示 
                  maxLines: 3,
                  overflow: TextOverflow.ellipsis,
                ),
                margin: new EdgeInsets.only(top: 6.0, bottom: 2.0),
                alignment: Alignment.topLeft
              ),
              new Padding(padding: EdgeInsets.all(10.0)),
      				///三个平均分配的横向图标文字
              new Row(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: <Widget>[
                  _getBottomItem(Icons.star, "1000"),
                  _getBottomItem(Icons.link, "1000"),
                  _getBottomItem(Icons.subject, "1000"),
                ],
              ),
            ],
            ),
          )
        )
      ),
    );
  }
  ```

  ![image-20210105153536512](/Users/liyong/Library/Application Support/typora-user-images/image-20210105153536512.png)

  Flutter 中，你的布局很多时候就是这么一层一层嵌套出来的，当然还有其他更高级的布局方式，这里 就先不展开了。

## 5. Flutter 页面

Flutter 中除了布局的 Widget，还有交互显示的 Widget 和完整⻚面呈现的Widget，其中常⻅的有 *MaterialApp*、*Scaffold*、*Appbar*、*Text*、*Image*、*FlatButton*等，下面简单介绍这些 Wdiget，并完成 一个⻚面。

| 类型        | 作用特点                                                     |
| ----------- | ------------------------------------------------------------ |
| MaterialApp | 一般作为APP顶层的主⻚入口，可配置主题，多语言，路由等        |
| Scaffold    | 一般用户⻚面的承载Widget，包含appbar、snackbar、drawer等material design 的设定。 |
| Appbar      | 一般用于Scaffold的appbar ，内有标题，二级⻚面返回按键等，当然不止这些， tabbar等也会需要它 。 |
| Text        | 显示文本，几乎都会用到，主要是通过style设置TextStyle来设置字体样式等。 |
| RichText    | 富文本，通过设置 TextSpan，可以拼接出富文本场景。            |
| TextField   | 文本输入框 :new TextField(controller: //文本控制器, obscureText:"hint文本"); |
| Image       | 图片加载:new FadeInImage.assetNetwork( placeholder: "预览图", fit:BoxFit.fitWidth, image: "url"); |
| FlatButton  | 按键点击:new FlatButton(onPressed: () {},child: new Container()); |

那么再次直插主题实现一个简单完整的⻚面试试。如下方代码:

- 首先我们创建一个StatefulWidget: DemoPage。
-  然后在_DemoPageState 中，通过build创建了一个 Scaffold 。
- Scaffold内包含了一个 AppBar和一个 ListView。
- AppBar类似标题了区域，其中设置了title为 Text Widget。
-  body是 ListView ,返回了20个之前我们创建过的 DemoItem Widget。

```dart
import 'package:flutter/material.dart';
import 'package:gsy_github_app_flutter/test/DemoItem.dart';
class DemoPage extends StatefulWidget {
	@override
	_DemoPageState createState() => _DemoPageState();
}

class _DemoPageState extends State<DemoPage> { 
  @override
	Widget build(BuildContext context) {
		///一个⻚面的开始 ///如果是新⻚面，会自带返回按键 
    return new Scaffold(
				///背景样式
				backgroundColor: Colors.blue, ///标题栏，当然不仅仅是标题栏 
     		appBar: new AppBar(
					///这个title是一个Widget
    			title: new Text("Title"),
      	),
				///正式的⻚面开始 ///一个ListView，20个Item 
     		body: new ListView.builder(
        	itemBuilder: (context, index) {
          	return new DemoItem();
					},
        	itemCount: 20,
      	),
		); 
  }
}
```

最后我们创建一个StatelessWidget作为入口文件，实现一个 MaterialApp 将上方的 DemoPage 设置 为home⻚面，通过 main 入口执行⻚面。

```dart
import 'package:flutter/material.dart';
import 'package:gsy_github_app_flutter/test/DemoPage.dart';
void main() {
  runApp(new DemoApp());
}
class DemoApp extends StatelessWidget {
  DemoApp({Key key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(home: DemoPage());
  }
}
```











