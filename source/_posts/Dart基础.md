---
title: Dart基础
typora-root-url: ./
date: 2021-01-28 15:57:09
tags: [Dart,Dart基础]
categories: 编程语言
---
## 简介

Dart 是谷歌开发的计算机编程语言，被应用于 Web、服务器、移动应用和物联网等领域的开发。Dart 是面向对象、类定义的、单继承的语言。它的语法类似C语言，可以转译为 JavaScript，支持接口(interfaces)、混入(mixins)、抽象类(abstract classes)、具体化泛型(reified generics)、可选类型(optional typing)和sound type system。

## 变量

变量是一个引用，根据Dart中“万物皆对象”原则，即变量存储的都是对象的引用，或者说它们都是指向对象。

### **声明变量**

声明可以有两种方式，一种是不指定类型，即使用 `var` 关键字。

```dart
var name = 'bob';
```

另一种声明是明确指定类型 （Optional types）即具体化泛型。

```dart
String name = 'bob';
```

> 因为有类型推导，所以两种实现效果一样，官方推荐在函数内的本地变量尽量使用var声明。

在变量类型并不明确的情况下，可以使用dynamic关键字。

```dart
dynamic name = 'bob';
```

### 默认值

未初始化的变量默认值是 null。即使变量是数字类型默认值也是 null，因为在 Dart 中一切都是对象，数字类型也不例外。

```dart
int count;
print(count == null );  // true
```

### Final 和 Const

使用过程中从来不会被修改的变量， 可以使用 final 或 const，而不是 var 或者其他类型，Final 变量的值只能被设置一次； Const 变量在编译时就已经固定 (Const 变量 是隐式 Final 的类型) 。最高级 final 变量或类变量在第一次使用时被初始化。

> 提示： 实例变量可以是 final 类型但不能是 const 类型。 必须在构造函数体执行之前初始化 final 实例变量 —— 在变量声明中，参数构造函数中或构造函数的初始化列表中进行初始化。

创建和设置一个 Final 变量：

```dart
final String name = 'bob';
const nickName = 'bob';
name = '11'; // Error: Setter not found: 'name'.
```

## 内置数据类型

> **快捷键 ctrl +F12 查看所有方法**

Dart 语言支持以下内建类型：

- Number
- String
- Boolean
- List <也被称为 Array>
- Map
- Set
- Rune （用于在字符串中表示 Unicode 字符）
- Symbol

### Number

Dart 语言的 Number 有两种类型:

- int 整数

  整数值不大于64位， 具体取决于平台。 在 Dart VM 上， 值的范围从 -263 到 263 - 1. Dart 被编译为 JavaScript 时，使用 JavaScript numbers, 值的范围从 -253 到 253 - 1.

- double  浮点数

  64位（双精度）浮点数，依据 IEEE 754 标准。带有小数点的。

int 和 double 都是 num. 的亚类型。 num 类型包括基本运算 +， -， /， 和 *， 以及 abs()， ceil()， 和 floor()， 等函数方法。（按位运算符，例如»，定义在 int 类中。） 如果 num 及其亚类型找不到你想要的方法， 尝试查找使用 dart:math 库。

整数类型不包含小数点。 下面是定义整数类型字面量的例子:

```dart
var two = 2;
int two1 = 2;
print(two == two1); // true
```

> **版本提示：** 在 2.1 之前，在 double 上下文中使用 int 字面量是错误的。
>
> 以下是将字符串转换为数字的方法，反之亦然：

```dart
var one = int.parse('1'); // String -> int
var doubleStr = double.parse('1.2'); // String -> int
// int -> String
String numInt = 1.toString();
// double -> String 
String numDouble = 3.1415926.toStringAsFixed(2); // 保留两位有效数字
```

int 特有的传统按位运算操作，移位（<<， >>），按位与（&）以及 按位或（|）。 例如：

```dart
print( (3 << 1) == 6); // 0011 << 1 == 0110
```

### String 字符串

Dart 字符串是一组 UTF-16 单元序列。 字符串通过单引号或者双引号创建。

字符串常用方法如下：

- 字符串插值表达式 ${}

  ```dart
  // 字符串插值 '${变量}'
  var s = 'dart';
  print('this is ${s}'); // this is dart
  ```

