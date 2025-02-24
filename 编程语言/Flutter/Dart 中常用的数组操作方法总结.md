# Dart 中常用的数组操作方法总结

这里总结了一些在 Dart 中常用的数组操作方法，以便查阅。

首先，我们准备两组数据，以方便后面使用：



```dart
List<Map> students = [
 { 'name': 'tom', 'age': 16 },
 { 'name': 'jack', 'age': 18 },
 { 'name': 'lucy', 'age': 20 }
];

List numbers = [2, 8, 5, 1, 7, 3];
```

本文中用到的 `students` 及 `numbers` 均指的是这里已经定义好的，后面不再重复。

## forEach()

可以遍历一个数组或者是一个map



```php
students.forEach((student) => print(student));
// {name: tom, age: 16}
// {name: jack, age: 18}
// {name: lucy, age: 20}

Map tom = { 'name': 'tom', 'age': 16 };
tom.forEach((key, value) => print(key + ' - ' + value.toString()));
// name - tom
// age - 16
```

## Map()

可以用来操作已知数组里的每一项，然后返回一个新数组



```dart
var messages = students.map((student) => 'Hello ' + student['name']).toList();
print(messages);
// [Hello tom, Hello jack, Hello lucy]
```

## contains()

用于判断数组是否包含某个元素



```dart
print(numbers.contains(5));
// true
```

## sort()

用于排序，接收一个函数当做参数



```dart
numbers.sort((num1, num2) => num1 - num2);
print(numbers);
// [1, 2, 3, 5, 7, 8]
```



## reduce(), fold()

- `reduce()` 将数组中的每一个值与前面返回的值相加，最后返回相加的总和
- `fold()` 用法跟 `reduce()` 基本一样，只不过是可以提供一个初始值



```dart
var sum = numbers.reduce((curr, next) => curr + next);
print(sum);
// 26

var sum2 = numbers.fold(10, (curr, next) => curr + next);
print(sum2);
// 36
```

## every()

用于判断数组中的每一项是否均达到了某个条件



```dart
var isAgeOver20 = students.every((student) => student["age"] > 20);
print(isAgeOver20);
// false

var isAgeOver15 = students.every((student) => student["age"] > 15);
print(isAgeOver15);
// true
```

## where(), firstWhere(), singleWhere()

- `where()` 返回数组中满足给定条件的元素集合
- `firstWhere()` 返回数组中满足给定条件的第一个元素
- `singleWhere()` 返回数组中满足给定条件的唯一一个元素，若有多个元素满足条件会抛出异常



```jsx
var ageOver16 = students.where((student) => student["age"] > 16);
print(ageOver16.toList());
// [{name: jack, age: 18}, {name: lucy, age: 20}]

var ageFirstOver16 = students.firstWhere((student) => student["age"] > 16, orElse: () => null);
print(ageFirstOver16);
// {name: jack, age: 18}

var ageUnder20 = students.singleWhere((student) => student["age"] < 16, orElse: () => null);
print(ageUnder20);
// {}
```

## take(), skip()

- `take(n)` 从数组里取 n 个元素
- `skip(n)` 跳过数组中的 n 个元素



```dart
List arr = [1, 3, 5, 2, 7, 9];

print(arr.take(3).toList());
// [1, 3, 5]
print(arr.skip(4).toList());
// [7, 9]
print(arr.take(3).skip(2).take(1).toList());
// [5]
```

## List.from()

克隆一个数组



```dart
List arr = [1, 3, 5, 2, 7, 9];

var clonedArr = List.from(arr);
print(clonedArr);
// [1, 3, 5, 2, 7, 9]
```

## expand()



```dart
var arr1 = [[2, 5], [7], [11, 12]];
var flattened = arr1.expand((item) => item).toList();
print(flattened);
// [2, 5, 7, 11, 12]

var arr2 = [2, 5, 8];
var computed = arr2.expand((item) => [item * 8]).toList();
print(computed);
// [16, 40, 64]

// 当对每一项进行计算时类似于 map()
var computed2 = arr2.map((item) => item * 8).toList();
print(computed2);
// [16, 40, 64]
```

## add(), addAll()

- `add()` 向数组中添加一个元素
- `addAll()` 向数组中添加另一个数组的所有元素



```dart
var arr1 = [1, 3, 5, 9, 2, 1];

arr1.add(10);
print(arr1);
// [1, 3, 5, 9, 2, 1, 10]
arr1.addAll([15, 21]);
print(arr1);
// [1, 3, 5, 9, 2, 1, 10, 15, 21]
```

