---
title: JavaScript 基础 (四)
date: 2022-03-18
categories: Programming
tags: [JavaScript]
---

# 对象

在js中，对象是一组无序的相关属性和方法的集合。所有的事物都是对象，如数组、字符串、数值、函数等。

对象是由属性和方法组成的：

- 属性：事物的特征，在对象中用属性来表示，常用名词
- 方法：事物的行为，在对象中用方法来表示，常用动词

### 创建对象

三种方式：

1. 利用字面量创建对象：`var obj = {};`
   
    ```js
    var obj = {
      name: 'Leon',
      age: 19,
      sexual: 'boy',
      sayHi: function() {
        console.log('Hi~');
      }
    }
    ```
    
    - 采用键值对的形式
    - 多个属性或者方法之间用逗号隔开，结尾用分号
    - 方法是一个匿名函数
2. 利用new Object创建对象
   
    ```js
    var obj = new Object();
      obj.name = 'Leon';
      obj.age = 19;
      obj.sexual = 'boy';
      obj.sayHi = function() {
        console.log('Hi~');
      }
    ```
    
    - 采用等号赋值的形式添加对象的属性和方法
    - 多个属性和方法之间用分号隔开，结尾也用分号
3. 利用构造函数创建对象：对象实例化
    - 把对象的一些相同的属性和方法抽象出来封装在函数内部
    
    ```js
    // 创建构造函数
    function Students(myname, myage, mysexual) {
      this.name = myname;
      this.age = myage;
      this.sexual = mysexual;
      this.sayHi = function(str) {
        console.log(str);
      }
    }
    
    // 调用构造函数创建对象
    var somebody = new Students('Leon', '18', 'boy');
    somebody.sayHi('Hi~');
    ```
    
    - 构造函数的名字首字母大写
    - 调用构造函数`new 构造函数名();`
    - 和函数一样有形参实参
    - 构造函数不需要return就可以返回结果
    - `new`在执行时会做的4件事情：
      
        ![Untitled](https://p.ipic.vip/4hi4g2.png)
        

### 使用对象

调用对象的属性：

- `对象名.属性名`
- `对象名['属性名']`

调用对象的方法：

- `对象.方法名()`

### 遍历对象

`for...in`语句用于对数组或对象的属性行进循环操作

```js
for(var 变量名 in 对象名) {
  console.log(变量名);  // 得到的是对象里面的属性名
  console.log(对象名[变量名])；  // 得到的是属性值
}
```

- 在`for...in`里面的变量名一般习惯写 k 或者 key

# 内置对象

js对象分为自定义对象、内置对象、浏览器对象

## Math数学对象

- `Math.PI` 圆周率
- `Math.Max()` 求最大值
- `Math.Min()` 求最小值
- `Math.abs()` 求绝对值
- `Math.floor()` 向下取整
- `Math.ceil()` 向上取整
- `Math.round()` 四舍五入取整
    - 但 .5 特殊，只往大取整，`Math.round(-1.5) = -1`
- `Math.random()` 随机数方法
    - 返回一个随机小数，在区间 [ 0 , 1 ) 中
    - 没有参数
    - 若要得到一个两个数之间的随机整数，且包含这两个整数：
      
        ```js
        function getRandom(min, max) {
          return Math.floor(Math.random() * (max - min + 1)) + min;
        }
        
        console.log(getRandom(1, 10));
        ```
        

## Date()日期对象

Date()日期对象是一个构造函数，必须实例化，通过 new 来调用创建日期对象

```js
var date = new Date();
```

- 如果没有参数，则返回系统的当前时间
- 如果有参数，则返回参数的时间，两种形式：
    - `Date(2018, 1, 14)` （月份为0-11月）
    - `Date('2018-1-14 20:30:59')`
- 格式化日期
    - `.getFullYear()` 返回当前日期的年份
    - `.getMonth()` 返回当前日期的月份（0-11月，一般应+1）
    - `.getDate()` 返回当前日期是几号
    - `.getDay()` 返回当前日期是周几（返回值为数字，一般用数组转换为汉字。且一周从周日开始，周日为0）
    - `.getHours()` 返回当前的小时数（24小时模式）
    - `.getMinutes()` 返回当前的分钟数（小于10的个位数前面应拼接一个0）
    - `.getSeconds()` 返回当前的秒数（小于10的个位数前面应拼接一个0）
- 毫秒数：获得当前日期距离1970年1月1号总的毫秒数（时间戳）
    - `.valueOf()`
    - `.getTime()`
    - `+new Date()` 最常用
    - `Date.now()` H5新增

## 数组对象

创建数组对象可以用数组字面量或`new Array()` 

- 若只填写一个参数 n ，则表示创建了一个具有 n 个空元素的数组
- 若填写多个参数，则表示创建了一个数组，数组中包含多个元素
- 检测对象是否为数组
    - `instanceof` 运算符，返回值为true / false
      
        ```js
        var arr = [54, 34, 65];
        console.log(arr instanseof Array);  // true
        ```
        
    - `Array.isArray(参数)` 方法，返回值为true / false（H5新增）
      
        ```js
        var arr = [54, 34, 65];
        console.log(Array.isArray(arr));  // true
        ```
    
- 添加数组元素的方法：
    - `.push()` 在数组的末尾，添加一个或多个元素
        - 参数直接写要添加的数组元素
        - 返回值为新数组的长度
    - `.unshift()` 在数组的头部，添加一个或多个元素
        - 参数直接写要添加的数组元素
        - 返回值为新数组的长度
- 删除数组元素的方法：
    - `.pop()` 删除数组的最后一个元素
        - 无参数
        - 返回值为删除的那个元素的值
    - `.shift()` 删除数组的第一个元素
        - 无参数
        - 返回值为删除的那个元素的值
    - `.splice(index, number)` 根据索引号删除数组的元素
        - index：起始索引号
        - number：要删除的元素的个数
- 数组排序的方法
    - `.reverse()` 翻转数组
    - `.sort()` 冒泡排序
      
        ```js
        var arr = [2, 65, 33, 7, 54];
        
        arr.sort(function(a, b) {
          return a - b;  // 升序[2, 7, 33, 54, 65]
        });
        
        arr.sort(function(a, b) {
          return b - a;  // 降序[65, 54, 33, 7, 2]
        });
        ```
    
- 数组索引的方法
    - `.indexOf()` 返回参数中的元素在数组中的索引号（正序查找）
        - 数组索引号从0开始
        - 只返回满足条件的第一个索引号（正序）
        - 若在数组中找不到该元素，则返回 -1
    - `.lastIndexOf()` 返回参数中元素在数组中的索引号（倒序查找）
        - 数组索引号从0开始
        - 只返回满足条件的第一个索引号（倒序）
        - 若在数组中找不到该元素，则返回 -1
- 数组转换为字符串
    - `.toString()` 将数组转换为字符串
    - `.join()` 将数组转换为字符串，并在每个元素之间添加分隔符参数（默认为逗号）

## 字符串对象

- 基本包装类型：把简单数据类型包装为复杂数据类型
  
    ```js
    var str = 'leon';
    // 等价于
    var temp = new String('leon');
    var str = temp;
    temp = null;
    ```
    
    - 这样基本数据类型就有了属性和方法
      
        ```js
        console.log(str.length)  // 4
        ```
    
- 字符串的不可变：字符串的值不可变
    - 虽然看上去字符串的值可以改变，但实际是其指向的内存地址发生改变，在内存中开辟了新的空间存储新的字符串
    - 尽量不要大量的拼接字符串
- 根据字符返回位置
    - `.indexOf()` 返回参数中的字符在字符串中的位置（正序查找）
        - 位置从0开始
        - 只返回满足条件的第一个位置（正序）
        - 若在字符串中找不到该字符，则返回 -1
        - 可选参数：查找的起始位置（将略过起始位置前面的字符）
    - `.lastIndexOf()` 返回参数中字符在字符串中的位置（倒序查找）
        - 位置从0开始
        - 只返回满足条件的第一个位置（倒序）
        - 若在字符串中找不到该字符，则返回 -1
        - 可选参数：查找的起始位置（将略过起始位置前面的字符）
- 根据位置返回字符
    - `.charAt()` 返回参数中位置对应的字符
    - 结合循环可以实现遍历字符串中的所有字符
    - `.charCodeAt()` 返回参数中位置对应的字符的ASCII码，用于判断用户按下的键位
    - `变量名[位置]` 返回字符串指定位置相应的字符，H5新增
- 拼接及截取字符串
    - `.concat()` 拼接字符串，将参数里的字符串依次拼接在对象字符串的后面，等效于 +
    - `.substr(起始位置, 截取几个字符)` 截取字符串，将截取的字符串返回
- `.replace(被替换的字符, 替换为的字符)` 替换字符串中的某几个字符
    - 只会替换满足条件的第一个字符
    - 可以结合循环实现替换字符串中所有满足的字符
- `.split(分隔符)` 将字符串转换为数组
    - 需要参数输入字符串中每个元素之间的分隔符
    - 和`.join()`将数组转换为字符串的功能相反
- `.toUpperCase()` 转换为大写
- `.toLowerCase()` 转换为小写

# 简单/复杂数据类型

简单数据类型又叫基本数据类型或值类型，复杂数据类型又叫引用类型

- 值类型：在变量存储的时候，存储的是值本身
    - string、number、boolean、undefined、null
    - null比较特殊，类型返回的是object
- 引用类型：在变量存储的时候，存储的仅仅是内存地址
    - 通过new关键字创建的，Object、Array、Date

---

- 简单数据类型传参：函数的形参实际上是一个临时变量，传入实参就是把实参的值在内存的堆中复制一份赋给形参的变量。函数内修改形参的值不会影响外部实参的值
- 复杂数据类型传参：函数的形参也是可以看做是一个变量，当把引用类型传给形参时，实际上是把变量在内存中栈的地址复制给了形参，实参和形参中保存的都是同一个地址，所以操作的是同一个对象