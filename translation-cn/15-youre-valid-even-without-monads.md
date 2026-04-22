# Haskell 慕课，第 2 部分

- [15 第 15 讲：没有 Monad 也能验证](#lecture-15-youre-valid-even-without-monads)
  - [15.1 Applicative 简介](#introduction-to-applicatives)
  - [15.2 列表 Applicative](#the-list-applicative)
  - [15.3 新运算符](#new-operators)
  - [15.4 Validation Applicative](#the-validation-applicative)
  - [15.5 验证列表：`traverse`](#validating-lists-traverse)
  - [15.6 附注：`Traversable`](#sidenote-traversable)
  - [15.7 处理失败：`Alternative`](#dealing-with-failure-alternative)
  - [15.8 附注：语境中的 Applicative](#sidenote-applicatives-in-context)
  - [15.9 测验](#quiz-5)
  - [15.10 练习](#exercises-6)


<a id="lecture-15-youre-valid-even-without-monads"></a>

# 15 第 15 讲：没有 Monad 也能验证


<a id="introduction-to-applicatives"></a>

## 15.1 Applicative 简介

`Applicative` 类型类是 `Functor`（你不能用它做那么多事情）和 `Monad`（它几乎允许你编写任意程序）之间的中间立场。使用 `Applicative` 而不是 `Monad` 的原因包括：

- 性能：由于`Applicative`允许的操作较少，因此可以比`Monad`更好地优化。
- 简单性：`Applicative` 接口更容易推理。
- 必要性：无法为你的类型定义 `Monad` 实例，但有一个 `Applicative` 实例。这是很少见的。

那么什么是`Applicative`？我们来看一个定义。

``` haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  -- other operations omitted for now
```

因此，`Applicative` 是 `Functor`，它允许我们通过 `pure` 构建单例值，并使用 `liftA2` 将两个值合并为一个值。与裸 Functor 相比，这增加了很多函数。使用 Functor 的计算必然是线性的：`fmap :: (a -> b) -> f a -> f b` 接受一个 Functor 值，并输出另一个 Functor 值。相比之下，`pure` 不接收任何 Functor 值并输出 1 个，而 `liftA2` 接收 2 个并返回 1 个。

附注：Applicative 这个术语来自术语 [Applicative Functor](https://en.wikipedia.org/wiki/Applicative_functor)，听起来像是来自范畴论，但实际上是在一篇编程论文中引入的。

现在抽象的胡言乱语已经够多了。让我们看看我们可以使用Applicative 运算（和 `fmap`）来表达什么样的计算。我们将从 `Maybe` Applicative 开始。这是一个简化的定义：

``` haskell
instance Applicative Maybe where
  pure x = Just x
  liftA2 f (Just x) (Just y) = Just (f x y)
  liftA2 f _        _        = Nothing
```

你将看到该定义使用与 `Monad Maybe` 实例相同类型的故障传播。让我们在解析货币值时使用它：

``` haskell
data Currency = EUR | USD
  deriving (Show, Eq)
data Money = Money Int Currency
  deriving (Show, Eq)

parseCurrency :: String -> Maybe Currency
parseCurrency "e" = pure EUR
parseCurrency "€" = pure EUR
parseCurrency "$" = pure USD
parseCurrency _ = Nothing

parseAmount :: String -> Maybe Int
parseAmount = readMaybe

parseMoney :: String -> String -> Maybe Money
parseMoney amountString currencyString =
  liftA2 Money (parseAmount amountString) (parseCurrency currencyString)
```

``` haskell
parseMoney "123" "€"  ==> Just (Money 123 EUR)
parseMoney "45" "$"   ==> Just (Money 45 USD)
parseMoney "4x" "€"   ==> Nothing
parseMoney "45" "£"   ==> Nothing
```

效果很好。然而，如果我们尝试对此进行扩展，我们很快就会遇到 `Applicative` 的限制。例如，考虑这个 `sumMoney` 函数，该函数对 `Money` 值求和，但如果它们不是相同的货币，则会失败：

``` haskell
sumMoney :: Money -> Money -> Maybe Money
sumMoney (Money a c) (Money b c')
    | c == c'   = Just (Money (a+b) c)
    | otherwise = Nothing
```

我们无法使用 `Applicative` 操作将其应用于两个 `Maybe Money` 值。为此，我们需要 `Maybe` monad：

``` haskell
example :: Maybe Money
example = do x <- parseMoney "123" "€"
             y <- parseMoney "45" "$"
             sumMoney x y
```

如果我们尝试使用 `liftA2`，我们就会陷入 `Maybe (Maybe Money)` 类型。此外，我们现在可以获得两种不同类型的故障：`Nothing` 和 `Just Nothing`，具体取决于错误发生的级别。这是切换到 `Monad` 实例的明显情况。

    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "€")
      ==> Just (Just (Money 168 EUR))
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "$")
      ==> Just Nothing
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "xxx" "e")
      ==> Nothing

附注：`liftA2` 这个名字听起来有点麻烦，但它与 monad 的 `liftM`、`liftM2` 等函数进行类比。回想一下，`liftM` 只是 `fmap`，所以也许 `liftA2` 应该被称为 `fmap2`。


<a id="the-list-applicative"></a>

## 15.2 列表 Applicative

让我们看看我们见过的另一个`Functor`的应用实例。列表Functor的 `Applicative` 实例会遍历所有可能的值组合（就像列表 monad 一样）。这是例子：

``` haskell
instance Applicative [] where
  pure x = [x]
  liftA2 f xs ys = [f x y | x <- xs, y <- ys]
```

这是一个例子：生成一些短语。

``` haskell
things :: [String]
things = ["tangerine","bandit","diamond"]

fruits :: [String]
fruits = ["apple", "tangerine"]

phrases :: [String]
phrases = liftA2 combine things fruits
  where combine t f = "a " ++ t ++ " the size of a " ++ f

bunches = liftA2 copy [1,2,3] fruits
  where copy n f = unwords (replicate n f)
```

``` haskell
phrases ==> ["a tangerine the size of a apple",
             "a tangerine the size of a tangerine",
             "a bandit the size of a apple",
             "a bandit the size of a tangerine",
             "a diamond the size of a apple",
             "a diamond the size of a tangerine"]

bunches ==> ["apple","tangerine",
             "apple apple","tangerine tangerine",
             "apple apple apple","tangerine tangerine tangerine"]
```


<a id="new-operators"></a>

## 15.3 新运算符

有一些非常方便的Applicative 运算符。它们是 `<$>`、`<*>`、`<*` 和 `*>`。

让我们从 `<$>` 开始，它只是 `fmap` 的中缀版本：

``` haskell
(<$>) :: Functor f => (a -> b) -> f a -> f b
f <$> x = fmap f x
```

``` haskell
not <$> Just True   ==> Just False
not <$> Nothing     ==> Nothing
negate <$> [1,2,3]  ==> [-1,-2,-3]
```

这本身就很好，但是当与这个 Applicative 运算符结合使用时，它真的会大放异彩：

``` haskell
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```

该类型告诉你 `<*>` 的作用：它的函数Applicative“提升”为Applicative。以下是一些独立的示例：

``` haskell
Just not <*> Just True    ==> Just False
Nothing  <*> Just True    ==> Nothing
Just not <*> Nothing      ==> Nothing
[(+1),(*2)] <*> [10,100]  ==> [11,101,20,200]
```

当我们将 `<$>` 和 `<*>` 结合起来时，真正的魔力发生了：然后我们可以将任意多个参数的函数提升为 Applicative！

``` haskell
say :: String -> Int -> String -> String
say x i y = x ++ " has " ++ show i ++ " " ++ y
```

``` haskell
say <$> Just "haskell" <*> Just 99 <*> Just "operators"
  ==> Just "haskell has 99 operators"
say <$> Nothing <*> Just 99 <*> Just "operators"
  ==> Nothing
say <$> ["bob","jake"] <*> [2,3] <*> ["bananas","cars"]
  ==> ["bob has 2 bananas",
       "bob has 2 cars",
       "bob has 3 bananas",
       "bob has 3 cars",
       "jake has 2 bananas",
       "jake has 2 cars",
       "jake has 3 bananas",
       "jake has 3 cars"]
```

这是怎么回事？让我们逐步进行评估。关键是每个 `<*>` 部分地向函数应用一个以上的参数。

``` haskell
    say <$> Just "haskell" <*> Just 99 <*> Just "operators"
=== ((say <$> Just "haskell") <*> Just 99) <*> Just "operators"
=== (fmap say (Just "haskell") <*> Just 99) <*> Just "operators"
==> (Just (say "haskell") <*> Just 99) <*> Just "operators"
==> Just (say "haskell" 99) <*> Just "operators"
==> Just (say "haskell" 99 "operators")
==> Just "haskell has 99 operators"
```

也许查看类型会更清楚：

``` haskell
say <$> Just "haskell"                                  :: Maybe (Int -> String -> String)
say <$> Just "haskell" <*> Just 99                      :: Maybe (       String -> String)
say <$> Just "haskell" <*> Just 99 <*> Just "operators" :: Maybe (                 String)
```

接下来的两个运算符稍微简单一些：

``` haskell
(*>) :: Applicative f => f a -> f b -> f b
x *> y = liftA2 (\a b -> b) x y

(<*) :: Applicative f => f a -> f b -> f a
x <* y = liftA2 (\a b -> a) x y
```

你可以将这些类型与更熟悉的运算符进行比较：

``` haskell
(>>) :: Monad m => m a -> m b -> m b
```

运算符 `<*` 和 `*>` 的含义是：运行这两个操作，但只保留一个结果。箭头指向保留的结果：

``` haskell
Just 1 *> Just 2  ==> Just 2
Just 1 <* Just 2  ==> Just 1
Just 1 <* Nothing ==> Nothing
Nothing <* Just 2 ==> Nothing
```

这些运算符可能看起来微不足道，但它们在组合检查时非常有用。例如：

``` haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing

small :: Int -> Maybe Int
small i = if i<10 then Just i else Nothing

decreaseSmall :: Int -> Maybe Int
-- do what decrease does, but fail if small fails
decreaseSmall i = decrease i <* small i
```

``` haskell
decreaseSmall 4   ==> Just 3
decreaseSmall 0   ==> Nothing
decreaseSmall 11  ==> Nothing
```

现在我们已经了解了所有这些运算符，我们可以理解 `Applicative` 的完整定义。所有运算符都有 `liftA2` 的定义，因此在实现 `Applicative` 实例时定义 `liftA2` 和 `pure` 就足够了。

``` haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  (<*>) :: f (a -> b) -> f a -> f b
  (*>) :: f a -> f b -> f b
  (<*) :: f a -> f b -> f a
```


<a id="the-validation-applicative"></a>

## 15.4 Validation Applicative

让我们看一个比 Maybe 或列表更有趣的 Applicative。在编程中，我们通常需要“验证”用户的一些输入。在这些情况下，将输入可能存在的所有错误收集在一起很有用。文件 [`exercises/Examples/Validation.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Validation.hs) 实现 `Validation` 数据类型：

``` haskell
data Validation a = Ok a | Errors [String]
  deriving (Show,Eq)
```

`Validation` 的 `Applicative` 实例的工作方式如下：

``` haskell
liftA2 (+) (Ok 1) (Ok 2)
  ==> Ok 3
liftA2 (+) (Errors ["oh no"]) (Errors ["boom"])
  ==> Errors ["oh no","boom"]
```

请注意，与 `Maybe` Applicative相比，我们有许多不同类型的故障。

这是一个有效的示例，介绍了一些助手，然后使用它们来祝贺某人的生日：

``` haskell
invalid :: String -> Validation a
invalid err = Errors [err]

check :: Bool -> String -> a -> Validation a
check b err x
  | b = pure x
  | otherwise = invalid err

birthday :: String -> Int -> Validation String
birthday name age = liftA2 congratulate checkedName checkedAge
  where checkedName = check (length name < 10) "Name too long" name
        checkedAge = check (age < 99) "Too old" age
        congratulate n a = "Happy "++show a++"th birthday "++n++"!"
```

``` haskell
birthday "Guy" 31
  ==> Ok "Happy 31th birthday Guy!"
birthday "Guybrush Threepwood" 31
  ==> Errors ["Name too long"]
birthday "Yog-sothoth" 10000
  ==> Errors ["Name too long","Too old"]
```

哦，对了，这是 `Validation` 的 `Functor` 和 `Applicative` 实例：

``` haskell
instance Functor Validation where
  fmap f (Ok x) = Ok (f x)
  fmap _ (Errors e) = Errors e

instance Applicative Validation where
  pure x = Ok x
  liftA2 f (Ok x)      (Ok y)      = Ok (f x y)
  liftA2 f (Errors e1) (Ok y)      = Errors e1
  liftA2 f (Ok x)      (Errors e2) = Errors e2
  liftA2 f (Errors e1) (Errors e2) = Errors (e1++e2)
```

`Validation` 的 `liftA2` 的定义表明错误是从左到右收集在一起的。这可以在上面的示例中看到，其中表达式 `liftA2 congratulate checkedName checkedAge` 首先输出来自 `checkedName` 的错误 (`"Name too long"`)，最后输出来自 `checkedAge` 的错误 (`"Too old"`)。


<a id="validating-lists-traverse"></a>

## 15.5 验证列表：`traverse`

到目前为止，我们已经处理了固定大小的事物和Applicative：我们已经将两个或三个参数的函数应用于某些事物。如果我们有任意数量的输入怎么办？如果我们需要验证列表怎么办？

让我们看一下实现这样的函数的一些方法：

``` haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

与往常一样，在使用列表时，模式匹配和递归通常是最佳选择。这是一个递归解决方案：

``` haskell
allPositive :: [Int] -> Validation [Int]
allPositive [] = Ok []
allPositive (x:xs) = liftA2 (:) checkThis checkRest
  where checkThis = check (x>=0) ("Not positive: "++show x) x
        checkRest = allPositive xs
```

总是拼写出这样的递归有点麻烦。如果我们在 `Monad` 中工作，我们可以使用像 `mapM` 这样的助手：

``` haskell
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

`Applicative` 的 `mapM` 等效项称为 `traverse`。它是类型类 `Traversable` 的成员：

``` haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这是一个很糟糕的类型签名，所以让我们稍微简化一下。列表是 `Traversable`，所以我们可以将这种类型专门化为：

``` haskell
traverse :: Applicative f => (a -> f b) -> [a] -> f [b]
```

这看起来正是我们所需要的！对于同样是 Monad 的 Applicatives，`traverse` 只是 `mapM` 的另一个名称：

``` haskell
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

但对于我们的 `Validation`（不是 `Monad`）来说，`traverse` 正是我们想要的：

``` haskell
allPositive :: [Int] -> Validation [Int]
allPositive xs = traverse checkNumber xs
  where checkNumber x = check (x>=0) ("Not positive: "++show x) x
```

``` haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

请注意 `Validation` 的 `traverse` 如何按照原始列表中出现的顺序将所有错误收集在一起。

P.S. 事实上，`Validation` 是 `Applicative` 不可能是 `Monad` 的少数示例之一。你能弄清楚为什么吗？


<a id="sidenote-traversable"></a>

## 15.6 附注：`Traversable`

那么`Traversable`是什么东西呢？很多熟悉的结构。以下是一些示例：

``` haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing
```

``` haskell
-- Lists are Traversable
traverse decrease [1,2,3] ==> Just [0,1,2]
traverse decrease [1,0,3] ==> Nothing

-- Arrays are Traversable
traverse decrease (array (1,3) [(1,10),(2,11),(3,12)])
         ==> Just (array (1,3) [(1,9),(2,10),(3,11)])

-- Maps are Traversable
traverse decrease (M.fromList [("a",1),("b",2)])
         ==> Just (M.fromList [("a",0),("b",1)])
traverse decrease (M.fromList [("a",1),("b",0)])
         ==> Nothing

-- Either is Traversable
traverse decrease (Left "abc") ==> Just (Left "abc")
traverse decrease (Right 3)    ==> Just (Right 2)
traverse decrease (Right 0)    ==> Nothing
```

所以 `Traversable` 是各种容器的类型类，有点像 `Foldable`。确实，如果你看一下定义，`Traversable` 是 `Foldable` 的子类。事实证明，`traverse` 和 `mapM` 是该类的方法！

``` haskell
class (Functor t, Foldable t) => Traversable t where
  traverse :: Applicative f => (a -> f b) -> t a -> f (t b)
  mapM :: Monad m => (a -> m b) -> t a -> m (t b)
```

在这里很难保持类型的正确性。我们回到`traverse`的类型：

``` haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这里我们有两个Functor：`t` 和 `f`。 `t` Functor也是 `Foldable` 和 `Traversable`，`f` Functor也是 `Applicative`。 `traverse` 函数允许我们在 `t` 容器内运行 `f` 操作。

如果这感觉很抽象，请不要担心。实际上，你几乎总是在列表上使用 `traverse`。


<a id="dealing-with-failure-alternative"></a>

## 15.7 处理失败：`Alternative`

如果你稍微尝试一下Applicative，你就会开始注意到它们的函数有一些限制。例如，当像我们在 `parseMoney` 示例中那样编写解析器时，如果能够尝试几个不同的解析器并获取任何非失败结果，那就太好了。对于像 `Maybe` 这样的具体Applicative来说，这很容易编写，如下所示。

``` haskell
data Answer = Yes | No
  deriving (Show, Eq)

parseYes :: String -> Maybe Answer
parseYes "y" = Just Yes
parseYes "yes" = Just Yes
parseYes "maybe" = Just Yes
parseYes _ = Nothing

parseNo :: String -> Maybe Answer
parseNo "n" = Just No
parseNo "no" = Just No
parseNo "maybe" = Just No
parseNo _ = Nothing

eitherOf :: Maybe x -> Maybe x -> Maybe x
eitherOf (Just x) _  = Just x
eitherOf Nothing  mx = mx

parseAnswer :: String -> Maybe Answer
-- prefer positive answers!
parseAnswer s = eitherOf (parseYes s) (parseNo s)
```

``` haskell
parseAnswer "yes"    ==> Just Yes
parseAnswer "y"      ==> Just Yes
parseAnswer "n"      ==> Just No
parseAnswer "maybe"  ==> Just Yes
parseAnswer "x"      ==> Nothing
```

我们如何概括 `eitherOf`？我们不能给它类型 `Applicative f => f x -> f x -> f x`，因为这样实现就需要有效地类似于 `eitherOf a b = liftA2 something a b`，但是 `eitherOf Nothing (Just x)` 将是 `Nothing`（因为这就是 Applicative 实例的工作方式）！

事实证明我们需要一个新的类型类：`Alternative`。 Alternative 在 Applicative 中添加了两个操作：`empty` 表示没有结果，`<|>` 表示合并结果。

``` haskell
class Applicative f => Alternative f where
  empty :: f a
  (<|>) :: f a -> f a -> f a
  -- some other operations omitted
```

现在我们可以使用通用操作重写我们的解析代码：

``` haskell
data Answer = Yes | No
  deriving (Show, Eq)

parseYes :: Alternative f => String -> f Answer
parseYes "y" = pure Yes
parseYes "yes" = pure Yes
parseYes "maybe" = pure Yes
parseYes _ = empty

parseNo :: Alternative f => String -> f Answer
parseNo "n" = pure No
parseNo "no" = pure No
parseNo "maybe" = pure No
parseNo _ = empty

parseAnswer :: Alternative f => String -> f Answer
parseAnswer s = parseYes s <|> parseNo s
```

我们还可以选择在哪个 `Alternative` 中运行解析器以获得不同的行为。 `Maybe` 只给我们一个结果，而 `[]` 给我们所有可能的结果。

``` haskell
> parseAnswer "yes" :: Maybe Answer
Just Yes
> parseAnswer "maybe" :: Maybe Answer
Just Yes
> parseAnswer "yes" :: [Answer]
[Yes]
> parseAnswer "maybe" :: [Answer]
[Yes,No]
```

`[]` 和 `Maybe` 的 `Alternative` 实例并不令人意外：

``` haskell
instance Alternative [] where
  empty = []
  (<|>) = (++)

instance Alternative Maybe where
  empty = Nothing
  Just x  <|> _  = Just x
  Nothing <|> mx = mx
```

`Validation` 类型也是 `Alternative`。该实例将所有错误消息收集在一起，就像 `Applicative` 实例一样。

``` haskell
instance Alternative Validation where
  empty = Errors []
  Ok x <|> _ = Ok x
  Errors e1 <|> Ok y = Ok y
  Errors e1 <|> Errors e2 = Errors (e1++e2)
```

这是最后一个示例：验证联系信息，可以是电话数字或电子邮件地址。

``` haskell
data ContactInfo = Email String | Phone String
  deriving Show

validateEmail :: String -> Validation ContactInfo
validateEmail s = check (elem '@' s) "Not an email: should contain a @" (Email s)

checkLength :: String -> Validation ContactInfo
checkLength s = check (length s <= 10) "Not a phone number: should be at most 10 digits" (Phone s)

checkDigits :: String -> Validation ContactInfo
checkDigits s = check (all isDigit s) "Not a phone number: should be all numbers" (Phone s)

validatePhone :: String -> Validation ContactInfo
validatePhone s = checkDigits s *> checkLength s

validateContactInfo :: String -> Validation ContactInfo
validateContactInfo s = validateEmail s <|> validatePhone s
```

``` haskell
validateContactInfo "user@example.com"
  ==> Ok (Email "user@example.com")
validateContactInfo "01234"
  ==> Ok (Phone "01234")
validateContactInfo "01234567890"
  ==> Errors ["Not an email: should contain a @","Not a phone number: should be at most 10 digits"]
validateContactInfo "01234567890x"
  ==> Errors ["Not an email: should contain a @",
              "Not a phone number: should be all numbers",
              "Not a phone number: should be at most 10 digits"]
validateContactInfo "x"
  ==> Errors ["Not an email: should contain a @",
              "Not a phone number: should be all numbers"]
```

请注意，与前面的示例一样，错误是从左到右收集的：来自 `validateEmail` 的错误出现在来自 `validatePhone` 的错误之前。来自 `checkDigits` 的错误先于来自 `checkLength` 的错误。


<a id="sidenote-applicatives-in-context"></a>

## 15.8 附注：语境中的 Applicative

### 15.8.1 为什么是Applicative？

学习 Applicatives 的原因有多种，即使它们不提供任何比 Monad 更强大的函数。首先，正如第 13 讲中所讨论的，GHC 标准库现在强制所有 Monad 的应用实例。因此，一个工作中的 Haskell 程序员一定会看到很多 Applicative 实例。

其次，即使在 Monadic 代码中，你也会经常遇到Applicative 运算符。像 `f <$> x <*> y <*> z` 这样的表达式在许多 Monadic 上下文中都很有用。此外，由于 `Traversable` 类型类是根据 `Applicative` 构建的，因此你经常会对其使用应用操作。

第三，Applicative是理解函数设计模式的绝佳练习。它们将 Functor 模式与 Monoid 模式结合起来，而 Alternative 则带来了另一个类似 Monoid 的维度。能够有效地使用 Applicatives 将使使用 *monad 转换器* 或 *lenses* 等进一步的抽象变得更容易。

最后，有几种类型是 Applicatives 但不是 Monads。 `Validation` 就是一个例子，而且是一个非常实用的例子。如果不了解 Applicative，我们就无法识别和概括此类类型的操作。另一种这样的类型是[`ZipList`](https://hackage.haskell.org/package/base-4.16.4.0/docs/Control-Applicative.html#t:ZipList)。

### 15.8.2 野外应用

尽管我们在本次讲座中只介绍了一些非常简单且具体的 Applicatives，但仍有大量 Haskell 库使用 Applicatives 来完成重要任务。以下是一些示例。

与我们的 `Validation` Applicative相同的想法已在[验证](https://hackage.haskell.org/package/validation) 和[任一](https://hackage.haskell.org/package/either) 库中实现。

有多个使用 Applicatives 的解析器库。例如，[regex-applicative](https://hackage.haskell.org/package/regex-applicative)、[optparse-applicative](https://hackage.haskell.org/package/optparse-applicative)、[yamlparse-applicative](https://cs-syd.eu/posts/2020-06-28-yamlparse-applicative)、[json-stream](https://hackage.haskell.org/package/json-stream) 等。

### 15.8.3 Monad 和Applicative

那么 Monad 和 Applicative 之间有什么关系呢？如果Applicative也是 Monad，则以下定律成立：

``` haskell
pure             === return

fmap             === liftM

fmap f op        === do x <- op
                        return (f x)

liftA2           === liftM2

liftA2 f op1 op2 === do x <- op1
                        y <- op2
                        return (f x y)

op1 *> op2       === op1 >> op2

op1 <*> op2      === do f <- op1
                        x <- op2
                        return (f x)
```

在 `Monad` 中工作时，你可以将 `Applicative` 和 `Functor` 与 `Monad` 操作自由混合。作为一个例子，让我们重写 `mapM` 直到它只使用应用操作，从而得到 `traverse` 的实现。这是我们的出发点：

``` haskell
myMapM op [] = return []
myMapM op (x:xs) = do y <- op x
                      ys <- myMapM op xs
                      return (y:ys)
```

GHCi 告诉我们它只适用于 Monad：

``` haskell
Prelude> :t myMapM
myMapM :: Monad m => (a -> m b) -> [a] -> m [b]
```

让我们应用上面的 `pure === return` 和 `liftA2` 定律：

``` haskell
myMapM op [] = pure []
myMapM op (x:xs) = liftA2 (:) (op x) (myMapM op xs)
```

哒哒！现在 `myMapM` 适用于任何 `Applicative`：

``` haskell
Prelude> :t myMapM
myMapM :: Applicative f => (a -> f b) -> [a] -> f [b]
```


<a id="quiz-5"></a>

## 15.9 测验

`liftA2 (&&) Nothing x`中的`x`是什么类型？

1.`Applicative f => f Bool`
2.`Applicative Bool`
3.`Maybe Bool`

当`xs`和`ys`是列表时，`liftA2 f xs ys`有多少个元素？

1.`length xs + length ys`
2.`length xs * length ys`
3.`min (length xs) (length ys)`

以下哪个表达式相当于 `liftA2 f x y`？可能有多个正确答案。

1.`f <$> x <*> y`
2.`f <*> x <*> y`
3.`f <*> x <$> y`
4.`fmap f x <*> y`
5.`pure f <*> x <*> y`

如果 `f :: a -> Maybe b` 和 `xs :: [a]`，其中哪个表达式的类型与其他表达式不同？

1.`fmap f xs`
2.`traverse f xs`
3.`map f xs`

对于哪个 `Applicative`，表达式 `pure x <* pure y` 和 `pure x <|> pure y` 会产生不同的结果？

1.`Maybe`
2.`[]`
3.`Validation`


<a id="exercises-6"></a>

## 15.10 练习

- [Set15](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set15.hs)
