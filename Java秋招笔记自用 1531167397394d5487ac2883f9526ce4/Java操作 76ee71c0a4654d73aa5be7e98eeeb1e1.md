# Java操作

# JavaSE

# 集合与数组

## 类型转换

### HashSet转ArrayList

```java
Set<Integer> s = new HashSet<>();
s.add(12);

List<Integer> l = new ArrayList<>(s);
```

### int[]与List<Integer>相互转换

```java
// int[] -> list
Arrays.stream(nums).boxed().collect(Collectors.toList());
// list -> int[]
list.stream().mapToInt(Integer::parseInt).toArray();
```

## 数组拷贝

### System.arraycopy()

使用System.arraycopy做数组拷贝时，对于数组引用来说是深拷贝，对于部分类型（基本数据类型，包装类，String）的数组元素是深拷贝，其他都是浅拷贝。

```java
System.arraycopy(src, srcpos, tgt, tgtpos, len);
```

### Arrays.copyOfRange()

```java
Arrays.copyOfRange(src, fromIdx, toIdx);
```

## 数组求值

### 数组最大值

```java
Arrays.stream(arr).max().getAsInt();
```

# 字符串

## 字符串与字符数组相互转化

```java
// 字符串->字符数组
char[] c = s.toCharArray();

// 字符数组->字符串
String s = new String(c);
```

## StringBuilder构建字符串

```java
StringBuilder sb = new StringBuilder();

sb.append(c[1]);
sb.append("abc");

String s = sb.toString();
```

## 字符串的切割与合并

```java
// 切割
String[] newString = s.split(",");
// 正则表达式
// "\\s" space
// "+" 匹配一个或多个
// "*" 匹配零个或多个
// "?" 匹配零个或一个

// 合并
s = s1 + s2;
s = s1.concat(s2);

String[] sArray = {"123", "456"};
String.join(" ", sArray);
```

## 取出首尾空白字符

```java
String trimmed = s.trim();
```

## 子串

```java
// 获取子串
s.substring(0, 2); // 半开半闭
// 子串替换
s.replaceAll(" ", "abc");
// 是否包含子串
s.contains("abc");
// 获取指定字符或子串在字符串中首次出现的位置
s.indexOf();
```