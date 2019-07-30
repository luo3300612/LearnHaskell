# Haskell
参考自[这里](http://learnyouahaskell.com/)

## Basic
平方 ^

否定 not

不等于 /=

静态类型，type inference
## Starting Out
Haskell文件后缀 .hs

常用编译器 hgc

交互模式，命令行输入hgci

### 加载.hs文件中的函数

比如有一个文件baby.hs
```Haskell
doubleMe x = x + x
```
在交互模式下输入
```Haskell
>:l baby
>doubleMe 2
4
```
即可使用加载的函数

### if-else
Haskell中的if必须要有else与之匹配
```Haskell
doubleSmallNumber' x = (if x > 100 then x else x*2) + 1  
```
注意上面`是合法的名称字符，且最后的+1作用在所有之前的结果中

大写字母不可以作为函数名的开头字母

### list
Haskell中的list只能存放同类型的元素

合并list
```Haskell
a = [1,2,3]
b = [4,5,6]
a ++ b
```
使用++的时候，Haskell内部会遍历左边的那个列表，然后把右边的加在左边的后面

将一个元素加在列表前面的方法是:
```Haskell
a = [1,2,3,4,5]
5:a
```

按索引取值
```Haskell
a = [1,2,3]
a !! 1
2
```

对比列表，可以用比较运算符来对比列表，效果就是逐个元素比较

其他操作
* head，返回首元素
* tail，返回除首元素的部分
* last，返回尾元素
* init，返回除尾元素的部分
* length，返回列表长度
* null，检查列表是否为空
* reverse
* take，take num list，返回列表前num个元素
* drop，drop num list，丢弃列表前num个元素
* maximum,minimum,sum,product
* elem，列表中是否有某个元素，通常以中缀形式call，1 `elem` [1,2,3]


### range
```Haskell
[1..20]
['a'..'z']
['K'..'Z']
[3,6..20]
```

不要用在range中使用浮点数
```Haskell
[0.1, 0.3 .. 1]  
[0.1,0.3,0.5,0.7,0.8999999999999999,1.0999999999999999] 
```

如果想要12到12×24的间隔为12的列表，可以
```Haskell
[13,26..24*13]
```
也可以
```Haskell
take 24 [13,26..]
```
因为Haskell是lazy的，所以它不会先生成整个序列

* cycle，将一个列表变成一个无限循环的列表
* repeat，将一个元素变成一个无限长的列表
* replicate num1 num2，重复num1遍num2的列表

### 列表推导（list comprehension）
```Haskell
[x*2 | x<-[1..10]]
[x*2 | x<-[1..10],x*2>=12]
[x | x<-[50..100],x `mod` 7 == 3]
[ x | x <- [10..20], x /= 13, x /= 15, x /= 19]
boomBangs xs = [ if x < 10 then "BOOM!" else "BANG!" | x <- xs, odd x]   
```

odd和even可以直接判断奇偶

除了使用多个predicate(大概是条件)来filter列表，还可以使用多个列表
```Haskell
[ x*y | x <- [2,5,10], y <- [8,10,11]]  
```

我们可以写一个自己的length函数
```Haskell
length' xs = sum [1 | _ <- xs]   
```

更复杂的推导
```Haskell
let xxs = [[1,3,5,2,3,1,2,4,5],[1,2,3,4,5,6,7,8,9],[1,2,4,2,1,6,3,1,3,2,3,6]]  
[ [ x | x <- xs, even x ] | xs <- xxs]  
[[2,2,4],[2,4,6,8],[2,4,2,6,2,6]]  
```

### Tuples
和列表不一样，Tuple可以包括不同类型的元素，一般用来存放已知长度的变量

不同长度的Tuple是不同的类型

* fst 返回tuple的第一个元素
* snd 返回tuple的第二个元素
* zip 产生一个tuple的列表，和python一样

因为Haskell是lazy的，且zip的结果是取决于短的那个列表，所以，可以这样写
```Haskell
zip [1..] ["apple", "orange", "cherry", "mango"]  
```

## Types and Typeclasses
Haskell has a static type system. The type of every expression is known at compile time

与Java或Pascal不同，Haskell可以进行类型推导（type inference）

查看类型
```Haskell
:t 'a'
```

函数也有类型，为函数写上类型是一个好习惯

类型均以大写字母开头表示
* Int，-2147483648~2147483647
* Integer, no bound
* Float
* Double
* Bool
* Char

==等运算符实际上是函数，所以也有类型
```Haskell
ghci> :t (==)  
(==) :: (Eq a) => a -> a -> Bool  
```
前面的括号里是class constraint，限制了字母的类型

一些基本的Typeclasses
* Eq，为了检验相等的类
* Ord，有ordering的类
* Show，Show的成员可以打印成字符串
  ```Haskell
  ghci> show 3  
  "3"  
  ghci> show 5.334  
  "5.334"  
  ghci> show True  
  "True"  
  ```
* Read与Show恰恰相反，read接受一个字符串然后返回一个Read的类型
  ```Haskell
  ghci> read "True" || False  
  True  
  ghci> read "8.2" + 3.8  
  12.0  
  ghci> read "5" - 2  
  3  
  ghci> read "[1,2,3,4]" ++ [3]  
  [1,2,3,4,3]  
  ```
  可以通过加上类型来声明read的是什么类型来避免歧义
  ```Haskell
  ghci> read "5" :: Int  
  5  
  ghci> read "5" :: Float  
  5.0  
  ghci> (read "5" :: Float) * 4  
  20.0  
  ghci> read "[1,2,3,4]" :: [Int]  
  [1,2,3,4]  
  ghci> read "(3, 'a')" :: (Int, Char)  
  (3, 'a')  
  ```
* Enum的成员是有序的序列类型
* Bounded的成员有上界和下界
  ```Haskell
  ghci> minBound :: Int  
  -2147483648  
  ghci> maxBound :: Char  
  '\1114111'  
  ghci> maxBound :: Bool  
  True  
  ghci> minBound :: Bool  
  False  
  ```
* Num的成员必须act like numbers
  ```Haskell
  ghci> 20 :: Int  
  20  
  ghci> 20 :: Integer  
  20  
  ghci> 20 :: Float  
  20.0  
  ghci> 20 :: Double  
  20.0  
  ```

一个有用的函数`fromIntegral`，它可以将整数变成更general的数，比如
```Haskell
fromIntegral (length [1,2,3,4]) + 3.2
```
如果不这样就无法进行相加