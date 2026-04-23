# 第 10 讲：还原主义

- 纯性
- 惰性
- Haskell 求值


## 10.1 惰性与纯性

第 1 部分开头提到了 Haskell 的关键特性：纯性和惰性。让我们仔细看看。

Haskell 是一种“纯”函数式语言。这意味着给定相同的 `x` 和 `y`，值 `f x y` 总是相同的。换句话说，`x` 和 `y` 的值唯一确定 `f x y` 的值。此属性也称为“引用透明度”。

纯性还意味着没有副作用：你不能让 `f x y` 的计算从用户那里读取一行输入，因为不同调用可能读取到不同内容，从而影响返回值，破坏引用透明度！显然，要真正做成事情，你需要副作用。稍后我们会回到 Haskell 如何处理副作用。

Haskell 是一种“惰性”语言。这意味着如果某个值不需要，就不会计算它。下面这个示例最能说明这一点。考虑这两个函数：

```haskell
f x = f x   -- infinite recursion
g x y = x
```

由于无限递归，计算 `f 1` 无法停止。然而，这有效：

```haskell
g 2 (f 1)  ==>  2
```

惰性之所以可行，是因为 Haskell 是纯函数式的。重要的是函数的结果，而不是副作用。因此，如果某个结果没有被使用，就可以不计算它，而且不会改变程序含义。有时这会让原本不会停止的程序变成会停止的程序，但惰性不会破坏正常工作的 Haskell 程序。

