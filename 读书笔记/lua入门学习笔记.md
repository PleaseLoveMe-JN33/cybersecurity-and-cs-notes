# Lua语言入门学习笔记

> 个人兴趣，买了一款叫replicube的游戏，听说这个游戏是用lua玩的，索性来学习一下，解解闷

魔兽世界和愤怒的小鸟都使用过lua

速度最快，最轻量级的脚本语言之一

易于学习，可用于游戏，web应用程序，自动化，人工智能

lua葡萄牙语意为月亮，1993年在巴西被创造出来

为那些需要速度、灵活性和简洁性的开发者设计的语言



第一个lua程序

```
print('hello world !')
```

> 好眼熟有没有，跟python一模一样



**local是lua的关键字，专门用来定义局部变量**

值得注意的是，python和lua关于局部变量和全局变量的相关概念是不一样的，这点很重要

- lua

默认创建全局变量

```
username = "John"       -- 全局变量
local age = 22          -- 局部变量

function greet()
    print(username)
    print(age)
end

greet()
```

输出：

```
John
22
```

**在函数内部，如果没有使用local声明变量，那么赋值通常会创建或者修改全局变量**

来看示例：

```
username = "John"

function changeName()
    username = "Tom"
end

changeName()

print(username)
```

输出：

```
Tom
```

**函数直接修改了全局变量**



有这样一段代码：

```
local username = 'John'
print('hello '..username..' welcome to Lua')
```

输出：

```
hello John welcome to Lua
```

其中这个 **..** 有没有感觉眼熟？很容易联想到python里的 **+ **是不是，感觉都是用来连接字符串的

但是也有区别哦，python里的 **+ **除了拼接字符串，还可以做加法，lua里的 **..** 专门用于字符串拼接



```
local username = 'John'
print('hello '..username..' welcome to Lua\nI miss you so much')
```

输出：

```
hello John welcome to Lua
I miss you so much
```

| 转义字符 |       含义        |
| :------: | :---------------: |
|    \n    |       换行        |
|    \t    | 水平制表符（Tab） |
|  ` \\ `  | 输出一个反斜杠 \  |
|   \ '    |    输出单引号     |
|    \"    |    输出双引号     |

