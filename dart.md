# 官方文档

[官网](https://dart.dev/get-dart)

[sdk](https://gekorm.com/dart-windows/)



# 快速开始

## 入口

```dart
main() {
    print('Hello Dart');
}
// OR
void main() {
    print('Hello Dart');
}
```



## 变量

```dart
var a = <any>; // 会做类型推断
String b = 'rekaime';
int c = 9;
double d = 1.1;
```



## 常量

`final`：不仅有`const`编译时常量的特性，而且是运行时常量，并且为惰性初始化（初次使用时才会初始化）

```dart
const String name = 'rekaime';
final String name; // 定义和初始化可以分开
name = 'rekaime';
```



## 数据类型

+ int
+ double
+ String
+ bool
+ List
+ Map

### String

```dart
String str1 = 'Hello';
String str1 = '''line 1
line2
''';

// 拼接
print('$str1 Dart');
print(str1 + ' Dart');
```



### num

```dart
num n = 9;
int a = 9;
double b = 1.1;
```



### bool

```dart
bool flag = true;
flag = false;
```



### List

```dart
List ls = ['rekaime', 18, true];
List l2 = <String>['rekaime', 'Yoimiya'];
List l3 = List.filled(<length>, <fill>, growable: false); // 默认为定长列表
     l3 = List<int>.filled(); // 可以指定类型 如果借助var的类型推导则类型为List<int>
List l4 = const [1, 4, 5, 2, 3]; // 列表本身不可更改 变量可以更改
List ln = new List(); // 新版已废弃

ls.length;
ls.add('Hello');
[...l4, 6]; // [1, 4, 5, 2, 3, 6]
[...?l4, 6];

const bool flag = true;
const String prop = 'case';

[...l4, if (flag) 'Hello']; // [1, 4, 5, 2, 3, 'Hello']
[...l4, if (prop case 'case') 'Hello']; // [1, 4, 5, 2, 3, 'Hello']
[for (int i = 0; i < 4; i++) '$i']; // [0, 1, 2, 3]
```



### Map

```dart
String name = 'rekaime';

Map student = {
    'name': name,
};
student['name'];

Map s2 = {
    name: name,
};
// { 'rekaime': 'rekaime' }
s2[name];

Map s3 = new Map();
s3['name'] = 'rekaime';
s3['name'];
```



### Set

```dart
Set s = {'a', 'b'}; // 通过类型推导定义的{}默认为Map
s.add(<el>);
s.addAll(<otherSet>);
```





## 实例方法

### 



## 运算符

### 算数运算符

```dart
int a = 11;
int b = 3;

a + b;  // 14
a - b;  // 8
a * b;  // 33
a / b;  // 4.333333333333333
a % b;  // 1
a ~/ b; // 4 取整
```



### 关系运算符

```dart
// 和其他语言一样
```



### 逻辑运算符

```dart
a && b;
a || b;
!a;
```



### 赋值运算符

```dart
// 基础赋值运算符
int a = 10;

var b = 13;
var c;
// 左侧值为null则进行赋值
b ??= 9; // 13
c ??= 9; // 9

// 复合值运算符: += -= *= /= %= ~/=
```



## 内置函数

[官方文档](https://dart.dev/libraries/dart-core)

### String

```dart
// 检索
'Never odd or even'.contains('odd'); // true
'Never odd or even'.startsWith('Never'); // true
'Never odd or even'.endsWith('even'); // true
'Never odd or even'.indexOf('odd'); // 6

// 提取
'Never odd or even'.substring(6, 9); // 'odd'
'Never odd or even'.split(' '); // [Never, odd, or, even] 非常量
'Never odd or even'[0]; // 'N'
for (final char in 'Never odd or even'.split('')) print(char);
'Never odd or even'.codeUnits;
// [78, 101, 118, 101, 114, 32, 111, 100, 100, 32, 111, 114, 32, 101, 118, 101, 110]

// 转化
'web apps'.toUpperCase(); // 'WEB APPS'
'WEB APPS'.toLowerCase(); // 'web apps'

// 格式
'  hello  '.trim(); // 'hello'
''.isEmpty; // true
'  '.isNotEmpty; // true

// 替换
'Hello, NAME!'.replaceAll(RegExp('NAME'), 'Rekaime'); // Hello, Rekaime!

// 构建
final builder = StringBuffer();
builder..write('Hello')..writeAll([' Dart', ' and', ' Flutter']);
builder.toString(); // Hello Dart and Flutter

// 正则
final reg = RegExp(r'\d+');
final str = 'Im 18 years old';
str.contains(reg); // true
str.replaceAll(reg, '20'); // Im 20 years old
reg.hasMatch(str); // true
for (final match in reg.allMatches(str)) match.group(0); // 18
```



### num

```dart
int.parse('42'); // 42
int.parse('0x42'); // 66
int.parse('42', radix: 16); // 66
double.parse('0.50'); // 0.5
double.parse('1.2e+2'); // 120.0

// 能转int值就是int 否则是double
num.parse('42');   // 42
num.parse('0x42'); // 66
num.parse('0.50'); // 0.5

<num>.toString(); // '<num>'

123.456.toStringAsFixed(2); // '123.46'
123.456.toStringAsPrecision(2); // '1.2e+2'
```



### Collections

#### common

```dart
.isEmpty;
.isNotEmpty;

// 作为Collection返回时 类型是Iterable<T> 需要调用to<type>()方法转型
.forEach((el) => ...); // 如果是Map 回调允许有两个参数 (k, v) => ...
.map((el) => <mapValue>);
.where((el) => <boolValue>); // 返回结果包含所有通过校验的元素
.any((el) => <boolValue>); // 是否至少有一个元素通过校验
.every((el) => <boolValue>); // 是否所有元素都通过校验
```



#### List

```dart
final List l = <String>[];

.add('rekaime'); // l: [rekaime]
.addAll(<String>['Genshin', 'Star Rail', 'ZZZ']); // l: [rekaime, Genshin, Star Rail, ZZZ]
.indexOf('rekaime'); // 0
.removeAt(0); // l: [Genshin, Star Rail, ZZZ]
.remove('rekaime'); // 移除第一个匹配的元素
.clear(); // l: []

.sort((a, b) => a.compareTo(b));
.reverse; // 获取逆序迭代器
.fillRange(startIndex, endIndex, insertEl); // 将某一段填充为 insertEl
.insert(index, el); // 插入而非填充
.insertAll(index, iterable);
.join(separator='');
```



#### Set

```dart
final Set s = <String>{};

.add('rekaime');
.addAll(<String>['Genshin', 'Star Rail', 'ZZZ']); // s: {rekaime, Genshin, Star Rail, ZZZ}
.remove('rekaime'); // s: {Genshin, Star Rail, ZZZ}
Set.from(['Genshin', 'Star Rail', 'ZZZ']); // {Genshin, Star Rail, ZZZ}

.contains('Genshin'); // true
.containsAll(['Genshin', 'rekaime']); // false
.join(separator='');

.intersection(otherSet); // 返回交集
.union(otherSet); // 并集
.difference(otherSet); // 差集
```



#### Map

```dart
.containsKey(key); // <bool>
.containsValue(value); // <bool>
.remove(key); // 返回被删除的k对应的值
.keys;
.values;
.putIfAbsent(key, fn); // 如果key不存在 则插入值
```



## 函数

