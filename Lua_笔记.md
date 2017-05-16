# Lua 笔记

## 1 基础

### 1.1 数据类型

![1.1-1](D:\note\Lua学习笔记\1.1-1.png)

#### 1.1.1 string

可以用 2 个方括号 "[[]]" 来表示"一块"字符串

```lua
html = [[
<html>
<head></head>
<body>
    <a href="http://www.w3cschool.cc/">w3cschool菜鸟教程</a>
</body>
</html>
]]
print(html)
```

```lua
<html>
<head></head>
<body>
    <a href="http://www.w3cschool.cc/">w3cschool菜鸟教程</a>
</body>
</html>
```

字符连接使用 ..

```lua
> print("a" .. 'b')
ab
> print(157 .. 428)
157428
> 
```

- 在数字后面写..时，必须加上空格，否则会解释出错

  ```lua
  print(1223.. "third")
  ```

  ```lua
  malformed number near '1223..'
  ```

使用 # 来计算字符串的长度，放在字符串前面

```lua
> len = "www.w3cschool.cc"
> print(#len)
16
> print(#"www.w3cschool.cc")
16
> 
```

#### 1.1.2 table

不同于其他语言的数组把 0 作为数组的初始索引，在 Lua 里表的默认初始索引一般以 1 开始

```lua
-- table_test2.lua 脚本文件
local tbl = {"apple", "pear", "orange", "grape"}
for key, val in pairs(tbl) do
    print("Key", key)
end
```

```lua
$ lua table_test2.lua 
Key	1
Key	2
Key	3
Key	4
```

table 不会固定长度大小，有新数据添加时 table 长度会自动增长，没初始的 table 都是 nil。

```lua
-- table_test3.lua 脚本文件
a3 = {}
for i = 1, 10 do
    a3[i] = i
end
a3["key"] = "val"
print(a3["key"])
print(a3["none"])
```

```lua
$ lua table_test3.lua 
val
nil
```



#### 1.1.3 function

函数被看做“第一类值(First-Class Value)”，可以存放在变量里

function可以以匿名函数(anonymous function)的方式通过参数传递

```lua
-- function_test2.lua 脚本文件
function anonymous(tab, fun)
    for k, v in pairs(tab) do
        print(fun(k, v))
    end
end
tab = { key1 = "val1", key2 = "val2" }
anonymous(tab, function(key, val)
    return key .. " = " .. val
end)
```

```lua
$ lua function_test2.lua 
key1 = val1
key2 = val2
```

#### 1.1.4 thread（线程）

在 Lua 里，最主要的线程是协同程序（coroutine）。它跟线程（thread）差不多，拥有自己独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西

线程跟协程的区别：线程可以同时多个运行，而协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起（suspend）时才会暂停

#### 1.1.5 userdata（自定义类型）

userdata 是一种用户自定义数据，用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 **struct 和 指针**）存储到 Lua 变量中调用



### 1.2 Lua变量

- 三种类型变量：全局、局部、表中的域
- 除非使用local显示声明，否则都是全局
- 局部变量的作用域为声明位置到所在语句块结束
  - 局部变量避免命名冲突；访问速度比全局变量更快
- 变量的默认值为nil

### 1.3 赋值语句

可以对多个变量同时赋值，用逗号隔开，右边的值依次赋值给左边的变量

赋值语句会先计算右边所有值再进行赋值操作，所以经常使用赋值语句交换变量的值

```lua
x, y = y, x                     -- swap 'x' for 'y'
a[i], a[j] = a[j], a[i]         -- swap 'a[i]' for 'a[j]'
```

当变量个数和值的个数不一致时，Lua会一直以变量个数为基础采取以下策略

> ```
> a. 变量个数 > 值的个数             按变量个数补足nil
> b. 变量个数 < 值的个数             多余的值会被忽略
> ```

也可以将函数返回值赋值给变量，f()返回两个值

```lua
a, b = f()
```

### 1.4 索引访问数组元素

对 table 的索引使用方括号 []，Lua 也提供了 . 操作

```lua
t[i]
t.i                 -- 当索引为字符串类型时的一种简化写法
gettable_event(t,i) -- 采用索引访问本质上是一个类似这样的函数调用
```

```lua
> site = {}
> site["key"] = "www.w3cschool.cc"
> print(site["key"])
www.w3cschool.cc
> print(site.key)
www.w3cschool.cc
```

### 1.5 流程控制

Lua中0为true

```lua
--[ 0 为 true ]
if(0)
then
    print("0 为 true")
end
```

```lua
0 为 true
```

### 1.6 函数

可以将函数作为参数传递给函数

```lua
myprint = function(param)
   print("这是打印函数 -   ##",param,"##")
end

function add(num1,num2,functionPrint)
   result = num1 + num2
   -- 调用传递的函数参数
   functionPrint(result)
end
myprint(10)
-- myprint 函数作为参数传递
add(2,5,myprint)
```

```lua
这是打印函数 -   ##	10	##
这是打印函数 -   ##	7	##
```

#### 1.6.1 多返回值

Lua函数可以返回多个结果值，比如string.find，其返回匹配串"开始和结束的下标"（如果不存在匹配串返回nil）

```lua
> s, e = string.find("www.runoob.com", "runoob") 
> print(s, e)
5	10
```

Lua函数中，在return后列出要返回的值得列表即可返回多值

```lua
function maximum (a)
    local mi = 1             -- 最大值索引
    local m = a[mi]          -- 最大值
    for i,val in ipairs(a) do
       if val > m then
           mi = i
           m = val
       end
    end
    return m, mi
end

print(maximum({8,10,23,12,5}))
```

#### 1.6.2 可变参数

Lua函数可以接受可变数目的参数，和C语言类似在函数参数列表中使用**三点（...)** 表示函数有可变的参数

Lua将函数的参数放在一个叫arg的表中，**#arg** 表示传入参数的个数

计算几个数的平均值

```lua
function average(...)
   result = 0
   local arg={...}
   for i,v in ipairs(arg) do
      result = result + v
   end
   print("总共传入 " .. #arg .. " 个数")
   return result/#arg
end

print("平均值为",average(10,5,3,4,5,6))
```

```lua
总共传入 6 个数
平均值为	5.5
```

### 1.7 运算符

对于/运算符，Lua默认不会只保留整数，而是保留小数点后13位，并且四舍五入

```lua
a,b = 25,15

print(a/b);
```

```lua
1.6666666667
```

优先级

```lua
^
not    - (unary)
*      /
+      -
..
<      >      <=     >=     ~=     ==
and
or
```

除了^和..外所有的二元运算符都是左连接的

```lua
a+i < b/2+1          <-->       (a+i) < ((b/2)+1)
5+x^2*8              <-->       5+((x^2)*8)
a < y and y <= z     <-->       (a < y) and (y <= z)
-x^2                 <-->       -(x^2)
x^y^z                <-->       x^(y^z)
```

### 1.8 字符串

Lua 语言中字符串可以使用以下三种方式来表示：

- 单引号间的一串字符
- 双引号间的一串字符
- [[和]]间的一串字符

```lua
string1 = "Lua"
print("\"字符串 1 是\"",string1)
string2 = 'runoob.com'
print("字符串 2 是",string2)

string3 = [["Lua 教程"]]
print("字符串 3 是",string3)
```

### 1.9 数组

在 Lua 索引值是以 1 为起始，但你也可以指定 0 开始

除此外我们还可以以负数为数组索引值

```lua
array = {}

for i= -2, 2 do
   array[i] = i *2
end

for i = -2,2 do
   print(array[i])
end
```

```lua
-4
-2
0
2
4
```

