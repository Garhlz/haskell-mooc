# Haskell 慕课，第 2 部分

- [13 第 13 讲：问题范畴中的幺半群](#lecture-13-a-monoid-in-the-category-of-problems)
  - [13.1 例子 1：Maybe](#example-1-maybes)
  - [13.2 例子 2：日志](#example-2-logging)
  - [13.3 例子 3：维护状态](#example-3-keeping-state)
  - [13.4 终于：Monad 类型类](#finally-the-monad-type-class)
  - [13.5 Maybe 是 Monad！](#maybe-is-a-monad)
  - [13.6 `do` 的回归](#the-return-of-do)
  - [13.7 Logger 是 Monad！](#logger-is-a-monad)
  - [13.8 State Monad](#the-state-monad)
  - [13.9 `mapM` 的回归](#the-return-of-mapm)
  - [13.10 Monad 是 Functor](#monads-are-functors)
  - [13.11 又一个 Monad](#one-more-monad)
  - [13.12 哦对，IO](#oh-right-io)
  - [13.13 其他语言中的 Monad](#monads-in-other-languages)
  - [13.14 Monad：总结](#monads-wrap-up)
  - [13.15 附注：标准 Haskell](#sidenote-standard-haskell)
  - [13.16 测验](#quiz-4)
  - [13.17 练习](#exercises-4)


<a id="lecture-13-a-monoid-in-the-category-of-problems"></a>

# 13 第 13 讲：问题范畴中的幺半群

- Monad

在本次讲座中，我们将使用许多例子来构建 *monad* 的概念。现在你应该熟悉理解 Monad 所需的所有 Haskell 函数。

Monad 是编程中众所周知的难题，部分是因为术语陌生，部分是因为教程质量参差不齐，部分是因为在学习 Haskell 时过早理解 Monad。在课程后期引入 Monad 是为了让理解更容易。

如果你觉得本讲很难，不要绝望，许多人也觉得这个话题很难。许多高效的 Haskell 程序员已经成功理解了 Monad，所以这不是不可能的。

最后要注意的是：Monad 和 Functor 一样，是来自范畴论这个数学分支的概念。然而，*你不需要了解范畴论来理解 Haskell 编程中的 Monad*。 就像人们可以在不了解类型论的情况下使用 OOP 或函数式编程一样，可以在不了解范畴论的情况下使用 Monad。对于函数式程序员来说，范畴论可能很有益，但不是必需的。


<a id="example-1-maybes"></a>

## 13.1 例子 1：Maybe

使用许多 `Maybe` 值时，代码往往会变得有点混乱。让我们看一些例子。首先，我们组合一些返回 `Maybe String` 的函数。注意 `stealSecret` 中需要的嵌套 `case`：写起来并不有趣。

``` haskell
-- Try to login with a password.
-- `Just username` on success, `Nothing` otherwise.
login :: String -> Maybe String
login "f4bulous!" = Just "unicorn73"
login "swordfish" = Just "megahacker"
login _           = Nothing

-- Get a secret associated with a user.
-- Not all users have secrets.
secret :: String -> Maybe String
secret "megahacker" = Just "I like roses"
secret _            = Nothing

-- Login and return the user's secret, if any
stealSecret :: String -> Maybe String
stealSecret password =
  case login password of
    Nothing -> Nothing
    Just user -> case secret user of
                   Nothing -> Nothing
                   Just s -> Just ("Stole secret: "++s)
```

``` haskell
stealSecret "swordfish"  ==>  Just "Stole secret: I like roses"
stealSecret "f4bulous!"  ==>  Nothing
stealSecret "wrong_password"  ==>  Nothing
```

接下来，我们看一个列表的例子。我们使用 Prelude 中的 `Maybe` 返回函数 `lookup`。这里我们有一个 if 位于一个 case 中，而不是一个嵌套的 case。

``` haskell
-- Get the value corresponding to a key from a key-value list.
lookup :: (Eq a) => a -> [(a, b)] -> Maybe b
```

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  case lookup key assocs
  of Nothing -> Nothing
     Just x -> if (val < x)
                then Nothing
                else Just ((key,val) : delete (key,x) assocs)
```

这种类型的代码非常常见，并且通常重复相同的模式：如果任何中间结果是 `Nothing`，则整个结果是 `Nothing`。让我们尝试通过定义一个*链接运算符* `?>` 来更轻松地编写这样的代码。链接运算符获取结果和下一步计算，并且仅当结果是 `Just` 值时才运行下一步。

``` haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- if we failed, don't even bother running the next step:
Nothing ?> _ = Nothing
-- otherwise run the next step:
Just x  ?> f = f x
```

链接运算符很好地简化了我们的例子。请注意我们如何定义简单的辅助函数来处理计算的一步，而不是编写一个大表达式。

``` haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password ?>
    secret ?>
    decorate
  where decorate s = Just ("Stole secret: "++s)
```

``` haskell
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
    lookup key assocs ?>
    check ?>
    buildResult
  where check x
           | val < x   = Nothing
           | otherwise = Just x
        buildResult x = Just ((key,val) : delete (key,x) assocs)
```

这是另一个例子：从 `safeHead` 和 `safeTail` 构建的安全列表索引：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x

safeTail :: [a] -> Maybe [a]
safeTail [] = Nothing
safeTail (x:xs) = Just xs

safeThird :: [a] -> Maybe a
safeThird xs = safeTail xs ?> safeTail ?> safeHead

safeNth :: Int -> [a] -> Maybe a
safeNth 0 xs = safeHead xs
safeNth n xs = safeTail xs ?> safeNth (n-1)
```

``` haskell
safeThird [1,2,3,4]
  ==> Just 3
safeThird [1,2]
  ==> Nothing
safeNth 5 [1..10]
  ==> Just 6
safeNth 11 [1..10]
  ==> Nothing
```

P.S. 请注意，`?>` *关联到左侧*，这是 Haskell 中的默认值。这意味着 `op ?> f ?> g` 意味着 `(op ?> f) ?> g`。另一种选择是，`op ?> (f ?> g)` 甚至不会进行类型检查！

附注：这个 `?>` 运算符表达了在其他语言中非常常见的 if-result 模式。以下是用 Python 和 Java 编写 `op val ?> f` 的方法。

``` python
# Python
x = op(val)
if x:
  f(x)
```

``` java
// Java
Object x = op(val);
if (x != null) {
  f(x);
}
```

if-result 模式和 `?>` 之间的区别在于，我们使用 `Nothing` 值来显式表示失败，而不是依赖于 Python 中任何变量都可以是 `None`（或 `False`）这一事实，或者 Java 中任何 `Object` 引用都可以是 `null` 的事实。


<a id="example-2-logging"></a>

## 13.2 例子 2：日志

让我们用另一个例子来探讨链接的概念：日志记录。类型 `Logger` 表示一个值加上一个日志消息列表（由生成该值的计算生成）。

``` haskell
-- Logger definition
data Logger a = Logger [String] a  deriving Show

getVal :: Logger a -> a
getVal (Logger _ a) = a
getLog :: Logger a -> [String]
getLog (Logger s _) = s

-- Primitive operations:
nomsg :: a -> Logger a
nomsg x = Logger [] x        -- a value, no message

annotate :: String -> a -> Logger a
annotate s x = Logger [s] x  -- a value and a message

msg :: String -> Logger ()
msg s = Logger [s] ()        -- just a message
```

这是一个 `login` 函数，用于记录有关其处理的用户名和密码的一些详细信息。请注意，当我们需要处理多个 `Logger` 值时，我们如何在 `login` 中遇到复杂的代码。

``` haskell
validateUser :: String -> Logger Bool
validateUser "paul.atreides" = annotate "Valid user" True
validateUser "ninja" = nomsg True
validateUser u = annotate ("Invalid user: "++u) False

checkPassword :: String -> String -> Logger Bool
checkPassword "paul.atreides" "muad'dib" = annotate "Password ok" True
checkPassword "ninja"         ""         = annotate "Password ok" True
checkPassword _               pass       = annotate ("Password wrong: "++pass) False

login :: String -> String -> Logger Bool
login user password =
  let validation = validateUser user
  in if (getVal validation)
       then let check = checkPassword user password
            in Logger (getLog validation ++ getLog check) (getVal check)
       else validation
```

``` haskell
login "paul.atreides" "muad'dib"
  ==> Logger ["Valid user","Password ok"] True
login "paul.atreides" "arrakis"
  ==> Logger ["Valid user","Password wrong: arrakis"] False
login "ninja" ""
  ==> Logger ["Password ok"] True
login "leto.atreides" "paul"
  ==> Logger ["Invalid user: leto.atreides"] False
```

让我们尝试通过为 `Logger` 定义链接运算符来简化此代码。进行多个 `Logger` 操作时，重要的是保留所有日志。这是一个链接运算符 `#>`，以及如何使用它来记录一些算术计算的例子。

``` haskell
(#>) :: Logger a -> (a -> Logger b) -> Logger b
Logger la a #> f = let Logger lb b = f a  -- feed value to next step
                   in Logger (la++lb) b   -- bundle result with all messages
```

``` haskell
-- square a number and log a message about it
square :: Int -> Logger Int
square val = annotate (show val ++ "^2") (val^2)

-- add 1 to a number and log a message about it
add :: Int -> Logger Int
add val = annotate (show val ++ "+1") (val+1)

-- double a number and log a message about it
double :: Int -> Logger Int
double val = annotate (show val ++ "*2") (val*2)

-- compute the expression 2*(x^2+1) with logging
compute :: Int -> Logger Int
compute x =
    square x
    #> add
    #> double
```

``` haskell
compute 3
  ==> Logger ["3^2","9+1","10*2"] 20
```

通过使用 `#>`，我们可以大大简化 `login`。请注意，我们不需要担心将日志组合在一起。另请注意我们如何使用 lambda 表达式而不是定义辅助函数。

``` haskell
login :: String -> String -> Logger Bool
login user password =
  validateUser user
  #>
  \valid -> if valid then checkPassword user password
                     else nomsg False
```

为了进一步演练，让我们在递归列表处理函数中使用 `Logger`。这是 `filter` 的日志版本。请注意代码如何在递归调用之前链接日志消息，以保持日志条目的良好顺序。

``` haskell
-- sometimes you don't need the previous value:
(##>) :: Logger a -> Logger b -> Logger b
Logger la _ ##> Logger lb b = Logger (la++lb) b

filterLog :: (Eq a, Show a) => (a -> Bool) -> [a] -> Logger [a]
filterLog f [] = nomsg []
filterLog f (x:xs)
   | f x       = msg ("keeping "++show x) ##> filterLog f xs #> (\xs' -> nomsg (x:xs'))
   | otherwise = msg ("dropping "++show x) ##> filterLog f xs
```

``` haskell
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```


<a id="example-3-keeping-state"></a>

## 13.3 例子 3：维护状态

在前面的例子中，我们只是写入了一些状态（日志）。有时我们需要改变某种共享状态的计算。让我们看看一家小银行的账户。我们首先为银行状态定义一个数据类型：所有帐户的余额，作为从帐户名称到余额的映射。

``` haskell
import qualified Data.Map as Map

data Bank = Bank (Map.Map String Int)
  deriving Show
```

以下是我们如何将钱存入帐户的方法。我们使用 `Data.Map` 中的函数 `adjust` 来修改地图。

``` haskell
-- Apply a function to one value in a map
Map.adjust :: Ord k => (a -> a) -> k -> Map.Map k a -> Map.Map k a
```

``` haskell
deposit :: String -> Int -> Bank -> Bank
deposit accountName amount (Bank accounts) =
  Bank (Map.adjust (\x -> x+amount) accountName accounts)
```

提款有点复杂，因为我们要处理一些特殊情况，比如账户不存在，或者账户没有足够的钱。我们使用库函数 `findWithDefault` 来帮助我们。

``` haskell
-- Fetch the value corresponding to a key from a map,
-- or a default value in case the key does not exist
Map.findWithDefault :: Ord k => a -> k -> Map.Map k a -> a
```

``` haskell
withdraw :: String -> Int -> Bank -> (Int,Bank)
withdraw accountName amount (Bank accounts) =
  let -- balance is 0 for a nonexistant account
      balance = Map.findWithDefault 0 accountName accounts
      -- can't withdraw over balance
      withdrawal = min amount balance
      newAccounts = Map.adjust (\x -> x-withdrawal) accountName accounts
  in (withdrawal, Bank newAccounts)
```

最后，让我们编写一个函数，从一个账户中提取最多 100 块钱，将钱分成两半，然后存入两个账户。请注意我们需要多么小心地依次传递银行的不同版本 `bank`、`bank1`、`bank2` 和 `bank3`，以确保所有交易以正确的顺序发生。

``` haskell
share :: String -> String -> String -> Bank -> Bank
share from to1 to2 bank =
  let (amount,bank1) = withdraw from 100 bank
      half = div amount 2
      -- carefully preserve all money, even if amount was an odd number
      rest = amount-half
      bank2 = deposit to1 half bank1
      bank3 = deposit to2 rest bank2
  in bank3
```

``` haskell
share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
   ==> Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)])

share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
   ==> Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)])
```

当你对一个值进行串行更新，同时还执行一些其他计算时，这样的代码经常出现在 Haskell 中。很容易犯错误，并且类型系统不会帮助你，例如， 重用 `bank1` 值。让我们重写 `share` ，这样我们就不需要引用银行本身了。我们可以再次使用相同的链接思想来完成此任务。

``` haskell
-- `BankOp a` is an operation that transforms a Bank value,
-- while returning a value of type `a`
data BankOp a = BankOp (Bank -> (a,Bank))

-- running a BankOp on a Bank
runBankOp :: BankOp a -> Bank -> (a,Bank)
runBankOp (BankOp f) bank = f bank

-- Running one BankOp after another
(+>>) :: BankOp a -> BankOp b -> BankOp b
op1 +>> op2 = BankOp combined
  where combined bank = let (_,bank1) = runBankOp op1 bank
                        in runBankOp op2 bank1

-- Running a parameterized BankOp, using the value returned
-- by a previous BankOp.  The implementation is a bit tricky
-- but it's enough to understand how +> is used for now.
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
op +> parameterized = BankOp combined
  where combined bank = let (a,bank1) = runBankOp op bank
                        in runBankOp (parameterized a) bank1

-- Make a BankOp out of deposit.
-- There is no return value so we use ().
depositOp :: String -> Int -> BankOp ()
depositOp accountName amount = BankOp depositHelper
  where depositHelper bank = ((), deposit accountName amount bank)

-- Make a BankOp out of withdraw. Note how
--   withdraw accountName amount :: Bank -> (Int,Bank)
-- is almost a BankOp already!
withdrawOp :: String -> Int -> BankOp Int
withdrawOp accountName amount = BankOp (withdraw accountName amount)
```

让我们看看连锁如何与这些银行业务配合使用。

``` haskell
Prelude> bank = Bank (Map.fromList [("edsger",10),("grace",50)])

-- Running a number of operations using +>>

Prelude> runBankOp (depositOp "edsger" 1) bank
((),Bank (fromList [("edsger",11),("grace",50)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1) bank
((),Bank (fromList [("edsger",11),("grace",51)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1 +>> withdrawOp "edsger" 11) bank
(11,Bank (fromList [("edsger",0),("grace",51)]))

-- Using +> to implement a transfer from one account to the other:

Prelude> runBankOp (withdrawOp "edsger" 5 +> depositOp "grace") bank
((),Bank (fromList [("edsger",5),("grace",55)]))

Prelude> runBankOp (withdrawOp "edsger" 100 +> depositOp "grace") bank
((),Bank (fromList [("edsger",0),("grace",60)]))
```

请注意 `BankOp` 类型的值如何表示转换银行的过程。银行的初始状态必须使用 `runBankOp` 提供。这是有道理的，因为与 `Bank` 状态不同，`BankOp` 变换可以组合。必须使用 `runBankOp` 使得“定义”操作和“执行它们”之间的区别更加清晰。

现在我们已经熟悉了 `BankOp` 值的操作，我们可以将 `share` 实现为 `BankOp`。我们实现了一个助手 `distributeOp` 以使代码更加整洁。

``` haskell
-- distribute amount to two accounts
distributeOp :: String -> String -> Int -> BankOp ()
distributeOp to1 to2 amount =
  depositOp to1 half
  +>>
  depositOp to2 rest
  where half = div amount 2
        rest = amount - half

shareOp :: String -> String -> String -> BankOp ()
shareOp from to1 to2 =
  withdrawOp from 100
  +>
  distributeOp to1 to2
```

``` haskell
runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
  ==> ((),Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)]))

runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
  ==> ((),Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)]))
```

那很干净不是吗？我们根本不需要提及银行，我们几乎可以像使用命令式语言一样进行编程，同时保持完全纯性。

你可以在课程仓库的 [`exercises/Examples/Bank.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Bank.hs) 下找到所有这些代码。


<a id="finally-the-monad-type-class"></a>

## 13.4 终于：Monad 类型类

我们现在已经看到了三种具有链接操作的不同类型：

``` haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
(#>) :: Logger a -> (a -> Logger b) -> Logger b
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
```

就像之前的 `map` 和 `Functor` 一样，有一个类型类可以捕获此模式。请注意，`Monad` 是关于*类型构造函数*的类型类，就像 `Functor` 一样。

``` haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
```

`Monad` 中还有一些额外的操作：

``` haskell
  -- lift a normal value into the monad
  return :: a -> m a
  -- simpler chaining (like our ##>)
  (>>) :: m a -> m b -> m b
  a >> b  =  a >>= \_ -> b     -- remember: _ means ignored argument
```

回想一下，`Functor` 类型类是关于通用 `map` 操作的。类似地，`Monad` 类型类只是一个通用的链接操作。

``` haskell
fmap :: Functor f => (a->b) -> f a -> f b
(>>=) :: Monad m => m a -> (a -> m b) -> m b
```

表达式 `operation >>= next` 采用一元运算 `operation :: m a`，并使用 `next :: a -> m b` 生成的值进行一些进一步的计算。如果这感觉太抽象，只需回想一下 `Maybe` 的链接是如何工作的：

``` haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- if we failed, don't even bother running the next step
Nothing >>= _ = Nothing
-- otherwise run the next step
Just x  >>= f = f x
```


<a id="maybe-is-a-monad"></a>

## 13.5 Maybe 是 Monad！

这是 `Maybe` 的完整 `Monad` 实例和一些例子。

``` haskell
instance  Monad Maybe  where
    (Just x) >>= k      = k x
    Nothing  >>= _      = Nothing

    (Just _) >>  k      = k
    Nothing  >>  _      = Nothing

    return x            = Just x
```

``` haskell
Just 1 >>= \x -> return (x+1)
  ==> Just 2
Just "HELLO" >>= (\x -> return (length x)) >>= (\x -> return (x+1))
  ==> Just 6
Just "HELLO" >>= \x -> Nothing
  ==> Nothing
Just "HELLO" >> Just 2
  ==> Just 2
Just 2 >> Nothing
  ==> Nothing
```

以下是用 monad 操作重写的 `stealSecret` 和 `increase` 例子。将 `?>` 改为 `>>=`，将 `Just` 改为 `return`。

``` haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password >>=
    secret >>=
    decorate
  where decorate s = return ("Stole secret: "++s)
```

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
    lookup key assocs >>=
    check >>=
    buildResult
  where check x
           | val < x   = Nothing
           | otherwise = return x
        buildResult x = return ((key,val) : delete (key,x) assocs)
```


<a id="the-return-of-do"></a>

## 13.6 `do` 的回归

下面是一个复杂 monad 操作的例子。

``` haskell
f = op1 >>= continue
  where continue  x   = op2 >> op3 >>= continue2 x
        continue2 x y = op4 >> op5 x y
```

让我们看看稍微改变一下这段代码会发生什么。首先，让我们内联定义。

``` haskell
f = op1 >>= (\x ->
               op2 >>
               op3 >>= (\y ->
                          op4 >>
                          op5 x y))
```

由于 lambda 表达式一直持续到表达式末尾，因此我们可以省略括号。我们也以不同的方式缩进。

``` haskell
f = op1 >>= \x ->
    op2 >>
    op3 >>= \y ->
    op4 >>
    op5 x y
```

现在我们可以注意到与 `do` 表示法的相似性。下面的 `do` 块实际上是相同的代码！

``` haskell
f = do x <- op1
       op2
       y <- op3
       op4
       op5 x y
```

澄清一下，`do` 表示法只是 monad 操作（`>>=` 和 `>>`）和 lambda 的更好语法。以下是 do 表示法如何转换为 monad 操作。注意！该转换定义是递归的。

``` haskell
do x <- op a       ~~~>       op a >>= \x -> do ...
   ...
```

``` haskell
do op a            ~~~>       op a >> do ...
   ...
```

``` haskell
do let x = expr    ~~~>       let x = expr in do ...
   ...
```

``` haskell
do finalOp         ~~~>       finalOp
```

这是使用 do 表示法的 `safeNth`：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x

safeTail :: [a] -> Maybe [a]
safeTail [] = Nothing
safeTail (x:xs) = Just xs

safeNth :: Int -> [a] -> Maybe a
safeNth 0 xs = safeHead xs
safeNth n xs = do t <- safeTail xs
                  safeNth (n-1) t
```

这是 `increase` 最后一次，现在用 do 符号

``` haskell
-- Set the value of key to val in the given key-value list,
-- but only if val is larger than the current value!
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  do oldVal <- lookup key assocs
     check oldVal
     return ((key,val) : delete (key,oldVal) assocs)
  where check x
           | val < x   = Nothing
           | otherwise = return x
```


<a id="logger-is-a-monad"></a>

## 13.7 Logger 是 Monad！

我们应该能够通过将 `>>=` 设置为 `#>` 自己为 `Logger` 编写 `Monad` 实例。然而，由于[Haskell 语言的最新变化](https://wiki.haskell.org/Functor-Applicative-Monad_Proposal)，我们必须实现 `Functor` 和 `Applicative` 实例才能实现 `Monad` 实例。 `Functor` 我们已经见过了，但是`Applicative`是什么？我们稍后会介绍。让我们来实现实例：

``` haskell
import Control.Monad

data Logger a = Logger [String] a  deriving Show

msg :: String -> Logger ()
msg s = Logger [s] ()

-- The Functor instance just maps over the stored value
instance Functor Logger where
  fmap f (Logger log x) = Logger log (f x)

-- This is an Applicative instance that works for any
-- monad, you can just ignore it for now. We'll get back
-- to Applicative later.
instance Applicative Logger where
  pure = return
  (<*>) = ap

-- Finally, the Monad instance
instance Monad Logger where
  return x = Logger [] x
  Logger la a >>= f = Logger (la++lb) b
    where Logger lb b = f a
```

我们不再需要 `nomsg` 操作，因为它只是 `return`。我们还可以使用 monad 操作重新实现 `annotate` 操作。

``` haskell
nomsg :: a -> Logger a
nomsg x = return x

annotate :: String -> a -> Logger a
annotate s x = msg s >> return x
```

以下是使用 do 表示法重写的 `compute` 和 `filterLog` 例子。请注意 `filterLog` 与 do 表示法的配合有多好。

``` haskell
compute x = do
  a <- annotate "^2" (x*x)
  b <- annotate "+1" (a+1)
  annotate "*2" (b*2)

filterLog :: (Show a) => (a -> Bool) -> [a] -> Logger [a]
filterLog f [] = return []
filterLog f (x:xs)
   | f x       = do msg ("keeping "++show x)
                    xs' <- filterLog f xs
                    return (x:xs')
   | otherwise = do msg ("dropping "++show x)
                    filterLog f xs
```

``` haskell
compute 3
  ==> Logger ["^2","+1","*2"] 20
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```


<a id="the-state-monad"></a>

## 13.8 State Monad

Haskell 的 `State` monad 是我们的 `BankOp` 类型的通用版本。 `State` 类型由两种类型参数化，第一个是状态类型，第二个是生成值的类型。 `State Bank a` 相当于我们的 `BankOp a`。你可以在[`transformers`包的模块`Control.Monad.Trans.State`](https://downloads.haskell.org/~ghc/latest/docs/html/libraries/transformers-0.5.6.2/Control-Monad-Trans-State.html)中找到`State` monad。这是 `State` 的简化实现。

``` haskell
data State s a = State (s -> (a,s))

runState (State f) s = f s

-- operation that overwrites the state (and produces ())
put :: s -> State s ()
put state = State (\oldState -> ((),state))

-- operation that produces the current state
get :: State s s
get = State (\state -> (state,state))

-- operation that modifies the current state with a function (and produces ())
modify :: (s -> s) -> State s ()
modify f = State (\state -> ((), f state))

-- Functor and Applicative instances skipped

instance Monad (State s) where
  return x = State (\s -> (x,s))

  op >>= f = State h
    where h state0 = let (val,state1) = runState op state0
                         op2 = f val
                     in runState op2 state1
```

注意我们如何声明一个实例 `Monad (State s)`。我们使用“部分应用的类型构造函数”，因为 `Monad` 的实例只能为多采用一个类型参数的类型构造函数声明。如果你看看下面的 `>>=` 类型中 `m`、`Maybe` 和 `State` 是如何出现的，这可能会更清楚一些。

``` haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b

instance Monad Maybe where
  (>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b

instance Monad (State s) where
  (>>=) :: State s a -> (a -> State s b) -> State s b
```

让我们看一下使用 `State` 的一些例子。首先，让我们考虑 `State Int a` 类型的计算，它代表使用简单的计数器。

``` haskell
-- adds i to the value of the counter
add :: Int -> State Int ()
add i = do old <- get
           put (old+i)
```

``` haskell
runState (add 1 >> add 3 >> add 5 >> add 6) 0
  ==> ((),15)
```

``` haskell
example :: State Int Int
example = do add 3           -- increment state by 3
             value <- get    -- value is current state, i.e. initial+3
             add 1000        -- increment state by 1000
             put (value + 1) -- overwrite state with value+1, i.e. initial+4
             return value    -- produce value, i.e. intial+3
```

``` haskell
runState example 1
  ==> (4,5)           -- initial is 1, state is initial+4=5, produces initial+3=4
```

请注意 `State s a` 类型的值如何表示转换状态的过程（就像 `BankOp` 一样）。必须使用 `runState` 提供初始状态。同样，必须使用 `runState` 使得“定义”操作和“执行它们”之间的区别更加清晰。

处理列表时，状态可以替换累加器参数。下面是两个例子：查找列表中最大的元素，以及查找列表中紧接在 `0` 之后出现的值。

``` haskell
findLargest :: Ord a => [a] -> State a ()
findLargest [] = return ()
findLargest (x:xs) = do
  modify (\y -> max x y)  -- update state with max of current value and previous largest value
  findLargest xs          -- process rest of list
```

``` haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

``` haskell
-- store the given value in the state list
remember :: a -> State [a] ()
remember x = modify (x:)

valuesAfterZero :: [Int] -> ((),[Int])
valuesAfterZero xs = runState (go xs) []
  where go :: [Int] -> State [Int] ()
        go (0:y:xs) = do remember y
                         go (y:xs)
        go (x:xs) = go xs
        go [] = return ()
```

``` haskell
valuesAfterZero [0,1,2,3,0,4,0,5,0,0,6]
  ==> ((),[6,0,5,4,1])
```

**注意！** 顺便说一句，`State` monad 的实际实现没有像我们的简化例子那样的 `State` 构造函数。如果你想将函数包装到 `State` 操作中，请改用此帮助程序：

    state :: (s -> (a, s)) -> State s a


<a id="the-return-of-mapm"></a>

## 13.9 `mapM` 的回归

IO 讲座中的控制结构适用于*所有 monad*。这是它们的实际类型。

``` haskell
when :: Monad m => Bool -> m () -> m ()        -- conditional operation
unless :: Monad m => Bool -> m () -> m ()      -- same, but condition is flipped
replicateM :: Monad m => Int -> m a -> m [a]   -- do something many times
replicateM_ :: Monad m => Int -> m a -> m ()   -- same, but ignore the results
mapM :: Monad m => (a -> m b) -> [a] -> m [b]  -- do something on a list's elements
mapM_ :: Monad m => (a -> m b) -> [a] -> m ()  -- same, but ignore the results
forM  :: Monad m => [a] -> (a -> m b) -> m [b] -- mapM but arguments reversed
forM_ :: Monad m => [a] -> (a -> m b) -> m ()  -- same, but ignore the results
```

正如我们在这里看到的，我们可以在迄今为止遇到的所有 monad 上使用 `mapM`：

``` haskell
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,2,3]  ==>  Just [0,1,2]
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,0,3]  ==>  Nothing

mapM (\x -> msg "increment" >> msg (show x) >> return (x+1)) [1,2,3]
  ==> Logger ["increment","1","increment","2","increment","3"] [2,3,4]

runState (mapM (\x -> modify (x+) >> return (x+1)) [1,2,3]) 0
  ==> ([2,3,4],6)
```

更多例子：

``` haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x
firsts :: [[a]] -> Maybe [a]
firsts xs = forM xs safeHead
```

``` haskell
firsts [[1,2,3],[4,5],[6]] ==> Just [1,4,6]
firsts [[1,2,3],[],[6]]    ==> Nothing
```

``` haskell
-- an abbreviated version of an example from the last section
findLargest :: Ord a => [a] -> State a ()
findLargest xs = mapM_ update xs
  where update x = modify (\y -> max x y)
```

``` haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

``` haskell
let increment = modify (+1) >> get
    ops = replicateM 4 increment
in runState ops 0
  ==> ([1,2,3,4],4)
```

这是使用 `State` monad 重新实现的 `filter`：

``` haskell
rememberElements :: (a -> Bool) -> [a] -> State [a] ()
rememberElements f xs = mapM_ maybePut xs
  where maybePut x = when (f x) (modify (++[x]))

sfilter :: (a -> Bool) -> [a] -> [a]
sfilter f xs = finalState
  where (_, finalState) = runState (rememberElements f xs) []
```

``` haskell
sfilter even [1,2,3,4,5]
  ==> [2,4]
```

我们可以编写自己的适用于所有 monad 的操作。正如我们之前所见，这是通过类型类实现的。如果你仅使用 `return` 和 do-notation 等 monad 操作，类型系统将为你的函数推断出通用类型。

``` haskell
mywhen b op = if b then op else return ()

mymapM_ op [] = return ()
mymapM_ op (x:xs) = do op x
                       mymapM_ op xs
```

``` haskell
*Main> :t mywhen
mywhen :: (Monad m) => Bool -> m () -> m ()
*Main> :t mymapM_
mymapM_ :: (Monad m) => (t -> m a) -> [t] -> m ()
```

我们可以在每个例子 monad 中使用这些通用操作：

``` haskell
perhapsDecrease :: Int -> Maybe Int
perhapsDecrease x = do
  mywhen (x<=0) Nothing
  return (x-1)
```

``` haskell
perhapsDecrease 2  ==>  Just 1
perhapsDecrease 0  ==>  Nothing
```

``` haskell
search :: (Show a, Eq a) => a -> [a] -> Logger ()
search x ys = mymapM_ look ys
  where look y = mywhen (x==y) (msg ("Found "++show y))
```

``` haskell
search 3 [1,2,3,4,3,2]  ==>  Logger ["Found 3","Found 3"] ()
```

``` haskell
sumPositive :: [Int] -> State Int ()
sumPositive xs = mymapM_ f xs
  where f x = when (x>0) (modify (x+))
```

``` haskell
runState (sumPositive [1,-4,2,3]) 0  ==>  ((),6)
```


<a id="monads-are-functors"></a>

## 13.10 Monad 是 Functor

尚未引入一项有用的操作：`liftM`。

``` haskell
liftM :: Monad m => (a->b) -> m a -> m b
liftM f op = do x <- op
                return (f x)
```

`liftM` 操作让将纯函数代码与一元操作混合编写变得容易。

``` haskell
liftM negate (Just 3)
  ==> Just (-3)

liftM sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (liftM negate get) 3
  ==> (-3,3)
```

`liftM` 的类型是不是很眼熟？就像`fmap`的类型一样！事实上，为 monad 定义 Functor 实例很容易：只需设置 `fmap = liftM` 即可。由于如今每个 `Monad` 都需要是 `Functor`，因此现代 Haskell 风格更喜欢 `fmap` 而不是 `liftM`。

``` haskell
fmap :: Functor f => (a->b) -> f a -> f b
```

``` haskell
fmap negate (Just 3)
  ==> Just (-3)

fmap sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (fmap negate get) 3
  ==> (-3,3)
```


<a id="one-more-monad"></a>

## 13.11 又一个 Monad

*列表 monad*（即 `[]` 的 `Monad` 实例）表示具有*多个返回值*的计算。它对于搜索替代方案很有用。这是第一个例子。对于每个 `x`，我们都生产 `x` 和 `-x`：

``` haskell
[1,2,3] >>= \x -> [-x,x]
  ==> [-1,1,-2,2,-3,3]
```

我们可以通过生成一个空列表来过滤掉不合适的值：

``` haskell
[1,2,3] >>= \x -> if x>1 then [x] else []
  ==> [2,3]
```

如果我们使用 do 表示法，列表 monad 开始看起来更像是一个循环结构：

``` haskell
do word <- ["Blue", "Green"]
   number <- [1,2,3]
   return (word ++ show number)
  ==> ["Blue1","Blue2","Blue3","Green1","Green2","Green3"]
```

更有趣的例子：找到列表中总和为 `k` 的所有对。 （同一元素两次算作一对。）

``` haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = do a <- xs
                  b <- xs
                  if (a+b==k) then [(a,b)] else []
```

``` haskell
findSum [1,2,3,4,5] 5
  ==> [(1,4),(2,3),(3,2),(4,1)]
```

最后一个更复杂的例子。我们使用列表 monad 从字符串中查找所有回文，然后找到最长的一个。

``` haskell
import Data.List (sortBy)

substrings :: String -> [String]
substrings xs = do start <- [0..length xs - 1]
                   end <- [start+1..length xs - 1]
                   return $ drop start $ take end $ xs

palindromesIn :: String -> [String]
palindromesIn xs = do s <- substrings xs
                      if (s==reverse s) then return s else []

longestPalindrome xs = head . sortBy f $ palindromesIn xs
  where f s s' = compare (length s') (length s)  -- longer is smaller
```

``` haskell
palindromesIn "aabbacddcaca"
  ==> ["a","aa","a","abba","b","bb","b","a","acddca","c","cddc","d","dd","d","c","cac","a","c"]
longestPalindrome "aabbacddcaca"
  ==> "acddca"
```

这是列表 monad 的令人惊讶的简单实现：

``` haskell
instance Monad [] where
  return x = [x]                  -- an operation that produces one value
  lis >>= f = concat (map f lis)  -- compute f for all values, combine the results
```

实际上，我们之前已经以列表推导的形式见过列表 Monad。将 `findSum` 的重新实现与使用 `do` 表示法的早期实现进行比较。

``` haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = [(a,b) | a <- xs, b <- xs, a+b==k ]
```


<a id="oh-right-io"></a>

## 13.12 哦对，IO

你现在可能已经猜到了，`IO` 是一个 monad。然而，`IO` 类型和 `instance Monad IO` 的实现是编译器内置的。你无法仅使用标准 Haskell 来实现 IO monad，这与 `Maybe` monad、`State` monad 和我们见过的其他 monad 不同。

然而，真正的副作用符合 monad 模式，就像 `State` 和 `Maybe` 一样。就像其他 monad 一样，我们将“操作的纯定义”与“运行操作”的过程分开。作为奖励，你可以将所有通用 monad 操作（`mapM` 等）与 IO 一起使用。

下面是一些使用 monad 操作编写 IO 的例子。

``` haskell
printTwoThings :: IO ()
printTwoThings = putStrLn "One!" >> print 2

echo :: IO ()
echo = getLine >>= putStrLn

verboseEcho :: IO ()
verboseEcho = getLine >>= \s -> putStrLn ("You wrote: " ++ s)

query :: String -> IO String
query question = putStrLn question >> getLine

confirm :: String -> IO Bool
confirm question = putStrLn question >> fmap interpret getLine
    where interpret "Y" = True
          interpret _ = False
```

``` haskell
Prelude> printTwoThings
One!
2

Prelude> verboseEcho
The Iliad
You wrote: The Iliad

Prelude> answer <- query "Why am I here?"
Why am I here?
Good question!
Prelude> answer
"Good question!"

Prelude> b <- confirm "Fire warheads?"
Fire warheads?
no no no no
Prelude> b
False
Prelude> b <- confirm "Make love, not war?"
Make love, not war?
Y
Prelude> b
True
```


<a id="monads-in-other-languages"></a>

## 13.13 其他语言中的 Monad

一旦你熟悉了 monad 的概念，你也会开始在其他语言中看到类似 monad 的东西。最著名的例子是 *Option types*、*Java Streams* 和 *JavaScript Promise* 。如果你以前了解这些语言或概念，你可能会发现本节很有启发性。如果你对这些内容不熟悉，可以跳过本节。

### 13.13.1 选项

许多语言都有[选项类型](https://en.wikipedia.org/wiki/Option_type)。该类型在Java中称为`Optional<T>`，在C++中称为`std::optional<T>`，在C#中称为`Nullable<T>`，等等。这些类型通常具有类似于 Haskell `Maybe` monad 的行为，例如：

- 在 Java 中，[`Optional.flatMap`](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Optional.html#flatMap(java.util.function.Function)) 对应于 `>>=`：它允许你将 `Function<T,<Optional<U>>` 应用于 `Optional<T>` 并获得 `Optional<U>`。
- 在 C# 中，二进制运算自动[提升](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types#lifted-operators) 为 `Nullable` 类型。例如，`a + null` 变为 `null`。

### 13.13.2 流

[Java Streams](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/Stream.html) 也有一个类似 monad 的 API。流是关于增量地产生许多值。就像Optional一样，方法`Stream.flatMap`让我们获取`Stream<T>`，将其与`Function<T,Stream<U>>`组合并得到`Stream<U>`。

例如，如果 `lines` 是 `Stream<String>`，`words` 接受 `String` 并返回 `Stream<String>`，`readInt` 接受 `String` 并返回 `Integer`，我们可以编写：

``` java
Stream<Integer> parseNumbers(Stream<String> lines) {
    return lines.flatMap(words).map(read);
}
```

这对应于以下 Haskell 列表 monad 代码：

``` haskell
parseNumbers :: [String] -> [Int]
parseNumbers strings = fmap read (strings >>= words)
```

``` haskell
parseNumbers ["123 456","7 89"]  ==>  [123,456,7,89]
```

### 13.13.3 承诺

关于 JavaScript 中的 Promise 是否*真的*是 monad，存在[很多](https://hackernoon.com/functional-javascript-functors-monads-and-promises-679ce2ab8abe)[分歧](https://stackoverflow.com/questions/45712106/why-are-promises-monads)。然而，一些相似之处是显而易见的。

首先，考虑 `Promise.then` 和 `>>=` 之间的相似之处。两者都采用*操作*（承诺或 Monad 操作），并将其与返回新操作的函数结合起来。

``` javascript
function concatPromises(promise1, promise2) {
  return promise1.then(value1 => promise2.then(value2 => value1+value2));
}
```

``` javascript
>> concatPromises(Promise.resolve("abc"), Promise.resolve("def")).then(console.log)
abcdef
```

``` haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = op1 >>= (\value1 -> op2 >>= (\value2 -> return (value1++value2)))
```

``` haskell
Prelude> concatMonadic (Just "abc") (Just "def")
Just "abcdef"
```

接下来，让我们考虑一下 async/await 和 do-notation 之间的相似之处。两者都是使用原始 `Promise.then` 或 `>>=` 机制的更好语法。我们使用 async/await 重新实现 `concatPromises`，使用 do-notation 重新实现 `concatMonadic`。他们的行为保持不变。

``` javascript
async function concatPromises(promise1, promise2) {
  let value1 = await promise1;
  let value2 = await promise2;
  return value1+value2;
}
```

``` haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = do
  value1 <- op1
  value2 <- op2
  return (value1++value2)
```


<a id="monads-wrap-up"></a>

## 13.14 Monad：总结

- `Monad` 类型类是一种表示*执行配方*的不同方式的方法
  - 失败（`Maybe`）
  - 日志记录
  - 状态
  - 不确定性（列表 Monad）
  - IO
- 你可以用两种等效的方式编写 monad 代码：
  - 直接使用`Monad`类操作（`>>=`、`>>`）
  - 使用 `do` 表示法
- 当`M`是一个monad时，`M a`类型的值是*产生`a`类型结果的操作*
- Monad 是一个*设计模式*和一个*库*（`mapM` 等）
  - 使用通用抽象使代码更容易理解
  - 读取 `State` 操作比破译带有状态的复杂递归更容易
- 你可以用 monad 做的一切，你也可以不用它们做
  - 例外：IO
  - 使用 monad 通常可以简化代码
- *警告*：互联网上充斥着试图使用简单类比来解释 monad 的教程
  - 根据我的经验，这是行不通的
  - 有效的是使用不同的 Monad 并慢慢习惯这个概念


<a id="sidenote-standard-haskell"></a>

## 13.15 附注：标准 Haskell

本讲座和上一讲座涵盖了 Haskell 的 GHC 版本与标准 Haskell 2010 不同的许多部分。以下是 GHC 所做更改的简短列表，仅供你了解：

- `length`、`sum`、`foldr` 等普遍适用于 `Foldable` 而不仅仅是列表
- `Functor` 和 `Applicative` 是 `Monad` 的超类
- `fail` 方法已从 `Monad` 类型类移至其自己的 `MonadFail` 类


<a id="quiz-4"></a>

## 13.16 测验

与下面的 do 块等效的表达式是什么？

``` haskell
do y <- z
   s y
   return (f y)
```

1.`z >> \y -> s y >> return (f y)`
2.`z >>= \y -> s y >> return (f y)`
3.`z >> \y -> s y >>= return (f y)`

`\x xs -> return (x : xs)`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`\x xs -> return x : xs`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`(\x xs -> return x) : xs`是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是


<a id="exercises-4"></a>

## 13.17 练习

- [Set13a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13a.hs)
- [Set13b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13b.hs)
