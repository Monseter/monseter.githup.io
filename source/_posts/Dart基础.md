---
title: Dart基础
typora-root-url: ./
date: 2021-01-28 15:57:09
tags: [Dart,Dart基础]
categories: 编程语言
---
## 创建个demo

```dart
// main 程序入口
void main () {
  // 控制台输出查看
  print("hello world !");
}
```

main方法是固定写法，它是程序入口

print方法可以在控制台输出内容

通过文件选择run,可以运行文件中的main方法。

## 基本数据类型

**快捷键 ctrl +F12 查看所有方法**

### 变量与常量

**变量**：使用 var 声明变量，可赋予不同类型的值；

​			未初始化时，默认值为 null；

​			使用final 声明变量，只能赋值一次，运行时常量

**常量**： 使用 const 声明常量；

​			使用 const 声明的是编译器常量，也就是在编译器就存在的；

##### final 与 const 区别：

使用`const`表示的是编译时常量，`final`表示运行时常量。

const修饰的常量必须在声明时初始化，并且是可以确定的值，不是需要经过计算的值。（存在有效值）
final修饰的常量必须在声明进初始化或者在构造函数中初始化，它的值可以动态计算。（动态变化值）

**共同点**：两者赋值后就不能再修改(不能重新赋值，对于引用类型可以修改属性)。			

看一个Dart 的常量实列：

```dart
main() {
  const test1="hello";//正确，编译期常量
  final test2="hello";//正确，运行时常量

  const test3=DateTime.now();//编译报错,值不是编译期常量
  final test4=DateTime.now();//正确，运行时常量

  const test5=new List();//编译报错,值不是编译期常量
  final test6=new List();//正确，运行时常量
}
```

## 内置数值类型

**数字** Number		**字符串** String

**布尔型** Boolean		**列表** List

**键值对**  Map			Runes、Symbols

**Number**:       **整型** int

​				        **浮点型** double

#### Number 数值类型demo

```dart
void main () {
  num a = 10; // 声明的变量为数字类型包含了整型与浮点型
  a = 20;
  a = 20.1;
  print(a);
  int b = 10; // 数值为整型只能赋值整型数字,无小数点
//  b = 12.1; // 由于数值类型不同所以无法赋值
  double c = 12.1; // 数值类型为浮点型只能赋值带有小数点数值
  c = 18; // 赋值会转换为带有小数点的浮点数值类型 18.0
  print(c);
}
```

## **数值型操作**

运算符：+、-、*、/、~/（取整）、%（取余）

## **字符串 String 操作**

使用单引号，双引号创建字符串

使用三引号，多行字符串

String 字符串 demo

```dart
void main () {
  String a = 'hello world'; // 也可以使用双引号
  String b = ''' hello 
                 world '''; // 使用三引号
  String C = 'hello \n world'; // 换行 \n
  print(C);
  String str = r'hello \n world'; // r 原始形式，该是什么样还是什么样
    
  // 字符串常用操作
    + 字符串拼接；
    * 字符串复制次数；
    [num] 根据索引位置获取指定字符串；
    插值表达式 ${experesion}；
     print("${a+str}");
    // 输出结果为 hello worldhello \n world
    print("$a"); // 省略花括号直接表示变量a
    var str1 = '${a+str}';
    
  // 常用属性
    length、isEmpty、isNotEmpty
}

```

## 列表List

创建列表的方法

```dart
var list = [] // 多个数值类型都可以
var list = const [] // 不可变数组
var list = new list() // 构造函数
```

### 常用操作

```dart
[],length
indexOf() lastIndexOf() 反向查找
add() 添加, insert() 插入
remove() 删除   clear() 清空
sort() 排序 sublist() 截取
shuffle() 打乱  asMap() 转化为对象
forEach() 循环一个列表
```

list  demo

```dart
void main () {
  var list = ['hello','world',true];
  print(list);
  list.add(5); // 从尾部追加
  print(list);
  list.insert(4, 'new'); // 根据索引进行插值
  print(list);
  print(list.indexOf('hell')); // 如果存在会返回该值的索引否则返回-1
  var list1 = list.sublist(1); // 从指定索引位置截取到末尾的数值
  print(list1);
  list.remove('hello'); // 删除指定数值
  list.forEach(print); // 该函数是将list进行循环以参数的形式传入到print函数方法中
}
```

## 键值对 Map

创建方法

```dart
var language = {'first':'dart','second':'java'}

var language = const {'first':'dart','second':'java'}

var language = new Map() // 构造函数方法
```

常用操作

```dart
[],length	containsKey() 是否包含某个Key
containsValue() 是否包含某个value
isEmpty() 是否为空	isNotEmpty()
remove()	Keys(),values() 获取所有键或者值
forEach()
```

**dynamic** 可以声明一个动态变化的变量值

```dart
dynamic b = 1;
	b= 2
// 该变量多次被赋值操作即可用 dynamic 范式
```

## 运算符

### 算术运算符

加减乘除： +、-、*、/，~/、%

递增递减：++var,var++,--var,var-- 

```dart
void main () {
  int a = 10;
  int b = 3;
  print(a++); // 表示加了一已经改变了a 但是输出是初始a值   10 实际上已经是 11
  print(++a); // 已经对a + 1 后的输出显示 12
}
```