- 字符串拼接  +

  ```dart
  String firstName = '佩奇';
  String lastName = '朱';
  print(firstName + lastName); // 朱佩奇
  ```

- 使用连续三个单引号或者三个双引号实现多行字符串对象的创建

  ```dart
  var str = '''
          You can create
          multi-line strings like this one.
      ''';
  ```

- 使用 r 前缀，可以创建 “原始 raw” 字符串：

  ```dart
  var s = r"In a raw string, even \n isn't special.";
  ```

### Boolean

Dart 使用 bool 类型表示布尔值。 Dart 只有字面量 true and false 是布尔类型， 这两个对象都是编译时常量。

```dart
bool nan = true
```

### List

几乎每种编程语言中最常见的集合可能是 array 或有序的对象集合。 在 Dart 中的 Array 就是 [List](https://api.dartlang.org/stable/dart-core/List-class.html) 对象， 通常称之为 List 。

Dart 中的 List 字面量非常像 JavaScript 中的 array 字面量。 下面是一个 Dart List 的示例：

```dart
var list = [1, 2, 3];
```

> **提示：** Dart 推断 `list` 的类型为 `List<int>` 。 如果尝试将非整数对象添加到此 List 中， 则分析器或运行时会引发错误。

Lists 的下标索引从 0 开始，第一个元素的索引是 0。 list.length - 1 是最后一个元素的索引。 访问 List 的长度和元素与 JavaScript 中的用法一样：

```dart
ar list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);
```

在 List 字面量之前添加 const 关键字，可以定义 List 类型的编译时常量：

```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // 取消注释会引起错误。
```

- 常用方法如下

  ```dart
  [],length
  indexOf() lastIndexOf() 反向查找
  add() 添加, insert() 插入
  remove() 删除   clear() 清空
  sort() 排序 sublist() 截取
  shuffle() 打乱  asMap() 转化为对象
  forEach() 循环一个列表
  ```

### Set

在 Dart 中 Set 是一个元素唯一且无需的集合。 Dart 为 Set 提供了 Set 字面量和 [Set](https://api.dartlang.org/stable/dart-core/Set-class.html) 类型。

> **版本提示：** 虽然 Set *类型* 一直是 Dart 的核心部分， 但在 Dart2.2 中才引入了 Set *字面量* 。

下面是通过字面量创建 Set 的一个简单示例：

```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

> **Note:** Dart 推断 `halogens` 类型为 `Set<String>` 。如果尝试为它添加一个 错误类型的值，分析器或执行时会抛出错误。

要创建一个空集，使用前面带有类型参数的 {} ，或者将 {} 赋值给 Set 类型的变量：

```dart
var names = <String>{};
// Set<String> names = {}; // 这样也是可以的
// var names = {}; // 这样会创建一个 Map ，而不是 Set 
```

**是 Set 还是 Map ？** Map 字面量语法同 Set 字面量语法非常相似。 因为先有的 Map 字母量语法，所以 `{}` 默认是 `Map` 类型。  如果忘记在 `{}` 上注释类型或赋值到一个未声明类型的变量上，  那么 Dart 会创建一个类型为 `Map<dynamic, dynamic>` 的对象。

- 常用方法

  使用 add() 或 addAll() 为已有的 Set 添加元素

  ```dart
  var elements = <String>{};
  elements.add('fluorine');
  ```

  使用 .length 来获取 Set 中元素的个数：

  ```dart
  var elements = <String>{};
  elements.add('fluorine');
  print(elements.length == 1); // true
  ```

  在 Set 字面量前增加 const ，来创建一个编译时 Set 常量：

  ```dart
  final constantSet = const {
    'fluorine',
    'chlorine',
    'bromine',
    'iodine',
    'astatine',
  };
  ```

### Map

通常来说， Map 是用来关联 keys 和 values 的对象。 keys 和 values 可以是任何类型的对象。在一个 Map 对象中一个 key 只能出现一次。 但是 value 可以出现多次。 Dart 中 Map 通过 Map 字面量 和 Map 类型来实现。

下面是使用 Map 字面量的两个简单例子：

```dart
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

> Dart 会将 `gifts` 的类型推断为 `Map<String, String>`， `nobleGases` 的类型推断为 `Map<int, String>` 。 如果尝试在上面的 map 中添加错误类型，那么分析器或者运行时会引发错误。

以上 Map 对象也可以使用 Map 构造函数创建：

```dart
var gifts = Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';
```

> **提示:** 这里为什么只有 `Map()` ，而不是使用 `new Map()`。 因为在 Dart 2 中，`new` 关键字是可选的。 有关更多信息，参考 构造函数的使用。

- 常用方法

  ```
  [],length	containsKey() 是否包含某个Key
  containsValue() 是否包含某个value
  isEmpty() 是否为空	isNotEmpty()
  remove()	Keys(),values() 获取所有键或者值
  forEach()
  ```

  类似 JavaScript ，添加 key-value 对到已有的 Map 中：

  ```dart
  var gifts = {'first': 'partridge'};
  gifts['fourth'] = 'calling birds'; // Add a key-value pair
  ```

  类似 JavaScript ，从一个 Map 中获取一个 value：

  ```dart
  var gifts = {'first': 'partridge'};
  assert(gifts['first'] == 'partridge');
  ```

  如果 Map 中不包含所要查找的 key，那么 Map 返回 null：

  ```dart
  var gifts = {'first': 'partridge'};
  assert(gifts['fifth'] == null);
  ```

  使用 .length 函数获取当前 Map 中的 key-value 对数量：

  ```dart
  var gifts = {'first': 'partridge'};
  gifts['fourth'] = 'calling birds';
  assert(gifts.length == 2);
  ```

  创建 Map 类型运行时常量，要在 Map 字面量前加上关键字 const。

  ```dart
  final constantMap = const {
    2: 'helium',
    10: 'neon',
    18: 'argon',
  };
  ```



### Rune

在 Dart 中， Rune 用来表示字符串中的 UTF-32 编码字符。

Unicode 定义了一个全球的书写系统编码， 系统中使用的所有字母，数字和符号都对应唯一的数值编码。 由于 Dart 字符串是一系列 UTF-16 编码单元， 因此要在字符串中表示32位 Unicode 值需要特殊语法支持。

表示 Unicode 编码的常用方法是， \uXXXX, 这里 XXXX 是一个4位的16进制数。 例如，心形符号 (♥) 是 \u2665。 对于特殊的非 4 个数值的情况， 把编码值放到大括号中即可。 例如，emoji 的笑脸 (�) 是 \u{1f600}。

String 类有一些属性可以获得 rune 数据。 属性 codeUnitAt 和 codeUnit 返回16位编码数据。 属性 runes 获取字符串中的 Rune 。

下面是示例演示了 Rune 、 16-bit code units、 和 32-bit code points 之间的关系。

```dart
main() {
  var clapping = '\u{1f44f}';
  print(clapping);
  print(clapping.codeUnits);
  print(clapping.runes.toList());

  Runes input = new Runes(
      '\u2665  \u{1f605}  \u{1f60e}  \u{1f47b}  \u{1f596}  \u{1f44d}');
  print(new String.fromCharCodes(input));
}
```

### Symbol

一个 Symbol 对象表示 Dart 程序中声明的运算符或者标识符。 你也许永远都不需要使用 Symbol ，但要按名称引用标识符的 API 时， Symbol 就非常有用了。 因为代码压缩后会改变标识符的名称，但不会改变标识符的符号。 通过字面量 Symbol ，也就是标识符前面添加一个 # 号，来获取标识符的 Symbol 。

## 函数

### 可选参数

可选参数可以是命名参数或者位置参数，但一个参数只能选择其中一种方式修饰。

- 命名可选参数

  定义函数是，使用 {param1, param2, …} 来指定命名参数：

  ```dart
  void fn({bool bold, bool hidder}) {}
  // 调用函数时，可以使用指定命名参数 paramName: value。
  fn(bold: true, hdeer: false)
  ```

  Flutter 创建实例的表达式可能很复杂， 因此窗口小部件构造函数仅使用命名参数。 这样创建实例的表达式更易于阅读。

  使用 @required 注释表示参数是 required 性质的命名参数， 该方式可以在任何 Dart 代码中使用（不仅仅是Flutter）。

  ```dart
  const Scrollbar({Key key, @required Widget child})
  ```

  此时 Scrollbar 是一个构造函数， 当 child 参数缺少时，分析器会提示错误。

- 位置可选参数

  将参数放到 [] 中来标记参数是可选的：

  ```dart
  String fn(String from, String msg, [String device]) {
      var res = '$from says $msg';
      if (device != null) {
          res = '$res with $device';
      }
      return res;
  }
  // 方法调用
  var res = fn('bob','china');
  print(res);
  ```

- 默认参数值

  在定义方法的时候，可以使用 = 来定义可选参数的默认值。 默认值只能是编译时常量。 如果没有提供默认值，则默认值为 null。

  下面是设置可选参数默认值示例：

  ```dart
  /// 设置 [bold] 和 [hidden] 标志 ...
  void enableFlags({bool bold = false, bool hidden = false}) {...}
  
  // bold 值为 true; hidden 值为 false.
  enableFlags(bold: true);
  ```

  > 不推荐： 旧版本代码中可能使用的是冒号 (:) 而不是 = 来设置参数默认值。 原因是起初命名参数只支持 : 。 这种支持可能会被弃用。 建议 使用 = 指定默认值。

### 函数基础

- main()  函数

  任何应用都必须有一个顶级 main() 函数，作为应用服务的入口。 main() 函数返回值为空，参数为一个可选的 List<String> 。

  下面是 web 应用的 main() 函数：

  ```dart
  void main() {
    querySelector('#sample_text_id')
      ..text = 'Click me!'
      ..onClick.listen(reverseText);
  }
  ```

  > 提示：
  >
  > 以上代码中的 .. 语法为 级联调用 （cascade）。 使用级联调用， 可以简化在一个对象上执行的多个操作。

- 函数是一等对象

  一个函数可以作为另一个函数的参数。 例如：

  ```dart
  void printElement(int element) {
    print(element);
  }
  
  var list = [1, 2, 3];
  
  // 将 printElement 函数作为参数传递。
  list.forEach(printElement);
  ```

  同样可以将一个函数赋值给一个变量，例如：

  ```dart
  var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
  assert(loudify('hello') == '!!! HELLO !!!');
  ```

  示例中使用了匿名函数，参考下面。

- 匿名函数

  多数函数是有名字的， 比如 main() 和 printElement()。 也可以创建没有名字的函数，这种函数被称为 匿名函数， 有时候也被称为 lambda 或者 closure 。 匿名函数可以赋值到一个变量中， 举个例子，在一个集合中可以添加或者删除一个匿名函数。

  匿名函数和命名函数看起来类似— 在括号之间可以定义一些参数或可选参数，参数使用逗号分割。

  后面大括号中的代码为函数体：

  下面例子中定义了一个包含一个无类型参数 item 的匿名函数。 list 中的每个元素都会调用这个函数，打印元素位置和值的字符串。

  ```dart
  var list = ['apples', 'bananas', 'oranges'];
  list.forEach((item) {
    print('${list.indexOf(item)}: $item');
  });
  ```

  如果函数只有一条语句， 可以使用箭头简写。

  ```dart
  list.forEach( (item) => print('${list.indexOf(item)}: $item'));
  ```

### 函数其它概念

- 作用域

  Dart 是一门词法作用域的编程语言，就意味着变量的作用域是固定的， 简单说变量的作用域在编写代码的时候就已经确定了。 花括号内的是变量可见的作用域。

  下面示例关于多个嵌套函数的变量作用域：

  ```dart
  bool topLevel = true;
  
  void main() {
    var insideMain = true;
  
    void myFunction() {
      var insideFunction = true;
  
      void nestedFunction() {
        var insideNestedFunction = true;
  
        assert(topLevel);
        assert(insideMain);
        assert(insideFunction);
        assert(insideNestedFunction);
      }
    }
  }
  ```

  > 注意:
  >
  > nestedFunction() 可以访问所有的变量， 一直到顶级作用域变量。

- 闭包

  闭包 即一个函数对象，即使函数对象的调用在它原始作用域之外， 依然能够访问在它词法作用域内的变量。

  函数可以封闭定义到它作用域内的变量。 接下来的示例中， makeAdder() 捕获了变量 addBy。 无论在什么时候执行返回函数，函数都会使用捕获的 addBy 变量。

  ```dart
  /// 返回一个函数，返回的函数参数与 [addBy] 相加。
  Function makeAdder(num addBy) {
    return (num i) => addBy + i;
  }
  
  void main() {
    // 创建一个加 2 的函数。
    var add2 = makeAdder(2);
  
    // 创建一个加 4 的函数。
    var add4 = makeAdder(4);
  
    assert(add2(3) == 5);
    assert(add4(3) == 7);
  }
  ```

## 运算符

### 算术运算符

Dart 支持常用的运算运算符，如下表所示：

| Operator | Meaning |
| :------: | :-----: |
|    +     |   加    |
|    -     |   减    |
|  -expr   |  反转   |
|    *     |   乘    |
|    /     |   除    |
|    ~/    |  取整   |
|    %     |  取余   |

Dart 还支持前缀和后缀，自增和自减运算符。

| Operator | Meaning |
| :------: | :-----: |
| ++*var*  |  递增   |
| *var*++  |  递增   |
| --*var*  |  递减   |
| *var*--  |  递减   |

### 关系运算符

| **Operator** | **Meaning** |
| :----------: | :---------: |
|      ==      | 值内容相等  |
|      !=      |    不等     |
|      \>      |    大于     |
|      <       |    小于     |
|     \>=      |  大于等于   |
|      <=      |  小于等于   |

要测试两个对象x和y是否表示相同的事物， 使用 == 运算符。 (在极少数情况下， 要确定两个对象是否完全相同，需要使用 identical() 函数。) 下面给出 == 运算符的工作原理：

1. 如果 x 或 y 可以 null，都为 null 时返回 true ，其中一个为 null 时返回 false。
2. 结果为函数 x.==(y) 的返回值。 (如上所见, == 运算符执行的是第一个运算符的函数。 我们甚至可以重写很多运算符，包括 ==， 运算符的重写，参考 重写运算符。）

### 类型判定运算符

as， is， 和 is! 运算符用于在运行时处理类型检查：

| **Operator** |          **Meaning**          |
| :----------: | :---------------------------: |
|      as      |      将对象转为指定类型       |
|      is      | 判断是否为指定类型，是则 true |
|     is!      |           与上相反            |

### 赋值运算符

- 避空运算符

  避空运算符 ??= 仅当该变量为空值时才为其赋值

  ```dart
  int a;
  a ??= 3;
  // print(a) 3;
  ```

  另外一个避空运算符 ?? 如果该运算符左边的表达式返回的是空值，则会计算并返回右边的表达式

  ```dart
  print( 1 ?? 3 );  1
  print( null ?? 3 ); 
  ```

  条件属性访问 ?. 保护可能会为空的属性正常访问

复合运算符：+=	-=	*=	/=	~/=

```dart
int f;
  f ??= 10;  // 只有声明的变量为空才有效
  print(f);
```

### 逻辑运算符

逻辑操作符可以反转或组合布尔表达式。

| Operator | Meaning |
| :------: | :-----: |
| !*expr*  |  取非   |
|   `||`   | 或逻辑  |
|   `&&`   | 与逻辑  |

### 条件表达式

```dart
// 三元运算符 ? ：
String str = 1
String str = str == 1 ? "male = $str" : "famale = $str"
    
// ？？
String str = "dart";
String str1 = "java";
String str2 = str ?? str1; // 如果str为空那么就把 str1 赋值给str2 否则 就把str 赋值给str2
```

### 级联运算符（..）

级联运算符 (..) 可以实现对同一个对像进行一系列的操作。 除了调用函数， 还可以访问同一对象上的字段属性。 这通常可以节省创建临时变量的步骤， 同时编写出更流畅的代码。

```dart
querySelector('#confirm') // 获取对象。
  ..text = 'Confirm' // 调用成员变量。
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```



## 控制语句

### If 条件语句

```dart
if else
if 
    else if 
  	else
```

> 和 JavaScript 不同， Dart 的判断条件必须是布尔值，不能是其他类型。

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