如果你对此背后的理论感兴趣，请查看 [Church-Rosser 定理](https://en.wikipedia.org/wiki/Church%E2%80%93Rosser_theorem) 或 Haskell Wiki 文章 [惰性与非严格](https://wiki.haskell.org/Lazy_vs._non-strict)。


## 10.2 等式推理

引用透明度意味着表达式在相同输入下始终返回相同值。这是一个非常强大的属性，我们可以利用它来“推理程序”。

在 C 风格语言中，我们可能编写一个函数，它不一定总是为相同的参数返回相同的值：

    int c = 0;
    int funny(int x) {
      return x + c++;
    }

表达式 `c++` 递增 `c` 的值，并返回 `c` 的旧值。下次计算时，`c` 已经增加了。这意味着根据 `c` 的当前值，`funny(0)` 可能返回 `0`、`1`、`2` 或其他值。 （如果 `c` 溢出，它甚至可能返回负值！）

有时这种带副作用的行为有用，但有时能够轻松推理代码更重要。纯函数的好处是可以用数学技术分析它们。有时把数学应用到函数上，甚至能发现原本想不到的简化或优化。

考虑以下表达式：

```haskell
map (+1) . reverse . map (-1)
```

这个表达式可以简化为 `reverse`。我们首先建立一些有用的事实（或*引理*）：

1.`map id === id`
2.`map f . map g === map (f.g)`
3.`reverse . map f === map f . reverse`

我们需要的第四个事实如下：

4.`(+1) . (-1) === id`

我们可以通过推理 `(+1) . (-1)` 对于任意输入 `x` 的行为来证明事实 4：

```haskell
((+1) . (-1)) x === ((+1) ((-1) x))
                === ((+1) (x - 1))
                === (x - 1) + 1
                === x
                === id x
```

因为我们没有对 `x` 做出任何假设，所以我们可以得出结论，上述方程链适用于*每个*`x`。因此，

```haskell
(+1) . (-1) === id
```

对于那些熟悉“归纳证明”技术的人来说，证明前三个事实也是一个有趣的练习。不过，本课程不讨论归纳证明，所以如果你不了解归纳也不必担心。

现在，从事实 1-4 可以看出

```haskell
    map (+1) . reverse . map (-1)
=== map (+1) . (reverse . map (-1))    -- By associativity of (.)
=== map (+1) . (map (-1) . reverse)    -- By fact 3
=== (map (+1) . map (-1)) . reverse    -- By associativity of (.)
=== map ((+1) . (-1)) . reverse        -- By fact 2
=== map id . reverse                   -- By fact 4
=== id . reverse                       -- By fact 1
=== reverse                            -- 根据 id 的定义
```

本课程不会详细介绍有关程序的证明，但很高兴知道纯函数式编程与这样的分析非常兼容。


## 10.3 无限列表

一些涉及“无限列表”的示例最好地证明了惰性的好处。让我们从 `repeat 1` 开始，它生成 `1` 的无限列表。如果我们尝试告诉 GHCi 打印值 `repeat 1`，它将永远继续打印 `1`，直到我们使用 Control-C 中断它：

```haskell
Prelude> repeat 1
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
^C
```

然而，由于惰性，我们可以使用无限列表，并写出能够结束的计算。关键是只使用无限列表中有限数量的元素。以下是一些示例：

```haskell
Prelude> take 10 $ repeat 1
[1,1,1,1,1,1,1,1,1,1]
Prelude> take 20 $ repeat 1
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
Prelude> repeat 1 !! 13337
1
```

有时确实会需要一个只重复同一个元素的无限列表，但这还不算特别有意思。接下来看看一些更有用的无限列表。你可以使用 `[n..]` 语法生成从 `n` 开始的无限数字列表：

```haskell
Prelude> take 20 [0..]
[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19]
Prelude> take 10 . map (2^) $ [0..]
[1,2,4,8,16,32,64,128,256,512]
```

函数 `cycle` 一遍又一遍地重复给定列表中的元素。它在处理旋转或循环时很有用。

```haskell
Prelude> take 21 $ cycle "asdf"
"asdfasdfasdfasdfasdfa"
Prelude> take 4 . map (take 4) . tails $ cycle "asdf"
["asdf","sdfa","dfas","fasd"]
```

### 10.3.1 示例：交易编号

作为 `cycle` 如何有用的更具体示例，让我们看一下计算芬兰银行转账交易数字的校验位 ([viitenumero](https://fi.wikipedia.org/wiki/Tilisiirto#Viitenumero))。交易号由任意数量的数字组成，后跟一个校验位。通过将数字（从右到左）与数字 7、3、1、7、3、1 等相乘并对结果求和来检查校验位。如果和*加上校验位*的结果能被10整除，则该数字有效。

这是一个具体的示例。 `116127` 是有效的交易号。计算过程如下：

    digits:       1  1  6  1  2
                  *  *  *  *  *
    multipliers:  3  7  1  3  7
                  3+ 7+ 6+ 3+14 = 33
    check digit is 7, 33+7=40 is divisible by 10, valid

这是交易数字检查器的 Haskell 代码。请注意我们如何使用无限列表 `cycle [7,3,1]` 作为乘法器。

```haskell
viitenumeroCheck :: [Int] -> Bool
viitenumeroCheck allDigits = mod (checksum+checkDigit) 10 == 0
  where (checkDigit:digits) = reverse allDigits
        multipliers = cycle [7,3,1]
        checksum = sum $ zipWith (*) multipliers digits
```

```haskell
viitenumeroCheck [1,1,6,1,2,7]  ==> True
viitenumeroCheck [1,1,6,1,2,8]  ==> False
```

### 10.3.2 示例：求幂

最后，这里介绍如何求出大于 100 的 3 的第一个幂。

```haskell
Prelude> head . filter (>100) $ map (3^) [0..]
243
```

让我们逐步了解一下它是如何工作的。请注意映射和过滤器如何根据需要延迟处理列表，一次处理一个元素。这类似于 Python 或 Java 等语言中*生成器*或*迭代器*的工作方式。

```haskell
    head (filter (>100) (map (3^) [0..]))
==> head (filter (>100) (map (3^) (0:[1..])))   -- 对惰性列表的第一个元素求值
==> head (filter (>100) (1 : map (3^) [1..]))   -- map 处理该元素
==> head (filter (>100) (map (3^) [1..]))       -- filter 丢弃该元素
==> head (filter (>100) (map (3^) (1:[2..])))   -- 对惰性列表的第二个元素求值
==> head (filter (>100) (3 : map (3^) [2..]))   -- map 处理该元素
==> head (filter (>100) (map (3^) [2..]))       -- filter 丢弃该元素
-- 现在步子迈大一点
==> head (filter (>100) (9 : map (3^) [3..]))   -- map 处理，filter 将丢弃
==> head (filter (>100) (27 : map (3^) [4..]))  -- map 处理，filter 将丢弃
==> head (filter (>100) (81 : map (3^) [5..]))  -- map 处理，filter 将丢弃
==> head (filter (>100) (243 : map (3^) [6..])) -- map 处理
==> head (243 : filter (>100) (map (3^) [6..])) -- filter 让该值通过
==> 243                                         -- head 返回结果
```


## 10.4 Haskell 是如何工作的？

现在你可能会觉得惰性有点神奇，也可能想知道它是如何实现的。Haskell 求值其实非常简单，只是和你可能习惯的方式不同。下面深入看看。

在大多数其他编程语言（如 Java、C 或 Python）中，求值是由内而外进行的。函数的参数在函数之前计算。

Haskell 求值是从外到内，而不是从内到外。它会先应用表达式最外层函数的定义，而不预先计算任何参数。下面是玩具函数 `f` 和 `g` 的具体示例：

```haskell
g :: Int -> Int -> Int
g x y = y+1
f :: Int -> Int -> Int -> Int
f a b c = g (a*1000) c
```

由内而外（正常）求值：

```haskell
f 1 (1234*1234) 2
  -- 对 f 的参数求值
  ==> f 1 1522756 2
  -- 对 f 求值
  ==> g (1*1000) 2
  -- 对 g 的参数求值
  ==> g 1000 2
  -- 对 g 求值
  ==> 2+1
  ==> 3
```

Haskell 由外而内的求值：

```haskell
f 1 (1234*1234) 2
  -- 对 f 求值 without evaluating arguments
  ==> g (1*1000) 2
  -- 对 g 求值 without evaluating arguments
  ==> 2+1
  ==> 3
```

请注意未使用的计算 `1234*1234` 和 `1*1000` 未得到求值。这就是为什么惰性常常是有帮助的。

### 10.4.1 模式匹配驱动求值

让我们看一个更复杂的示例，其中包含模式匹配和更复杂的数据（列表）。正如我们将看到的，模式匹配以非常具体的方式驱动 Haskell 求值。这是我们将使用的一些函数。它们在前奏曲中很熟悉，但我会给它们简单的定义。

```haskell
not True = False
not False = True
map f [] = []
map f (x:xs) = f x : map f xs
length [] = 0
length (x:xs) = 1+length xs
```

这是表达式的由内而外的求值：

```haskell
length (map not (True:False:[]))
  ==> length (not True : not False : [])  -- 对 map 调用求值
  ==> length (False:True:[])              -- 对 not 调用求值
  ==> 2
```

以下是 Haskell 中的求值过程。请注意，它并不是严格由外向内的，因为我们有时需要求值内部参数才能知道匹配哪个模式。

```haskell
length (map not (True:False:[]))
  -- 我们还不能对 length 求值，因为不知道该用 length 的哪个等式，
  -- 所以先查看 length 的参数。这里可以应用 map 的第二个等式。
  ==> length (not True : map not (False:[]))
  -- 现在 length 的参数里有可以模式匹配的 (:)，所以应用
  -- length 的第二个等式
  ==> 1 + length (map not (False:[]))
  -- 最外层函数现在是 +，但只有两个参数都是数字时它才能继续。
  -- 因此需要对 length 求值。为了选择等式，需要再次求值 length 的参数。
  -- 这里应用 map 的第二个等式
  ==> 1 + length (not False : map not ([]))
  -- 现在可以再次应用 length 的第二个等式。
  ==> 1 + (1 + length (map not []))
  -- 最外层的 + 需要一个数字才能求值。第二个 + 也需要一个数字。
  -- 我们需要再次对 length 求值，这意味着要为 length 选择一个等式，
  -- 也就意味着要对它的参数求值。这一次适用的是 map 的第一个等式。
  ==> 1 + (1 + length [])
  -- 现在可以应用 length 的第一个等式
  ==> 1 + (1 + 0)
  -- 最外层的 + 仍然不能求值，但内层的 + 可以
  ==> 1 + 1
  -- 最后对外层的 + 求值
  ==> 2
```

请注意，`not` 的任何调用根本没有被求值。

引入一些术语。我们说模式匹配会*强制*求值。Haskell 求值时，会把表达式求到“弱头范式 (WHNF)”。WHNF 基本上意味着*已经可以进行模式匹配的值*。如果一个表达式在*最外层*已经不能继续计算，就处于 WHNF。这意味着：

- 是常数，例如：`1`
- 在顶层有一个构造函数，例如：`False`、`Just (1+1)`、`0:filter f xs`
- 是一个函数，例如：`(\x -> 1+x)`

最值得注意的是，函数应用通常不在 WHNF 中。如果一个表达式由某个函数（不是构造函数）应用到一些参数组成，那么它还不在 WHNF。我们必须对它求值，才能得到可以模式匹配的东西。

在前面的示例中，我们无法为 `length (map not (False:[]))` 中的 `length` 选择方程。参数 `(map not ...)` 不在 WHNF 中，因此无法进行模式匹配。因此我们需要对其进行求值。当我们应用 `map` 的第二个方程时，我们得到 `length (not False : map not [])`，现在长度的参数在 WHNF 中，因为顶层有一个构造函数 `(:)`。如果我们从中缀转换为前缀表示法并将参数写入 `length` 作为 `(:) (not False) (map not [])`，这一点会更明显。

在实践中，模式匹配并不是强制求值的唯一因素。像 `(+)` 这样的原语也会强制求值其参数。

有些资料谈论的是“严格性”，而不是强迫，例如我们可以说 `(+)` 在两个参数中都是“严格的”。

### 10.4.2 关于共享的一句话

关于 Haskell 求值还有一件事：只要你给一个值起了*名称*，它就会被*共享*。这意味着这个名称的每次出现都指向同一个（可能尚未计算的）表达式。一旦表达式被计算，所有使用这个名称的地方都会看到同一个结果。

让我们看一个非常简单的示例。

```haskell
square x = x*x
```

根据前面的部分，你可能会想象求值的工作方式如下。求值首先以文本形式表示，然后以表达式树的形式直观地表示。

```haskell
square (2+2)
  ==> (2+2) * (2+2)   -- definition of square
  ==>   4   * (2+2)   -- (*) 强制求值左参数
  ==>   4   *   4     -- (*) 强制求值右参数
  ==>      16         -- definition of (*)
```

![平方函数的表达式树](img/square1.svg)

然而，真正发生的是，变量 `x` 命名的表达式 `2+2` 只计算一次。随后，求值结果会在 `square` 中两次出现的 `x` 之间共享。所以下面才是正确的求值过程，先用文字表示，再用图示表示。请注意，现在我们有的是*表达式图*，而不是表达式树。这也是 Haskell 求值有时被称为[*图归约*](https://en.wikipedia.org/wiki/Graph_reduction)的原因。

```haskell
square (2+2)
  ==> (2+2) * (2+2)
  ==>   4   *   4
  ==>      16
```

![平方函数的表达式图](img/square2.svg)

作为另一个示例，请考虑下面的函数 `f` 及其求值。

```haskell
f :: Int -> Int
f i = if i>10 then 10 else i
```

```haskell
                  _______shared________
                 |                     |
f (1+1) ==> if (1+1)>10 then 10 else (1+1)
        ==> if 2>10 then 10 else 2
        ==> if False then 10 else 2
        ==> 2
```

Haskell 不会计算 `1+1` 两次，因为它已被命名，并且该名称被使用了两次。我们可以将其与另一个带有两个参数的函数进行对比：

```haskell
g :: Int -> Int
g i j = if i>10 then 10 else j
```

```haskell
                        ______no sharing_____
                       |                     |
g (1+1) (1+1) ==> if (1+1)>10 then 10 else (1+1)
              ==> if 2>10 then 10 else (1+1)
              ==> if False then 10 else (1+1)
              ==> (1+1)
              ==> 2
```

这里有两个不同但等价的表达式，Haskell 不会神奇地自动共享它们。自动共享等价表达式是一种称为“公共子表达式消除 (CSE)”的优化。你可以在 [CSE 和 Haskell](https://wiki.haskell.org/GHC_optimisations#Common_subexpression_elimination) 了解更多信息。

你可以通过以下方式给表达式命名：

- 函数参数
- `let ... in ...`
- `where`

与惰性相结合，共享意味着*一个名称最多被求值一次*。

### 10.4.3 更多示例

你将在下面找到函数 `even` 的稍微人为的递归定义。它将说明强制和共享的概念。

```haskell
not :: Bool -> Bool
not True = False
not False = True

(||) :: Bool -> Bool -> Bool
True || _ = True
_    || x = x

even :: Int -> Bool
even x  =  x == 0  ||  not (even (x-1))
```

首先，请注意 `||` 强制其左参数，但不强制其右参数。 （换句话说，`||` *在其左参数中是严格的*。）这是因为我们只需要求值 `||` 的左参数即可知道哪个方程适用。这意味着 `even` 强制其第一个参数：

- `||` 强制 `x==0`
- `x==0` 强制 `x`

现在我们将表达式 `even 2` 计算为 WHNF。

```haskell
even 2
==> 2 == 0  ||  not (even (2-1))                    -- apply definition of even
==> False   ||  not (even (2-1))                    -- || 强制求值第一个参数
==> not (even (2-1))                                -- || 的第二个等式
==> not ((2-1) == 0 || not (even ((2-1)-1)))        -- not 强制求值它的参数：应用 even 的定义
==> not (  1   == 0 || not (even (  1  -1)))        -- note sharing!
==> not (  False    || not (even (1-1)))
==> not (not (even (1-1)))
==> not (not ((1-1) == 0 || not (even ((1-1)-1))))
==> not (not (  0   == 0 || not (even (  0  -1))))  -- (sharing)
==> not (not (   True    || not (even (0-1))))
==> not (not True)
==> not False
==> True
```

请注意，使用此替代定义 `even` 将不起作用。你能告诉我为什么吗？

```haskell
even' x =  not (even' (x-1))  ||  x == 0
```

现在我们可以真正理解前面的无限列表示例中发生了什么。让我们使用这些定义：

```haskell
head (x:_) = x
head [] = -1

filter p [] = []
filter p (x:xs) = if p x
                  then x : filter p xs
                  else filter p xs

map f [] = []
map f (x:xs) = f x : map f xs

-- [0..] is syntax sugar for enumFrom 0
enumFrom n = n : enumFrom (n+1)
```

我们开始吧：

```haskell
    head (filter (>100) (map (3^) [0..]))
=== head (filter (>100) (map (3^) (enumFrom 0)))
-- head 强制求值 filter，filter 又强制求值 map，map 又强制求值 enumFrom。我们应用 enumFrom 的定义。
==> head (filter (>100) (map (3^) (0:[1..])))
-- head 强制求值 filter，filter 又强制求值 map。我们应用 map 的第二个等式。
==> head (filter (>100) ((3^0) : map (3^) [1..]))
-- head 强制求值 filter。我们应用 filter 的第二个等式
==> head (if ((3^0)>100)
          then (3^0) : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- head 强制求值 if，if 强制求值 >，> 强制求值 ^。注意共享！
==> head (if (1>100)
          then 1 : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- head 强制求值 if，if 强制求值 >
==> head (if False
          then 1 : filter (>100) (map (3^) [1..])
          else filter (>100) (map (3^) [1..]))
-- 应用 if 的定义
==> head (filter (>100) (map (3^) [1..]))
-- 现在步子稍微迈大一点
==> head (filter (>100) (map (3^) (1:[2..])))
==> head (filter (>100) ((3^1) : map (3^) [2..]))
==> head (filter (>100) (3 : map (3^) [2..]))
==> head (filter (>100) (map (3^) [2..]))
-- 现在步子再迈大一点
==> head (filter (>100) (9 : map (3^) [3..]))
==> head (filter (>100) (27 : map (3^) [4..]))
==> head (filter (>100) (81 : map (3^) [5..]))
==> head (filter (>100) (243 : map (3^) [6..]))
==> head (243 : filter (>100) (map (3^) [6..]))
==> 243
```

呼。


## 10.5 处理无限列表

使用列表的函数在以利用惰性的方式编写时通常具有最佳性能。尝试实现此目的的一种方法是编写可以很好地处理无限列表的列表处理函数。

要编写一个转换无限列表的函数，你需要编写一个只查看输入列表的有限前缀的函数，然后输出 `(:)` 构造函数，然后递归。这是第一个示例。

```haskell
everySecond :: [a] -> [a]
everySecond [] = []
everySecond (x:y:xs) = x : everySecond xs
```

```haskell
take 10 (everySecond [0..])  ==>  [0,2,4,6,8,10,12,14,16,18]
```

编写适用于无限列表的函数的一个很好的启发是：结果的 `head` 可以便宜地求值吗？以下是两个不适用于无限输入的函数示例。对于 `mapTailRecursive` 来说，问题在于它需要在进入 WHNF 之前处理整个输入。在 `myDrop` 的情况下，问题在于它使用函数 `length`，不适用于无限列表，因为它尝试迭代直到列表末尾。

```haskell
map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs

mapTailRecursive :: (a -> b) -> [a] -> [b]
mapTailRecursive f xs = go xs []
    where go (x:xs) res = go xs (res++[f x])
          go []     res = res
```

```haskell
head (map inc [0..]) ==> head (inc 0 : map inc [1..]) ==> inc 0 ==> 1
head (mapTailRecursive inc [0..])
  ==> head (go [0..] [])
  ==> head (go [1..] ([]++[inc 0]))
  ==> head (go [2..] ([]++[inc 0]++[inc 1]))
  ==> head (go [3..] ([]++[inc 0]++[inc 1]++[inc 2]))
  --  永不终止
```

```haskell
drop :: Int -> [a] -> [a]
drop 0 xs = xs
drop _ [] = []
drop n (x:xs) = drop (n-1) xs

myDrop :: Int -> [a] -> [a]
myDrop 0 xs = xs
myDrop n xs = if n > length xs then [] else myDrop (n-1) (tail xs)
```

```haskell
head (drop 2 [0..]) ==> head (drop 1 [1..]) ==> head (drop 0 [2..]) ==> head [2..] ==> 2
head (myDrop 2 [0..])
  ==> head (if n > length [0..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+length [1..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+1+length [2..] then [] else myDrop (n-1) (tail [0..]))
  ==> head (if n > 1+1+1+length [3..] then [] else myDrop (n-1) (tail [0..]))
  --  永不终止
```

几乎标准库中的所有列表函数都是以这种形式编写的，例如：

```haskell
head (takeWhile (>=0) [0..]) ==> 0
head (concat (repeat [1,2,3])) ==> 1
head (zip [0..] [2..]) ==> (0,2)
head (filter even [3..]) ==> 4
```


## 10.6 插曲：添加严格性

还记得第 1 部分中的 `foldr` 吗？我们来看看它的近亲 `foldl`。这是列表的 `foldl` 的定义（它实际上是 `Foldable` 类型类的一部分，因此也适用于各种其他类型）。 `foldr` 从右到左处理列表，而 `foldl` 从左到右处理列表。更准确地说，`foldr` *关联到右侧*，而 `foldl` *关联到左侧*。请注意下一个示例中的差异：

```haskell
foldr (+) 0 [1,2,3]  ==>  1+(2+(3+0))
foldl (+) 0 [1,2,3]  ==>  ((0+1)+2)+3
```

以下是 `foldl` 和 `foldr` 的定义：

```haskell
foldl :: (a -> b -> a) -> a -> [b] -> a
foldl f z [] = z
foldl f z (x:xs) = foldl f (f z x) xs
```

```haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f y []     = y
foldr f y (x:xs) = f x (foldr f y xs)
```

由于 `foldr f y (x:xs) ==> f x (foldr f y xs)`，惰性求值可以在第二步集中到 `f` 上。因此，`foldr` 可以很好地处理惰性或短路操作：

```haskell
    foldr (&&) True [False,False,False]
==> False && (foldr (&&) True [False,False])
==> False
```

```haskell
    head (foldr (++) [] ["Hello","World","lorem","ipsum"])
==> head ("Hello" ++ (foldr (++) [] ["World","lorem","ipsum"]))
==> head ('H':("ello" ++ (foldr (++) [] ["World","lorem","ipsum"])))
==> 'H'
```

然而，`foldl` 需要处理完整个列表，才能产生一个 WHNF 值。原因是，只要 `foldl` 的列表参数仍然非空，它就会一直停留在最左、最外层的位置，成为惰性求值优先处理的目标。只有当列表变空后，求值才会继续简化累积出来的折叠值。

```haskell
    foldl (&&) True [False,False,False]
==> foldl (&&) (True&&False) [False,False]
==> foldl (&&) ((True&&False)&&False) [False]
==> foldl (&&) (((True&&False)&&False)&&False) []
==> ((True&&False)&&False)&&False
==> (    False    &&False)&&False
==>              False    &&False
==>                      False
```

```haskell
    head (foldl (++) [] ["Hello","World","lorem","ipsum"])
==> head (foldl (++) ([]++"Hello") ["World","lorem","ipsum"])
==> head (foldl (++) (([]++"Hello")++"World") ["lorem","ipsum"])
==> head (foldl (++) ((([]++"Hello")++"World")++"lorem") ["ipsum"])
==> head (foldl (++) (((([]++"Hello")++"World")++"lorem")++"ipsum") [])
==> head (((([]++"Hello")++"World")++"lorem")++"ipsum")
-- head 强制求值最后一个 ++，它又强制求值倒数第二个 ++，依此类推
==> head ((("Hello"++"World")++"lorem")++"ipsum")
-- 同样的事情再次发生
==> head ((('H':("ello"++"World"))++"lorem")++"ipsum")
-- 为了清楚起见，去掉不需要的 "ello"++"World" 表达式
==> head ((('H':__)++"lorem")++"ipsum")
-- 现在倒数第二个 ++ 可以工作了
==> head (('H':(__++"lorem"))++"ipsum")
-- 去掉 __++"lorem" 表达式
==> head (('H':__)++"ipsum")
-- 现在最后一个 ++ 可以工作了
==> head ('H':(__++"ipsum"))
==> 'H'
```

那么为什么要使用 `foldl` 呢？让我们再次回到第一个折叠示例。现在，由于 `+` 是严格的操作，因此两种类型的折叠都需要构建具有大量 `+` 的表达式。Haskell 实现需要在内存中跟踪这个表达式，这就是为什么这样的问题被称为“空间泄漏”。

```haskell
    foldr (+) 0 [1,2,3]
==> 1 + foldr (+) 0 [2,3]
==> 1 + (2 + foldr (+) 0 [3])
==> 1 + (2 + (3 + foldr (+) 0 []))
==> 1 + (2 + (3 + 0))
==> 1 + (2 + 3)
==> 1 + 5
==> 6
```

```haskell
    foldl (+) 0 [1,2,3]
==> foldl (+) (0+1) [2,3]
==> foldl (+) ((0+1)+2) [3]
==> foldl (+) (((0+1)+2)+3) []
==> ((0+1)+2)+3
==> (  1  +2)+3
==>       3  +3
==>          6
```

现在让我们看看当我们使用 `foldl'`（强制其第二个参数的 `foldl` 的一个版本）时会发生什么！

```haskell
    foldl' (+) 0 [1,2,3]
==> foldl' (+) (0+1) [2,3]
-- 强制求值第二个参数
==> foldl' (+) 1 [2,3]
==> foldl' (+) (1+2) [3]
-- 强制求值第二个参数
==> foldl' (+) 3 [3]
==> foldl' (+) (3+3) []
-- 强制求值第二个参数
==> foldl' (+) 6 []
==> 6
```

现在，工作是在扫描列表的同时逐步执行的。没有空间泄漏！有时过于惰性会导致空间泄漏，稍微严格一点就可以解决这些问题。

你可以在`Data.List`模块中找到`foldl'`，它的工作原理就像这样。但如何实现 `foldl'` 呢？我们现在当然知道如何针对特定类型（例如 `Int`）执行此操作。我们只是在第二个参数上添加模式匹配，这不会改变函数的语义。

```haskell
foldl'Int :: (Int -> Int -> Int) -> Int -> [Int] -> Int
foldl'Int f z [] = z
foldl'Int f 0 (x:xs) = foldl'Int f (f 0 x) xs
foldl'Int f z (x:xs) = foldl'Int f (f z x) xs
```

```haskell
    foldl'Int (+) 0 [1,2,3]
==> foldl'Int (+) (0+1) [2,3]
-- 为了能在第二个和第三个等式之间选择，需要强制求值 (0+1)
==> foldl'Int (+) 1 [2,3]
-- 适用第三个等式
==> foldl'Int (+) (1+2) [3]
-- 同样，我们需要在第二个和第三个等式之间选择
==> foldl'Int (+) 3 [3]
==> foldl'Int (+) (3+3) []
==> 3+3
==> 6
```

要编写 `foldl'` 的通用实现，我们需要引入一个新的内置函数 `seq`。调用 `seq a b` 的计算结果为 `b`，但强制 `a` 进入 WHNF。以下是在 GHCi 中使用 `seq` 的一些示例。为了演示求值的内容，我们使用特殊值 `undefined`，如果尝试将其求值为 WHNF，则会导致错误。

```haskell
Prelude> seq (not True) 3
3
Prelude> seq undefined 3
*** Exception: Prelude.undefined
Prelude> (seq (not True) 3) + 7
10
Prelude> (seq undefined 3) + 7
*** Exception: Prelude.undefined
Prelude> let f x = f x in seq (f 3) 3
-- ...infinite recursion
```

作为在函数中使用 `seq` 的示例，以下是 `head` 的一个版本，它不适用于无限列表（因为它计算列表的最后一个元素）：

```haskell
strictHead :: [a] -> a
strictHead xs = seq (last xs) (head xs)
```

让我们在 GHCi 中尝试一下：

```haskell
Prelude> head [1,2,3]
1
Prelude> strictHead [1,2,3]
1
Prelude> head (1:2:undefined)
1
Prelude> strictHead (1:2:undefined)
*** Exception: Prelude.undefined
Prelude> head [1..]
1
Prelude> strictHead [1..]
-- ...infinite recursion
```

最后，这是 `foldl'` 的定义。请注意我们如何需要引入新变量 `z'` 的共享，以便能够使 `seq` 计算新值，然后在递归调用中使用它。新的定义也被用在下面对 `foldl' (+) 0 [1,2,3]`的更详细的求值中。

```haskell
foldl' :: (a -> b -> a) -> a -> [b] -> a
foldl' f z [] = z
foldl' f z (x:xs) = let z' = f z x
                    in seq z' (foldl' f z' xs)
```

```haskell
    foldl' (+) 0 [1,2,3]
==> seq (0+1) (foldl' (+) (0+1) [2,3])  -- seq 强制求值第一个参数
          |                 |
          +-----sharing-----'
          |                 |
==> seq   1   (foldl' (+)   1   [2,3])  -- seq 的第一个参数处于 WHNF，seq 消失
==> foldl' (+) 1 [2,3]
==> seq (1+2) (foldl' (+) (1+2) [3])
==> seq   3   (foldl' (+)   3   [3])
==> foldl' (+)   3   [3]
==> seq (3+3) (foldl' (+) (3+3) [])
==> seq   6   (foldl' (+)   6   [])
==> foldl' (+)   6   []
==> 6
```

我们不会在本课程中深入探讨这个主题，但重要的是你要知道 `seq` 的存在。你可以在 [the Haskell Wiki](https://wiki.haskell.org/Seq) 上找到有关 `seq` 的更多信息，并在 [Real World Haskell](https://book.realworldhaskell.org/read/profiling-and-optimization.html) 中了解有关何时需要添加严格性的更多信息。正如 [FPComplete](https://www.fpcomplete.com/blog/2017/09/all-about-strictness/) 和 [Real World Haskell](https://book.realworldhaskell.org/read/profiling-and-optimization.html) 所讨论的，通常使用*bang 模式*而不是 `seq` 更好。


## 10.7 newtype 声明

回想第 7 课。有时我们需要盒装类型。当需要装箱类型时，可以使用特殊关键字 `newtype` 来代替 `data`。 `newtype` 期望只有一个构造函数，只有一个字段。例如，

```haskell
newtype Money = Cents Int
```

然而，下面的方法不起作用，你需要`data`：

```haskell
-- 编译器不会接受这些！
newtype Currency = Dollars Int | Euros Int
newtype Money = Money Int Int
```

那么有什么区别呢？就写代码而言，没什么。你使用 `newtype` 的方式与使用 `data` 的方式完全相同。但是，内存布局不同。使用 `data` 引入了间接层（构造函数），但使用 `newtype` 则不会。 `data` 的间接寻址对于支持多个构造函数和多个字段是必需的。举例说明：

```haskell
code:                                 memory:

data Money = Cents Int                x --> Cents --> 100
x = Cents 100


newtype Money = Cents Int             x --> 100
x = Cents 100
```

这种差异会产生很多影响。首先，`newtype` 效率更高：类型可以说是在编译时“消失”。不过，类型仍然会被检查，因此你可以获得类型安全，而不会对性能产生任何影响。其次，新类型是“严格的”。具体来说，这意味着只有当 `x` 处于 WHNF 时，`Money x` 才处于弱头范式。这可以在 GHCi 中得到见证：

```haskell
-- 如果使用 data，Cents undefined 处于 WHNF
Prelude> data Money = Cents Int
Prelude> seq (Cents undefined) True
True
-- 如果使用 newtype，Cents undefined 不处于 WHNF，尝试
-- 让它进入 WHNF 会在 undefined 上失败
Prelude> newtype Money = Cents Int
Prelude> seq (Cents undefined) True
*** Exception: Prelude.undefined
```

那么什么时候应该使用`newtype`呢？一般来说，只要你有单字段单构造函数数据类型，最好使用 `newtype`。然而，如果你始终使用 `data`，则不会出现灾难性错误。当你需要为类型定义不同的类型类实例时，也经常使用 `newtype` 模式。这是一个定义具有倒序的数字类型的示例

```haskell
newtype Inverted = Inverted Int
  deriving (Show, Eq)

instance Ord Inverted where
  compare (Inverted i) (Inverted j) = compare j i
```

```haskell
Prelude Data.List> sort [1,2,3]
[1,2,3]
Prelude Data.List> sort [Inverted 1,Inverted 2,Inverted 3]
[Inverted 3,Inverted 2,Inverted 1]
```


## 10.8 趣味内容：打结

现在我们了解了共享和路径复制，我们可以制作自己的*循环数据结构*。还记得列表讲座中的 `cycle` 示例吗？

```haskell
Prelude> take 21 $ cycle "asdf"
"asdfasdfasdfasdfasdfa"
```

这就是它在内存中的样子：

![循环有向图示例](img/DAG4.svg)

早些时候有人说 Haskell 数据在内存中形成有向图。这是带有循环的有向图的示例。

我们如何定义这样的结构？我们只是给一个值一个名称，并在值本身中引用该名称。也就是说，该值是*递归*或*自引用*。这个技巧被称为“打结”。一个简单的示例：

```haskell
  code                     memory

let xs = 1:2:xs      xs -> (1:) -> (2:) -+
 in xs                      ^            |
                            +------------+
```

请注意我们如何在 `xs` 的定义中使用名称 `xs`。当我们进行这样的递归定义时，共享会导致它在内存中变成循环结构。

一个更有趣的示例：一个简单的冒险游戏，其中的世界是一个自我参照的结构。请注意循环结构是如何使用相互引用的局部定义构建的。

```haskell
data Room = Room String [(String,Room)]

describe :: Room -> String
describe (Room s _) = s

move :: Room -> String -> Maybe Room
move (Room _ directions) direction = lookup direction directions

world :: Room
world = meadow
  where
    meadow = Room "It's a flowery meadow next to a cliff." [("Stay",meadow),("Enter cave",cave)]
    cave = Room "You are in a cave" [("Exit",meadow),("Go deeper",tunnel)]
    tunnel = Room "This is a very dark tunnel. It seems you can either go left or right."
                  [("Go back",cave),("Go left",pit),("Go right",treasure)]
    pit = Room "You fall into a pit. There is no way out." []
    treasure = Room "A green light from a terminal fills the room. The terminal says <<loop>>."
                    [("Go back",tunnel)]

play :: Room -> [String] -> [String]
play room [] = [describe room]
play room (d:ds) = case move room d of Nothing -> [describe room]
                                       Just r -> describe room : play r ds
```

```haskell
Prelude> play world ["Stay","Enter cave","Go deeper","Go back","Go deeper","Go right"]
["It's a flowery meadow next to a cliff.",
 "It's a flowery meadow next to a cliff.",
 "You are in a cave",
 "This is a very dark tunnel. It seems you can either go left or right.",
 "You are in a cave",
 "This is a very dark tunnel. It seems you can either go left or right.",
 "A green light from a computer terminal floods the room. The terminal says <<loop>>."]
```

这是游戏的 `world` 在内存中的样子：

                   ,-----------------,
                   v                 |
             +-----------------------|-----------------+
    meadow-->|Room "It's..." ["Stay" o, "Enter cave" o]|
             +---------------------------------------|-+
                                        ^            v
             +--------------------------|----------------+
    cave---->|Room "You are..." ["Exit" o, "Go deeper" o]|
             +-----------------------------------------|-+
                                           ^           v
             +-----------------------------|----------------------------+
    tunnel-->|Room "This is..." ["Go back" o, "Go left" o, "Go right" o]|<--------,
             +------------------------------------------|-------------|-+         |
                                                        |             |           |
                         ,------------------------------'             |           |
                         v                                            v           |
             +---------------------+                +-----------------------------|-+
    pit----->|Room "You fall..." []|    treasure--->|Room "A green..." ["Go back" o]|
             +---------------------+                +-------------------------------+

我们现在已经看到了三种类型的递归。递归函数调用自身。递归类型允许我们表达任意大的结构。递归值是实现无限结构的一种方法。


## 10.9 趣味内容：Debug.Trace

尽管 Haskell 是一种纯的编程语言，但我们有时可以通过加入一些非纯性来获得见解。

我们可以使用模块 `Debug.Trace` 中的函数 `trace :: String -> a -> a` 来查看 Haskell 求值。表达式 `trace "message" x` 与 `x` 相同，但在求值（强制）时打印 `message`。我们可以用 `trace`来见证`||` 运算符的惰性：

```haskell
Prelude> import Debug.Trace
Prelude Debug.Trace> trace "a" True
a
True
Prelude Debug.Trace> trace "a" False || trace "b" True
a
b
True
Prelude Debug.Trace> trace "a" True || trace "b" True
a
True
```

我们还可以看看列表元素何时被求值。请注意，`length` 不需要求值列表的元素，而 `sum` 需要求值所有元素。 （准确地说，`head xs` 实际上并不计算 `xs` 的第一个元素，而是将其返回给 GHCi，GHCi 对其进行计算以显示它。）

```haskell
Prelude Debug.Trace> head [trace "first" 1, trace "second" 2, trace "third" 3]
first
1
Prelude Debug.Trace> last [trace "first" 1, trace "second" 2, trace "third" 3]
third
3
Prelude Debug.Trace> length [trace "first" 1, trace "second" 2, trace "third" 3]
3
Prelude Debug.Trace> sum [trace "first" 1, trace "second" 2, trace "third" 3]
third
second
first
6
```

`Debug.Trace` 还提供 `trace` 的有用变体。值得注意的是 `traceShowId x`，它打印 `show x` 并计算为 `x`。让我们使用 `traceShowId` 验证之前的 head-filter-map 示例的求值。请注意，即使我们将 `traceShowId` 映射到无限列表 `[0..]`，实际上也只求值了 6 个值。最后的 243 是返回值，而不是跟踪打印。

```haskell
Prelude Debug.Trace> head (filter (>100) (map (\x -> traceShowId (3^x)) [0..]))
1
3
9
27
81
243
243
```

当你遇到无限递归错误时，`Debug.Trace` 特别有用。这是一个示例：

```haskell
-- 计算类似 7+5+3+1 的和
sumEverySecond :: Int -> Int
sumEverySecond 0 = 0
sumEverySecond n = n + sumEverySecond (n-2)
```

```haskell
sumEverySecond 6 ==> 12
sumEverySecond 7 ==> doesn't terminate
```

我们可以通过添加 `trace` 来包装整个递归情况来调试它。

```haskell
sumEverySecond :: Int -> Int
sumEverySecond 0 = 0
sumEverySecond n = trace ("sumEverySecond "++show n) (n + sumEverySecond (n-2))
```

```haskell
Prelude Debug.Trace> sumEverySecond 6
sumEverySecond 6
sumEverySecond 4
sumEverySecond 2
12
Prelude Debug.Trace> sumEverySecond 7
sumEverySecond 7
sumEverySecond 5
sumEverySecond 3
sumEverySecond 1
sumEverySecond -1
sumEverySecond -3
sumEverySecond -5
-- 依此类推
```

啊哈！问题是我们的递归基本情况 `sumEverySecond 0` 不足以停止递归。

最后，请注意一点。使用 `trace`，尤其是 `traceShowId`，可能会导致原本不会被求值的事情得到求值。例如：

```haskell
Prelude Debug.Trace> traceHead xs = head (traceShowId xs)
Prelude Debug.Trace> traceHead [0..]
-- 永不终止，因为它试图显示一个无限列表
```

因此，在进行练习时，请随意使用 `Debug.Trace`，但请尝试将 `trace` 调用排除在最终答案之外。某些练习集会检查你的导入并不允许 `Debug.Trace`。

我们将在下一讲中看到处理副作用的更有原则的方法！


## 10.10 测验

以下哪些说法是正确的？

1.`reverse . reverse . reverse === reverse`
2.`reverse . reverse === reverse`
3.`reverse . id === id`

以下哪一个是以 `[0,1,2,1,2,1,2...]` 开头的无限列表？

1.`cycle [0,1,2]`
2.`0:repeat [1,2]`
3.`0:cycle [1,2]`
4.`0:[1,2..]`

求值这个表达式时下一步是什么？

```haskell
head (map not (True:False:[]))
```

1.`head (False : True : [])`
2.`head (not True)`
3.`head (False : map not (False:[]))`
4.`head (not True : map not (False:[]))`

以下哪个值*不*处于弱头正常形式？

1.`map`
2.`f 1 : map f (2 : [])`
3.`Just (not False)`
4.`(\x -> x) True`

关于以下函数的哪些陈述是正确的？

```haskell
f 0 x = 1+x
f _ x = 2+x
```

1. `f` 的左参数严格
2. `f` 其正确论证严格
3. `f` 强制其两个参数
4.以上都不是

此函数是否可以使用无限列表作为输入？为什么？

```haskell
f [] = []
f (x:xs) = x : map not xs
```

1. 不可以，因为它包含一个 `[]` 情况，这是从未达到的。
2. 不，因为它使用 `map`，它求值整个列表。
3. 是的，因为它在生成 WHNF 值之前只查看列表的第一个元素。
4. 是的，因为它调用 `map`，它适用于无限列表。

这个呢？

```haskell
f xs = map (+(sum xs)) xs
```

1. 不，因为它使用 `map`，它求值整个列表。
2. 不可以，因为计算结果的 `head` 需要整个输入列表。
3. 是的，因为它不包括 `[]` 外壳
4. 是的，因为它调用 `map`，它适用于无限列表。


## 10.11 练习

- [Set10a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set10a.hs)
- [Set10b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set10b.hs)
