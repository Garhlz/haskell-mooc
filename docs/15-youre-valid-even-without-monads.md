# 第 15 讲：没有 Monad 也能做验证


## 15.1 Applicative 简介

`Applicative` 位于 `Functor`（能做的事较少）和 `Monad`（能表达依赖前一步结果的程序）之间。选择 `Applicative` 而不是 `Monad` 的原因包括：

- 性能：因为 `Applicative` 允许的操作较少，可以比 `Monad` 优化得更好。
- 简单性：`Applicative` 接口更容易理解。
- 必要性：有时无法定义 `Monad` 实例，但能定义 `Applicative` 实例。

那么什么是 `Applicative` 呢？我们看一个定义。

```haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  -- 暂时省略其他操作
```

所以 `Applicative` 是一种 `Functor`，它允许我们用 `pure` 构造只包含一个值的上下文，并用 `liftA2` 将两个带上下文的值合并成一个。相比普通 Functor，这增加了不少能力。Functor 的计算必然是线性的：`fmap :: (a -> b) -> f a -> f b` 接收一个 Functor 值，输出另一个。相比之下，`pure` 不接收 Functor 值而输出 1 个，`liftA2` 接收 2 个并返回 1 个。

附注：Applicative 术语来自 [Applicative Functor](https://en.wikipedia.org/wiki/Applicative_functor)，听起来像范畴论，但实际上来自编程论文。

现在理论已经够多了。让我们看看用 `Applicative` 运算（和 `fmap`）能表达什么样的计算。我们从 `Maybe` Applicative 开始。这是简化的定义：

```haskell
instance Applicative Maybe where
  pure x = Just x
  liftA2 f (Just x) (Just y) = Just (f x y)
  liftA2 f _        _        = Nothing
```

你会看到这个定义用与 `Monad Maybe` 实例相同的失败传播。让我们在解析货币值时使用它：

```haskell
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

```haskell
parseMoney "123" "€"  ==> Just (Money 123 EUR)
parseMoney "45" "$"   ==> Just (Money 45 USD)
parseMoney "4x" "€"   ==> Nothing
parseMoney "45" "£"   ==> Nothing
```

效果很好。然而，如果我们尝试对此进行扩展，我们很快就会遇到 `Applicative` 的限制。例如，考虑这个 `sumMoney` 函数，该函数对 `Money` 值求和，但如果它们不是相同的货币，则会失败：

```haskell
sumMoney :: Money -> Money -> Maybe Money
sumMoney (Money a c) (Money b c')
    | c == c'   = Just (Money (a+b) c)
    | otherwise = Nothing
```

我们无法使用 `Applicative` 操作将其应用于两个 `Maybe Money` 值。为此，我们需要 `Maybe` monad：

```haskell
example :: Maybe Money
example = do x <- parseMoney "123" "€"
             y <- parseMoney "45" "$"
             sumMoney x y
```

如果尝试使用 `liftA2`，就会得到 `Maybe (Maybe Money)` 类型。此外，根据错误发生的层级不同，现在会出现两种不同形式的失败：`Nothing` 和 `Just Nothing`。这就是明显应该切换到 `Monad` 实例的情况。

    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "€")
      ==> Just (Just (Money 168 EUR))
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "45" "$")
      ==> Just Nothing
    liftA2 sumMoney (parseMoney "123" "e") (parseMoney "xxx" "e")
      ==> Nothing

附注：`liftA2` 这个名字听起来有点麻烦，但它与 monad 的 `liftM`、`liftM2` 等函数进行类比。回想一下，`liftM` 只是 `fmap`，所以也许 `liftA2` 应该被称为 `fmap2`。


## 15.2 列表 Applicative

让我们看看已经见过的另一个 `Functor` 的 Applicative 实例。列表 Functor 的 `Applicative` 实例会遍历所有可能的值组合（就像列表 monad 一样）。示例如下：

```haskell
instance Applicative [] where
  pure x = [x]
  liftA2 f xs ys = [f x y | x <- xs, y <- ys]
```

这是一个示例：生成一些短语。

```haskell
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

```haskell
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


## 15.3 新运算符

有一些非常方便的 Applicative 运算符：`<$>`、`<*>`、`<*` 和 `*>`。

让我们从 `<$>` 开始，它只是 `fmap` 的中缀版本：

```haskell
(<$>) :: Functor f => (a -> b) -> f a -> f b
f <$> x = fmap f x
```

```haskell
not <$> Just True   ==> Just False
not <$> Nothing     ==> Nothing
negate <$> [1,2,3]  ==> [-1,-2,-3]
```

这本身就很好，但是当与这个 Applicative 运算符结合使用时，它真的会大放异彩：

```haskell
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```

这个类型告诉你 `<*>` 的作用：它把 Applicative 里的函数应用到 Applicative 里的值上。下面是一些单独使用 `<*>` 的示例：

```haskell
Just not <*> Just True    ==> Just False
Nothing  <*> Just True    ==> Nothing
Just not <*> Nothing      ==> Nothing
[(+1),(*2)] <*> [10,100]  ==> [11,101,20,200]
```

当我们将 `<$>` 和 `<*>` 结合起来时，真正关键的地方就出现了：我们可以把任意多参数的函数提升到 Applicative 中使用！

```haskell
say :: String -> Int -> String -> String
say x i y = x ++ " has " ++ show i ++ " " ++ y
```

```haskell
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

这是怎么回事？让我们逐步求值。关键是每个 `<*>` 都会给函数部分应用一个参数。

```haskell
    say <$> Just "haskell" <*> Just 99 <*> Just "operators"
=== ((say <$> Just "haskell") <*> Just 99) <*> Just "operators"
=== (fmap say (Just "haskell") <*> Just 99) <*> Just "operators"
==> (Just (say "haskell") <*> Just 99) <*> Just "operators"
==> Just (say "haskell" 99) <*> Just "operators"
==> Just (say "haskell" 99 "operators")
==> Just "haskell has 99 operators"
```

也许查看类型会更清楚：

```haskell
say <$> Just "haskell"                                  :: Maybe (Int -> String -> String)
say <$> Just "haskell" <*> Just 99                      :: Maybe (       String -> String)
say <$> Just "haskell" <*> Just 99 <*> Just "operators" :: Maybe (                 String)
```

接下来的两个运算符稍微简单一些：

```haskell
(*>) :: Applicative f => f a -> f b -> f b
x *> y = liftA2 (\a b -> b) x y

(<*) :: Applicative f => f a -> f b -> f a
x <* y = liftA2 (\a b -> a) x y
```

你可以将这些类型与更熟悉的运算符进行比较：

```haskell
(>>) :: Monad m => m a -> m b -> m b
```

运算符 `<*` 和 `*>` 的含义是：运行这两个操作，但只保留一个结果。箭头指向保留的结果：

```haskell
Just 1 *> Just 2  ==> Just 2
Just 1 <* Just 2  ==> Just 1
Just 1 <* Nothing ==> Nothing
Nothing <* Just 2 ==> Nothing
```

这些运算符可能看起来微不足道，但它们在组合检查时非常有用。例如：

```haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing

small :: Int -> Maybe Int
small i = if i<10 then Just i else Nothing

decreaseSmall :: Int -> Maybe Int
-- 执行 decrease 的行为，但如果 small 失败则失败
decreaseSmall i = decrease i <* small i
```

```haskell
decreaseSmall 4   ==> Just 3
decreaseSmall 0   ==> Nothing
decreaseSmall 11  ==> Nothing
```

现在我们已经了解了所有这些运算符，我们可以理解 `Applicative` 的完整定义。所有运算符都有 `liftA2` 的定义，因此在实现 `Applicative` 实例时定义 `liftA2` 和 `pure` 就足够了。

```haskell
class Functor f => Applicative f where
  pure :: a -> f a
  liftA2 :: (a -> b -> c) -> f a -> f b -> f c
  (<*>) :: f (a -> b) -> f a -> f b
  (*>) :: f a -> f b -> f b
  (<*) :: f a -> f b -> f a
```


## 15.4 `Validation` Applicative

让我们看一个比 Maybe 或列表更有趣的 Applicative。在编程中，我们通常需要“验证”用户的一些输入。在这些情况下，将输入可能存在的所有错误收集在一起很有用。文件 [`exercises/Examples/Validation.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Validation.hs) 实现 `Validation` 数据类型：

```haskell
data Validation a = Ok a | Errors [String]
  deriving (Show,Eq)
```

`Validation` 的 `Applicative` 实例的工作方式如下：

```haskell
liftA2 (+) (Ok 1) (Ok 2)
  ==> Ok 3
liftA2 (+) (Errors ["oh no"]) (Errors ["boom"])
  ==> Errors ["oh no","boom"]
```

请注意，和 `Maybe` Applicative 相比，这里可以保留许多不同的失败信息。

下面是一个完整示例：先介绍几个辅助函数，然后用它们来祝贺某人的生日：

```haskell
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

```haskell
birthday "Guy" 31
  ==> Ok "Happy 31th birthday Guy!"
birthday "Guybrush Threepwood" 31
  ==> Errors ["Name too long"]
birthday "Yog-sothoth" 10000
  ==> Errors ["Name too long","Too old"]
```

哦，对了，这是 `Validation` 的 `Functor` 和 `Applicative` 实例：

```haskell
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

`Validation` 的 `liftA2` 定义表明，错误会从左到右收集起来。上面的示例也能看出这一点：表达式 `liftA2 congratulate checkedName checkedAge` 会先输出来自 `checkedName` 的错误（`"Name too long"`），再输出来自 `checkedAge` 的错误（`"Too old"`）。


## 15.5 验证列表：`traverse`

到目前为止，我们已经处理了固定大小的事物和 Applicative：我们已经将两个或三个参数的函数应用于某些事物。如果我们有任意数量的输入怎么办？如果我们需要验证列表怎么办？

让我们看一下实现这样的函数的一些方法：

```haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

与往常一样，在使用列表时，模式匹配和递归通常是最佳选择。这是一个递归解决方案：

```haskell
allPositive :: [Int] -> Validation [Int]
allPositive [] = Ok []
allPositive (x:xs) = liftA2 (:) checkThis checkRest
  where checkThis = check (x>=0) ("Not positive: "++show x) x
        checkRest = allPositive xs
```

每次都手写这样的递归有点麻烦。如果我们在 `Monad` 中工作，可以使用 `mapM` 这样的辅助函数：

```haskell
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
mapM (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

`Applicative` 的 `mapM` 等效项称为 `traverse`。它是类型类 `Traversable` 的成员：

```haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这个类型签名看起来很吓人，所以让我们稍微简化一下。列表是 `Traversable`，所以可以把这个类型具体化为：

```haskell
traverse :: Applicative f => (a -> f b) -> [a] -> f [b]
```

这看起来正是我们所需要的！对于同样是 Monad 的 Applicatives，`traverse` 只是 `mapM` 的另一个名称：

```haskell
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3]
  ==> Just [1,2,3]
traverse (\x -> if x>=0 then Just x else Nothing) [1,2,3,-4]
  ==> Nothing
```

但对于我们的 `Validation`（不是 `Monad`）来说，`traverse` 正是我们想要的：

```haskell
allPositive :: [Int] -> Validation [Int]
allPositive xs = traverse checkNumber xs
  where checkNumber x = check (x>=0) ("Not positive: "++show x) x
```

```haskell
allPositive [1,2,3]
  ==> Ok [1,2,3]
allPositive [1,2,3,-4]
  ==> Errors ["Not positive: -4"]
allPositive [1,-2,3,-4]
  ==> Errors ["Not positive: -2","Not positive: -4"]
```

请注意 `Validation` 的 `traverse` 如何按照原始列表中出现的顺序将所有错误收集在一起。

P.S. 事实上，`Validation` 是 `Applicative` 不可能是 `Monad` 的少数示例之一。你能弄清楚为什么吗？


## 15.6 附注：`Traversable`

那么 `Traversable` 到底是什么？很多熟悉的结构都是 `Traversable`。下面是一些示例：

```haskell
decrease :: Int -> Maybe Int
decrease i = if i>0 then Just (i-1) else Nothing
```

```haskell
-- 列表是 Traversable
traverse decrease [1,2,3] ==> Just [0,1,2]
traverse decrease [1,0,3] ==> Nothing

-- 数组是 Traversable
traverse decrease (array (1,3) [(1,10),(2,11),(3,12)])
         ==> Just (array (1,3) [(1,9),(2,10),(3,11)])

-- Map 是 Traversable
traverse decrease (M.fromList [("a",1),("b",2)])
         ==> Just (M.fromList [("a",0),("b",1)])
traverse decrease (M.fromList [("a",1),("b",0)])
         ==> Nothing

-- Either 是 Traversable
traverse decrease (Left "abc") ==> Just (Left "abc")
traverse decrease (Right 3)    ==> Just (Right 2)
traverse decrease (Right 0)    ==> Nothing
```

所以 `Traversable` 是各种容器的类型类，有点像 `Foldable`。确实，如果你看一下定义，`Traversable` 是 `Foldable` 的子类。事实证明，`traverse` 和 `mapM` 是该类的方法！

```haskell
class (Functor t, Foldable t) => Traversable t where
  traverse :: Applicative f => (a -> f b) -> t a -> f (t b)
  mapM :: Monad m => (a -> m b) -> t a -> m (t b)
```

这里很容易被类型绕晕。我们回到 `traverse` 的类型：

```haskell
traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)
```

这里我们有两个 Functor：`t` 和 `f`。 `t` Functor 也是 `Foldable` 和 `Traversable`，`f` Functor 也是 `Applicative`。 `traverse` 函数允许我们在 `t` 容器内运行 `f` 操作。

如果这感觉很抽象，请不要担心。实际上，你几乎总是在列表上使用 `traverse`。


## 15.7 处理失败：`Alternative`

如果你稍微尝试一下 Applicative，就会开始注意到它们的能力有一些限制。例如，当我们像 `parseMoney` 示例那样编写解析器时，如果能够尝试几个不同的解析器，并获得任意一个成功结果，那就太好了。对于像 `Maybe` 这样的具体 Applicative 来说，这很容易手写，如下所示。

```haskell
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
-- 优先选择肯定回答！
parseAnswer s = eitherOf (parseYes s) (parseNo s)
```

```haskell
parseAnswer "yes"    ==> Just Yes
parseAnswer "y"      ==> Just Yes
parseAnswer "n"      ==> Just No
parseAnswer "maybe"  ==> Just Yes
parseAnswer "x"      ==> Nothing
```

我们如何概括 `eitherOf`？我们不能给它类型 `Applicative f => f x -> f x -> f x`，因为这样实现就需要有效地类似于 `eitherOf a b = liftA2 something a b`，但是 `eitherOf Nothing (Just x)` 将是 `Nothing`（因为这就是 Applicative 实例的工作方式）！

事实证明，我们需要一个新的类型类：`Alternative`。`Alternative` 在 Applicative 的基础上增加了两个操作：`empty` 表示没有结果，`<|>` 表示合并结果。

```haskell
class Applicative f => Alternative f where
  empty :: f a
  (<|>) :: f a -> f a -> f a
  -- 省略一些其他操作
```

现在我们可以使用通用操作重写我们的解析代码：

```haskell
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

```haskell
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

```haskell
instance Alternative [] where
  empty = []
  (<|>) = (++)

instance Alternative Maybe where
  empty = Nothing
  Just x  <|> _  = Just x
  Nothing <|> mx = mx
```

`Validation` 类型也是 `Alternative`。该实例将所有错误消息收集在一起，就像 `Applicative` 实例一样。

```haskell
instance Alternative Validation where
  empty = Errors []
  Ok x <|> _ = Ok x
  Errors e1 <|> Ok y = Ok y
  Errors e1 <|> Errors e2 = Errors (e1++e2)
```

这是最后一个示例：验证联系信息，可以是电话数字或电子邮件地址。

```haskell
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

```haskell
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


## 15.8 附注：上下文中的 Applicative

### 15.8.1 为什么是 Applicative？

学习 Applicative 有多种原因，即使它并不提供比 Monad 更强大的能力。首先，正如第 13 讲中所讨论的，GHC 标准库现在要求所有 Monad 必须有 Applicative 实例。因此，Haskell 程序员必然会看到很多 Applicative 实例。

其次，即使在 monadic 代码中，你也会经常遇到 Applicative 运算符。像 `f <$> x <*> y <*> z` 这样的表达式在许多 monadic 上下文中都很有用。此外，由于 `Traversable` 类型类建立在 `Applicative` 之上，你也会经常通过它使用 Applicative 操作。

第三，Applicative 是理解函数式设计模式的绝佳练习。它把 Functor 模式和幺半群模式结合在一起，而 Alternative 又带来了另一个类似幺半群的维度。能够有效使用 Applicative，会让你更容易理解 *monad 变换器* 或 *lenses* 等更进一步的抽象。

最后，有几种类型是 Applicative 但不是 Monad。 `Validation` 就是一个示例，而且是一个非常实用的示例。如果不了解 Applicative，我们就无法识别和概括此类类型的操作。另一种这样的类型是[`ZipList`](https://hackage.haskell.org/package/base-4.16.4.0/docs/Control-Applicative.html#t:ZipList)。

### 15.8.2 实际应用

尽管本讲只介绍了一些非常简单且具体的 Applicative，但仍有大量 Haskell 库使用 Applicative 来完成重要任务。下面是一些示例。

和我们的 `Validation` Applicative 相同的想法，已经在 [validation](https://hackage.haskell.org/package/validation) 和 [either](https://hackage.haskell.org/package/either) 库中实现。

有多个使用 Applicative 的解析器库。例如，[regex-applicative](https://hackage.haskell.org/package/regex-applicative)、[optparse-applicative](https://hackage.haskell.org/package/optparse-applicative)、[yamlparse-applicative](https://cs-syd.eu/posts/2020-06-28-yamlparse-applicative)、[JSON-stream](https://hackage.haskell.org/package/JSON-stream) 等。

### 15.8.3 Monad 和 Applicative

那么 Monad 和 Applicative 之间有什么关系呢？如果 Applicative 也是 Monad，则以下定律成立：

```haskell
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

在 `Monad` 中工作时，你可以将 `Applicative` 和 `Functor` 与 `Monad` 操作自由混合。作为一个示例，让我们重写 `mapM` 直到它只使用Applicative 操作，从而得到 `traverse` 的实现。这是我们的出发点：

```haskell
myMapM op [] = return []
myMapM op (x:xs) = do y <- op x
                      ys <- myMapM op xs
                      return (y:ys)
```

GHCi 告诉我们它只适用于 Monad：

```haskell
Prelude> :t myMapM
myMapM :: Monad m => (a -> m b) -> [a] -> m [b]
```

让我们应用上面的 `pure === return` 和 `liftA2` 定律：

```haskell
myMapM op [] = pure []
myMapM op (x:xs) = liftA2 (:) (op x) (myMapM op xs)
```

哒哒！现在 `myMapM` 适用于任何 `Applicative`：

```haskell
Prelude> :t myMapM
myMapM :: Applicative f => (a -> f b) -> [a] -> f [b]
```


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


## 15.10 练习

- [Set15](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set15.hs)