## 关系运算符

运算符：==，！=（不等），> (大于)，<, >=,  <=

判断内容是否相等： ==

## 逻辑运算符

！(取非)      &&（与）   || （或）

## 赋值运算符

基础运算符：=  	？？=

复合运算符：+=	-=	*=	/=	~/=

```dart
int f;
  f ??= 10;  // 只有声明的变量为空才有效
  print(f);
```

## 条件表达式

```dart
// 三元运算符 ? ：
String str = 1
String str = str == 1 ? "male = $str" : "famale = $str"
    
// ？？
String str = "dart";
String str1 = "java";
String str2 = str ?? str1; // 如果str为空那么就把 str1 赋值给str2 否则 就把str 赋值给str2
```

## 控制语句

### If 条件语句

```dart
if else
if 
    else if 
  	else
```

### for 循环语句

for     for   in

for demo

```dart
void main () {
  var list = [1,2,3,4,5];
  for (var i = 0; i < list.length; i++) {
    // jS中写法
    print(list[i]);
  }
  print('----------');
  for ( var item in list) {
      // list 必须是可迭代的对象
      print(item);
  }
}
```

### while  条件循环语句

```dart
while
do while
```

demo

```dart
void main () {
  var count = 0;
  while (count < 5) {
    print(count++);
  }
  print('----$count');
  do{
    print(count--); // 先执行，作为while的执行体
  } while(count > 0 && count < 5); // 判断体
}
```

### swich  ...  case 语句

比较类型：num,  String,	编译期常量，	对象	枚举

非空 case 必须有一个break

default 处理默认情况

continue 跳转标签

```dart
String str = "Java";
switch (str) {
    case "dart":
        print("dart");
        break;
    case "Java":
        print("Java");
        break;
}

```

## 方法

### 方法定义

返回类型	方法名  （参数1，参数2, ......）{

​			方法体.....

​			return     返回值

}

### 方法特性

方法也是对象、并有具体类型Function

返回值类型、参数类型都可省略

**箭头语法：=> expr 是 { return expr;} 缩写。只适用于一个表达式**

方法都有返回值。如果没有指定，默认 return null

demo

```dart
 void main () {
  getString("Dart", "Java");
  noItem("Python", "Web");

}
// 带有返回值类型String 与 参数类型 String  (可省略)
String getString (String str1,String str2) {
  print("str1=$str1, str2=$str2"); // str1=Dart, str2=Java
}
// 无返回值类型与参数类型
noItem (str1,str2) {
  print("str1=$str1, str2=$str2");
  print(arrow(str1, str2));
}
// 箭头函数特性
int a = 2;
arrow (str1,str2) => a == 1 ? "$str1,$str2" : "不等于1"; // 箭头方法可以跟一个表达式作为返回值或者直接返回值
```

### 可选参数

定义：在传入参数时可传或不传都行。

可选命名参数： {};

可选位置参数： [];

**注意：**如果传入具体参数，那么可选参数必须放在后面！！！

demo

```dart
void main () {
  getParameters("Dart",a: 1,b: "Java"); // 可选命名传参
  getIndexParameters("Python",10,"hello world"); // 可选位置参数，必须参数位置对应上
}
getParameters (String str,{int a,String b}) { // 接受可选命名参数
  print("$str,$a,$b");
}

getIndexParameters (String str, [int b, str2]) { // 接受可选位置参数
  print("$str,$b,$str2");
}
```

### 默认参数

默认参数 =

```dart
void main () {
  getParameters("Dart");
  getParameters("Dart",a: 20);
}
getParameters (String str,{int a=10,String b}) { // 默认参数， int a =10 如果未传参则使用默认值参数，否则使用传过来的参数 
  print("$str,$a,$b");
}
```

### 方法对象

方法也是一个对象，可以将方法赋值给一个变量

可以将方法当成一个参数 

详情demo

```dart
void main () {
  String str = "dart";
  var list3 = [7,8,9];
  List list2 = [1,2,3,4,5,6];
  var getList = handelList(list2); // 将函数的返回值赋给一个变量

  addWays(list3,handelList); // 把函数方法当成参数
}
addWays (List list,f(list)) {// 这里的方法参数处理的是传进来的参数，无发另外传参
  var b = f(list);
  var newList = b + list; // 数组也可以用+ 合并
  print("$list,-----,$b");
  print(newList);
}
handelList (list) {
  List list1 = [];
  for (var item in list) {
    int a = item%2;
    a == 0 ? list1.add(item) : '';
  }
  return list1;
}
```

### 匿名函数方法

```dart
//定义方法格式

(parameter1,parameter2,....) {
    方法体。。。。
    return 返回值
}

// 匿名方法特性

'（1）可赋值给变量，通过变量进行调用'
'（2）可在其它方法中直接调用或传递给其它方法'
```

demo

```dart
void main () {
  var anonymous = (str) {
    return str*3;
  };
  print(anonymous('he'));
}
```

##### （6）闭包

闭包是一个方法（对象）；

闭包是定义在其它方法内部（函数内嵌套函数）；

闭包可以访问外部方法内的局部变量，并持有其状态 
