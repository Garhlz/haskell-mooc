# 第 13 讲：问题范畴中的幺半群

- Monad

在本讲中，我们将使用许多示例来构建 *monad* 的概念。现在你应该熟悉理解 Monad 所需的所有 Haskell 函数。

Monad 是编程中众所周知的难题，部分是因为术语陌生，部分是因为教程质量参差不齐，部分是因为在学习 Haskell 时过早理解 Monad。在课程后期引入 Monad 是为了让理解更容易。

如果你觉得本讲很难，不要灰心，许多人也觉得这个话题很难。许多高效的 Haskell 程序员已经成功理解了 Monad，所以这不是不可能的。

最后要注意的是：Monad 和 Functor 一样，是来自范畴论这个数学分支的概念。然而，*你不需要了解范畴论来理解 Haskell 编程中的 Monad*。 就像人们可以在不了解类型论的情况下使用 OOP 或函数式编程一样，可以在不了解范畴论的情况下使用 Monad。对于函数式程序员来说，范畴论可能很有益，但不是必需的。


## 13.1 示例 1：Maybe

使用许多 `Maybe` 值时，代码往往会变得有点混乱。让我们看一些示例。首先，我们组合一些返回 `Maybe String` 的函数。注意 `stealSecret` 中需要的嵌套 `case`：写起来并不有趣。

```haskell
-- 尝试使用密码登录。
-- 成功时返回 `Just username`，否则返回 `Nothing`。
login :: String -> Maybe String
login "f4bulous!" = Just "unicorn73"
login "swordfish" = Just "megahacker"
login _           = Nothing

-- 获取与用户关联的秘密。
-- 不是所有用户都有秘密。
secret :: String -> Maybe String
secret "megahacker" = Just "I like roses"
secret _            = Nothing

-- 登录并返回用户的秘密（如果有的话）
stealSecret :: String -> Maybe String
stealSecret password =
  case login password of
    Nothing -> Nothing
    Just user -> case secret user of
                   Nothing -> Nothing
                   Just s -> Just ("Stole secret: "++s)
```

```haskell
stealSecret "swordfish"  ==>  Just "Stole secret: I like roses"
stealSecret "f4bulous!"  ==>  Nothing
stealSecret "wrong_password"  ==>  Nothing
```

接下来，我们看一个列表相关的示例。这里会用到 Prelude 中返回 `Maybe` 的函数 `lookup`。这次不是嵌套的 `case`，而是在 `case` 里面放了一个 `if`。

```haskell
-- 从键值对列表中获取某个键对应的值。
lookup :: (Eq a) => a -> [(a, b)] -> Maybe b
```

```haskell
-- 在给定键值对列表中把 key 的值设为 val，
-- 但只有当 val 大于当前值时才这样做！
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  case lookup key assocs
  of Nothing -> Nothing
     Just x -> if (val < x)
                then Nothing
                else Just ((key,val) : delete (key,x) assocs)
```

这种代码非常常见，而且通常会重复同一种模式：只要任意一个中间结果是 `Nothing`，整个结果就是 `Nothing`。我们可以定义一个*链接运算符* `?>`，让这样的代码更容易写。这个运算符接受一个结果和下一步计算，并且只有当结果是 `Just` 值时才运行下一步。

```haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- 如果已经失败，就不必运行下一步：
Nothing ?> _ = Nothing
-- 否则运行下一步：
Just x  ?> f = f x
```

链接运算符很好地简化了我们的示例。请注意我们如何定义简单的辅助函数来处理计算的一步，而不是编写一个大表达式。

```haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password ?>
    secret ?>
    decorate
  where decorate s = Just ("Stole secret: "++s)
```

```haskell
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

这是另一个示例：从 `safeHead` 和 `safeTail` 构建的安全列表索引：

```haskell
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

```haskell
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

```python
# Python 代码
x = op(val)
if x:
  f(x)
```

```java
// Java 代码
Object x = op(val);
if (x != null) {
  f(x);
}
```

if-result 模式和 `?>` 之间的区别在于，我们使用 `Nothing` 值来显式表示失败，而不是依赖于 Python 中任何变量都可以是 `None`（或 `False`）这一事实，或者 Java 中任何 `Object` 引用都可以是 `null` 的事实。


## 13.2 示例 2：日志

让我们用另一个示例来探讨链接的概念：日志记录。类型 `Logger` 表示一个值，以及生成这个值时产生的日志消息列表。

```haskell
-- Logger 定义
data Logger a = Logger [String] a  deriving Show

getVal :: Logger a -> a
getVal (Logger _ a) = a
getLog :: Logger a -> [String]
getLog (Logger s _) = s

-- 基本操作：
nomsg :: a -> Logger a
nomsg x = Logger [] x        -- 一个值，没有消息

annotate :: String -> a -> Logger a
annotate s x = Logger [s] x  -- 一个值和一条消息

msg :: String -> Logger ()
msg s = Logger [s] ()        -- just a message
```

这是一个 `login` 函数，用于记录有关其处理的用户名和密码的一些详细信息。请注意，当我们需要处理多个 `Logger` 值时，我们如何在 `login` 中遇到复杂的代码。

```haskell
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

```haskell
login "paul.atreides" "muad'dib"
  ==> Logger ["Valid user","Password ok"] True
login "paul.atreides" "arrakis"
  ==> Logger ["Valid user","Password wrong: arrakis"] False
login "ninja" ""
  ==> Logger ["Password ok"] True
login "leto.atreides" "paul"
  ==> Logger ["Invalid user: leto.atreides"] False
```

让我们尝试通过为 `Logger` 定义链接运算符来简化此代码。进行多个 `Logger` 操作时，重要的是保留所有日志。这是一个链接运算符 `#>`，以及如何使用它来记录一些算术计算的示例。

```haskell
(#>) :: Logger a -> (a -> Logger b) -> Logger b
Logger la a #> f = let Logger lb b = f a  -- 将值传给下一步
                   in Logger (la++lb) b   -- 将结果和所有消息打包在一起
```

```haskell
-- 对数字求平方，并记录一条相关消息
square :: Int -> Logger Int
square val = annotate (show val ++ "^2") (val^2)

-- 给数字加 1，并记录一条相关消息
add :: Int -> Logger Int
add val = annotate (show val ++ "+1") (val+1)

-- 将数字翻倍，并记录一条相关消息
double :: Int -> Logger Int
double val = annotate (show val ++ "*2") (val*2)

-- 带日志地计算表达式 2*(x^2+1)
compute :: Int -> Logger Int
compute x =
    square x
    #> add
    #> double
```

```haskell
compute 3
  ==> Logger ["3^2","9+1","10*2"] 20
```

通过使用 `#>`，我们可以大大简化 `login`。请注意，我们不需要担心将日志组合在一起。另请注意我们如何使用 lambda 表达式而不是定义辅助函数。

```haskell
login :: String -> String -> Logger Bool
login user password =
  validateUser user
  #>
  \valid -> if valid then checkPassword user password
                     else nomsg False
```

为了进一步演练，让我们在递归列表处理函数中使用 `Logger`。这是 `filter` 的日志版本。请注意代码如何在递归调用之前链接日志消息，以保持日志条目的良好顺序。

```haskell
-- 有时不需要前一个值：
(##>) :: Logger a -> Logger b -> Logger b
Logger la _ ##> Logger lb b = Logger (la++lb) b

filterLog :: (Eq a, Show a) => (a -> Bool) -> [a] -> Logger [a]
filterLog f [] = nomsg []
filterLog f (x:xs)
   | f x       = msg ("keeping "++show x) ##> filterLog f xs #> (\xs' -> nomsg (x:xs'))
   | otherwise = msg ("dropping "++show x) ##> filterLog f xs
```

```haskell
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```


## 13.3 示例 3：维护状态

在前面的示例中，我们只是写入了一些状态（日志）。有时，计算还需要修改某个共享状态。让我们看看一个小型银行账户示例。我们首先为银行状态定义一个数据类型：所有账户的余额，也就是从账户名称到余额的映射。

```haskell
import qualified Data.Map as Map

data Bank = Bank (Map.Map String Int)
  deriving Show
```

下面是如何向账户存钱。我们使用 `Data.Map` 中的函数 `adjust` 来修改映射。

```haskell
-- 对 Map 中的一个值应用函数
Map.adjust :: Ord k => (a -> a) -> k -> Map.Map k a -> Map.Map k a
```

```haskell
deposit :: String -> Int -> Bank -> Bank
deposit accountName amount (Bank accounts) =
  Bank (Map.adjust (\x -> x+amount) accountName accounts)
```

取款稍微复杂一些，因为要处理账户不存在、余额不足等特殊情况。我们用库函数 `findWithDefault` 来帮忙。

```haskell
-- 从 Map 中取得某个键对应的值，
-- 如果键不存在则返回默认值
Map.findWithDefault :: Ord k => a -> k -> Map.Map k a -> a
```

```haskell
withdraw :: String -> Int -> Bank -> (Int,Bank)
withdraw accountName amount (Bank accounts) =
  let -- 不存在的账户余额为 0
      balance = Map.findWithDefault 0 accountName accounts
      -- 取款不能超过余额
      withdrawal = min amount balance
      newAccounts = Map.adjust (\x -> x-withdrawal) accountName accounts
  in (withdrawal, Bank newAccounts)
```

最后，让我们编写一个函数，从一个账户中提取最多 100 块钱，将钱分成两半，然后存入两个账户。请注意我们需要多么小心地依次传递银行的不同版本 `bank`、`bank1`、`bank2` 和 `bank3`，以确保所有交易以正确的顺序发生。

```haskell
share :: String -> String -> String -> Bank -> Bank
share from to1 to2 bank =
  let (amount,bank1) = withdraw from 100 bank
      half = div amount 2
      -- 小心保留所有金额，即使 amount 是奇数
      rest = amount-half
      bank2 = deposit to1 half bank1
      bank3 = deposit to2 rest bank2
  in bank3
```

```haskell
share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
   ==> Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)])

share "wotan" "siegfried" "brunhilde"
      (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
   ==> Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)])
```

当你需要按顺序更新一个值，同时还执行一些其他计算时，这样的代码经常会出现在 Haskell 中。它很容易写错，比如不小心重用了 `bank1`，而类型系统不会帮你发现这种错误。让我们重写 `share`，让代码不再直接引用银行状态本身。这里仍然可以使用同样的链接思想。

```haskell
-- `BankOp a` 是一个转换 Bank 值的操作，
-- 同时返回一个 `a` 类型的值
data BankOp a = BankOp (Bank -> (a,Bank))

-- 在 Bank 上运行 BankOp
runBankOp :: BankOp a -> Bank -> (a,Bank)
runBankOp (BankOp f) bank = f bank

-- 依次运行两个 BankOp
(+>>) :: BankOp a -> BankOp b -> BankOp b
op1 +>> op2 = BankOp combined
  where combined bank = let (_,bank1) = runBankOp op1 bank
                        in runBankOp op2 bank1

-- 使用前一个 BankOp 返回的值运行参数化的 BankOp。
-- 实现有点绕，但现在只需要理解 +> 如何使用即可。
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
op +> parameterized = BankOp combined
  where combined bank = let (a,bank1) = runBankOp op bank
                        in runBankOp (parameterized a) bank1

-- 根据 deposit 构造 BankOp。
-- 没有返回值，所以使用 ()。
depositOp :: String -> Int -> BankOp ()
depositOp accountName amount = BankOp depositHelper
  where depositHelper bank = ((), deposit accountName amount bank)

-- 根据 withdraw 构造 BankOp。注意
--   withdraw accountName amount :: Bank -> (Int,Bank)
-- 几乎已经是一个 BankOp 了！
withdrawOp :: String -> Int -> BankOp Int
withdrawOp accountName amount = BankOp (withdraw accountName amount)
```

让我们看看这些链接运算符如何配合银行操作使用。

```haskell
Prelude> bank = Bank (Map.fromList [("edsger",10),("grace",50)])

-- 使用 +>> 运行多个操作

Prelude> runBankOp (depositOp "edsger" 1) bank
((),Bank (fromList [("edsger",11),("grace",50)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1) bank
((),Bank (fromList [("edsger",11),("grace",51)]))

Prelude> runBankOp (depositOp "edsger" 1 +>> depositOp "grace" 1 +>> withdrawOp "edsger" 11) bank
(11,Bank (fromList [("edsger",0),("grace",51)]))

-- 使用 +> 实现从一个账户到另一个账户的转账：

Prelude> runBankOp (withdrawOp "edsger" 5 +> depositOp "grace") bank
((),Bank (fromList [("edsger",5),("grace",55)]))

Prelude> runBankOp (withdrawOp "edsger" 100 +> depositOp "grace") bank
((),Bank (fromList [("edsger",0),("grace",60)]))
```

请注意，`BankOp` 类型的值表示一个转换银行状态的过程。银行的初始状态必须通过 `runBankOp` 提供。这是合理的，因为和具体的 `Bank` 状态不同，`BankOp` 这样的变换可以组合。必须显式使用 `runBankOp`，也让“定义操作”和“执行操作”之间的区别更加清楚。

现在我们已经熟悉了 `BankOp` 值的操作，我们可以将 `share` 实现为 `BankOp`。这里实现了一个辅助函数 `distributeOp`，让代码更整洁。

```haskell
-- 将金额分配到两个账户
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

```haskell
runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",1000)]))
  ==> ((),Bank (Map.fromList [("brunhilde",50),("siegfried",50),("wotan",900)]))

runBankOp (shareOp "wotan" "siegfried" "brunhilde")
          (Bank (Map.fromList [("brunhilde",0),("siegfried",0),("wotan",91)]))
  ==> ((),Bank (Map.fromList [("brunhilde",46),("siegfried",45),("wotan",0)]))
```

这样清爽多了，不是吗？我们根本不需要显式传递银行状态，几乎可以像命令式语言那样编程，同时仍然保持完全纯粹。

你可以在课程仓库的 [`exercises/Examples/Bank.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Bank.hs) 下找到所有这些代码。


## 13.4 终于，Monad 类型类

我们现在已经看到了三种具有链接操作的不同类型：

```haskell
(?>) :: Maybe a -> (a -> Maybe b) -> Maybe b
(#>) :: Logger a -> (a -> Logger b) -> Logger b
(+>) :: BankOp a -> (a -> BankOp b) -> BankOp b
```

就像之前的 `map` 和 `Functor` 一样，有一个类型类可以捕获此模式。请注意，`Monad` 是关于*类型构造函数*的类型类，就像 `Functor` 一样。

```haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
```

`Monad` 中还有一些额外的操作：

```haskell
  -- 将普通值提升到 monad 中
  return :: a -> m a
  -- 更简单的链接（类似我们的 ##>）
  (>>) :: m a -> m b -> m b
  a >> b  =  a >>= \_ -> b     -- 记住：_ 表示忽略的参数
```

回想一下，`Functor` 类型类是关于通用 `map` 操作的。类似地，`Monad` 类型类只是一个通用的链接操作。

```haskell
fmap :: Functor f => (a->b) -> f a -> f b
(>>=) :: Monad m => m a -> (a -> m b) -> m b
```

表达式 `operation >>= next` 接受一个 monad 操作 `operation :: m a`，并把它产生的值交给 `next :: a -> m b`，继续进行下一步计算。如果这感觉太抽象，只要回想一下 `Maybe` 的链接是如何工作的：

```haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
-- 如果已经失败，就不必运行下一步：
Nothing >>= _ = Nothing
-- 否则运行下一步：
Just x  >>= f = f x
```


## 13.5 Maybe 是 Monad！

这是 `Maybe` 的完整 `Monad` 实例和一些示例。

```haskell
instance  Monad Maybe  where
    (Just x) >>= k      = k x
    Nothing  >>= _      = Nothing

    (Just _) >>  k      = k
    Nothing  >>  _      = Nothing

    return x            = Just x
```

```haskell
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

以下是用 monad 操作重写的 `stealSecret` 和 `increase` 示例。将 `?>` 改为 `>>=`，将 `Just` 改为 `return`。

```haskell
stealSecret :: String -> Maybe String
stealSecret password =
    login password >>=
    secret >>=
    decorate
  where decorate s = return ("Stole secret: "++s)
```

```haskell
-- 在给定键值对列表中把 key 的值设为 val，
-- 但只有当 val 大于当前值时才这样做！
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


## 13.6 再谈 `do`

下面是一个复杂 monad 操作的示例。

```haskell
f = op1 >>= continue
  where continue  x   = op2 >> op3 >>= continue2 x
        continue2 x y = op4 >> op5 x y
```

让我们看看稍微改变一下这段代码会发生什么。首先，让我们内联定义。

```haskell
f = op1 >>= (\x ->
               op2 >>
               op3 >>= (\y ->
                          op4 >>
                          op5 x y))
```

由于 lambda 表达式一直持续到表达式末尾，因此我们可以省略括号。我们也以不同的方式缩进。

```haskell
f = op1 >>= \x ->
    op2 >>
    op3 >>= \y ->
    op4 >>
    op5 x y
```

现在我们可以注意到与 `do` 表示法的相似性。下面的 `do` 块实际上是相同的代码！

```haskell
f = do x <- op1
       op2
       y <- op3
       op4
       op5 x y
```

明确一下，`do` 语法只是 monad 操作（`>>=` 和 `>>`）以及 lambda 的一种更好写法。下面展示了 `do` 语法如何转换为 monad 操作。注意！这个转换定义是递归的。

```haskell
do x <- op a       ~~~>       op a >>= \x -> do ...
   ...
```

```haskell
do op a            ~~~>       op a >> do ...
   ...
```

```haskell
do let x = expr    ~~~>       let x = expr in do ...
   ...
```

```haskell
do finalOp         ~~~>       finalOp
```

这是使用 do 语法的 `safeNth`：

```haskell
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

这是最后一次重写 `increase`，这次使用 `do` 语法：

```haskell
-- 在给定键值对列表中把 key 的值设为 val，
-- 但只有当 val 大于当前值时才这样做！
increase :: Eq a => a -> Int -> [(a,Int)] -> Maybe [(a,Int)]
increase key val assocs =
  do oldVal <- lookup key assocs
     check oldVal
     return ((key,val) : delete (key,oldVal) assocs)
  where check x
           | val < x   = Nothing
           | otherwise = return x
```


## 13.7 Logger 是 Monad！

按理说，只要把 `>>=` 定义成 `#>`，我们就能自己为 `Logger` 编写 `Monad` 实例。不过由于 [Haskell 语言的较新变化](https://wiki.haskell.org/Functor-Applicative-Monad_Proposal)，在实现 `Monad` 实例之前，还必须先实现 `Functor` 和 `Applicative` 实例。`Functor` 我们已经见过了，但 `Applicative` 是什么？稍后会介绍。先来看实例实现：

```haskell
import Control.Monad

data Logger a = Logger [String] a  deriving Show

msg :: String -> Logger ()
msg s = Logger [s] ()

-- Functor 实例只是映射存储的值
instance Functor Logger where
  fmap f (Logger log x) = Logger log (f x)

-- 这是一个适用于任何 monad 的 Applicative 实例，
-- 现在可以先忽略。稍后会回到 Applicative。
instance Applicative Logger where
  pure = return
  (<*>) = ap

-- 最后是 Monad 实例
instance Monad Logger where
  return x = Logger [] x
  Logger la a >>= f = Logger (la++lb) b
    where Logger lb b = f a
```

我们不再需要 `nomsg` 操作，因为它只是 `return`。我们还可以使用 monad 操作重新实现 `annotate` 操作。

```haskell
nomsg :: a -> Logger a
nomsg x = return x

annotate :: String -> a -> Logger a
annotate s x = msg s >> return x
```

以下是使用 do 语法重写的 `compute` 和 `filterLog` 示例。请注意 `filterLog` 与 do 语法的配合有多好。

```haskell
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

```haskell
compute 3
  ==> Logger ["^2","+1","*2"] 20
filterLog (>0) [1,-2,3,-4,0]
  ==> Logger ["keeping 1","dropping -2","keeping 3","dropping -4","dropping 0"] [1,3]
```


## 13.8 `State` Monad（状态 Monad）

Haskell 的 `State` monad 是我们的 `BankOp` 类型的通用版本。 `State` 类型由两种类型参数化，第一个是状态类型，第二个是生成值的类型。 `State Bank a` 相当于我们的 `BankOp a`。你可以在[`transformers`包的模块`Control.Monad.Trans.State`](https://downloads.haskell.org/~ghc/latest/docs/html/libraries/transformers-0.5.6.2/Control-Monad-Trans-State.html)中找到`State` monad。这是 `State` 的简化实现。

```haskell
data State s a = State (s -> (a,s))

runState (State f) s = f s

-- 覆盖状态的操作（并产生 ()）
put :: s -> State s ()
put state = State (\oldState -> ((),state))

-- 产生当前状态的操作
get :: State s s
get = State (\state -> (state,state))

-- 使用函数修改当前状态的操作（并产生 ()）
modify :: (s -> s) -> State s ()
modify f = State (\state -> ((), f state))

-- 省略 Functor 和 Applicative 实例

instance Monad (State s) where
  return x = State (\s -> (x,s))

  op >>= f = State h
    where h state0 = let (val,state1) = runState op state0
                         op2 = f val
                     in runState op2 state1
```

注意我们如何声明实例 `Monad (State s)`。这里使用的是“部分应用的类型构造函数”，因为 `Monad` 的实例只能为还需要一个类型参数的类型构造函数声明。看看下面 `>>=` 类型里 `m`、`Maybe` 和 `State` 出现的位置，这一点可能会更清楚。

```haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b

instance Monad Maybe where
  (>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b

instance Monad (State s) where
  (>>=) :: State s a -> (a -> State s b) -> State s b
```

让我们看一下使用 `State` 的一些示例。首先，让我们考虑 `State Int a` 类型的计算，它代表使用简单的计数器。

```haskell
-- 将 i 加到计数器的值上
add :: Int -> State Int ()
add i = do old <- get
           put (old+i)
```

```haskell
runState (add 1 >> add 3 >> add 5 >> add 6) 0
  ==> ((),15)
```

```haskell
example :: State Int Int
example = do add 3           -- 将状态增加 3
             value <- get    -- value 是当前状态，即 initial+3
             add 1000        -- 将状态增加 1000
             put (value + 1) -- 用 value+1 覆盖状态，即 initial+4
             return value    -- 产生 value，即 initial+3
```

```haskell
runState example 1
  ==> (4,5)           -- initial 为 1，状态为 initial+4=5，产生 initial+3=4
```

请注意，`State s a` 类型的值表示一个转换状态的过程（就像 `BankOp` 一样）。必须使用 `runState` 提供初始状态。同样，显式使用 `runState` 会让“定义操作”和“执行操作”之间的区别更加清晰。

处理列表时，状态可以替换累加器参数。下面是两个示例：查找列表中最大的元素，以及查找列表中紧接在 `0` 之后出现的值。

```haskell
findLargest :: Ord a => [a] -> State a ()
findLargest [] = return ()
findLargest (x:xs) = do
  modify (\y -> max x y)  -- 用当前值和之前最大值中的较大者更新状态
  findLargest xs          -- 处理列表剩余部分
```

```haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

```haskell
-- 将给定值存入状态列表
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

```haskell
valuesAfterZero [0,1,2,3,0,4,0,5,0,0,6]
  ==> ((),[6,0,5,4,1])
```

**注意！** 顺便说一句，`State` monad 的实际实现没有像我们的简化示例那样的 `State` 构造函数。如果你想将函数包装到 `State` 操作中，请改用此帮助程序：

    state :: (s -> (a, s)) -> State s a


## 13.9 再谈 `mapM`

IO 讲座中的控制结构适用于*所有 monad*。这是它们的实际类型。

```haskell
when :: Monad m => Bool -> m () -> m ()        -- 条件操作
unless :: Monad m => Bool -> m () -> m ()      -- 类似，但条件取反
replicateM :: Monad m => Int -> m a -> m [a]   -- do something many times
replicateM_ :: Monad m => Int -> m a -> m ()   -- 类似，但忽略结果
mapM :: Monad m => (a -> m b) -> [a] -> m [b]  -- 对列表元素做某事
mapM_ :: Monad m => (a -> m b) -> [a] -> m ()  -- 类似，但忽略结果
forM  :: Monad m => [a] -> (a -> m b) -> m [b] -- mapM but arguments reversed
forM_ :: Monad m => [a] -> (a -> m b) -> m ()  -- 类似，但忽略结果
```

正如我们在这里看到的，我们可以在迄今为止遇到的所有 monad 上使用 `mapM`：

```haskell
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,2,3]  ==>  Just [0,1,2]
mapM (\x -> if (x>0) then Just (x-1) else Nothing) [1,0,3]  ==>  Nothing

mapM (\x -> msg "increment" >> msg (show x) >> return (x+1)) [1,2,3]
  ==> Logger ["increment","1","increment","2","increment","3"] [2,3,4]

runState (mapM (\x -> modify (x+) >> return (x+1)) [1,2,3]) 0
  ==> ([2,3,4],6)
```

更多示例：

```haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:xs) = Just x
firsts :: [[a]] -> Maybe [a]
firsts xs = forM xs safeHead
```

```haskell
firsts [[1,2,3],[4,5],[6]] ==> Just [1,4,6]
firsts [[1,2,3],[],[6]]    ==> Nothing
```

```haskell
-- 上一节某个示例的简化版本
findLargest :: Ord a => [a] -> State a ()
findLargest xs = mapM_ update xs
  where update x = modify (\y -> max x y)
```

```haskell
runState (findLargest [1,2,7,3]) 0  ==>  ((),7)
```

```haskell
let increment = modify (+1) >> get
    ops = replicateM 4 increment
in runState ops 0
  ==> ([1,2,3,4],4)
```

这是使用 `State` monad 重新实现的 `filter`：

```haskell
rememberElements :: (a -> Bool) -> [a] -> State [a] ()
rememberElements f xs = mapM_ maybePut xs
  where maybePut x = when (f x) (modify (++[x]))

sfilter :: (a -> Bool) -> [a] -> [a]
sfilter f xs = finalState
  where (_, finalState) = runState (rememberElements f xs) []
```

```haskell
sfilter even [1,2,3,4,5]
  ==> [2,4]
```

我们也可以编写适用于所有 monad 的操作。正如之前所见，这是通过类型类实现的。如果你只使用 `return` 和 `do` 语法等 monad 操作，类型系统就会为你的函数推断出通用类型。

```haskell
mywhen b op = if b then op else return ()

mymapM_ op [] = return ()
mymapM_ op (x:xs) = do op x
                       mymapM_ op xs
```

```haskell
*Main> :t mywhen
mywhen :: (Monad m) => Bool -> m () -> m ()
*Main> :t mymapM_
mymapM_ :: (Monad m) => (t -> m a) -> [t] -> m ()
```

我们可以在每个示例 monad 中使用这些通用操作：

```haskell
perhapsDecrease :: Int -> Maybe Int
perhapsDecrease x = do
  mywhen (x<=0) Nothing
  return (x-1)
```

```haskell
perhapsDecrease 2  ==>  Just 1
perhapsDecrease 0  ==>  Nothing
```

```haskell
search :: (Show a, Eq a) => a -> [a] -> Logger ()
search x ys = mymapM_ look ys
  where look y = mywhen (x==y) (msg ("Found "++show y))
```

```haskell
search 3 [1,2,3,4,3,2]  ==>  Logger ["Found 3","Found 3"] ()
```

```haskell
sumPositive :: [Int] -> State Int ()
sumPositive xs = mymapM_ f xs
  where f x = when (x>0) (modify (x+))
```

```haskell
runState (sumPositive [1,-4,2,3]) 0  ==>  ((),6)
```


## 13.10 Monad 是 Functor

尚未引入一项有用的操作：`liftM`。

```haskell
liftM :: Monad m => (a->b) -> m a -> m b
liftM f op = do x <- op
                return (f x)
```

`liftM` 操作让纯函数代码和 monad 操作更容易混合编写。

```haskell
liftM negate (Just 3)
  ==> Just (-3)

liftM sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (liftM negate get) 3
  ==> (-3,3)
```

`liftM` 的类型是不是很眼熟？就像`fmap`的类型一样！事实上，为 monad 定义 Functor 实例很容易：只需设置 `fmap = liftM` 即可。由于如今每个 `Monad` 都需要是 `Functor`，因此现代 Haskell 风格更喜欢 `fmap` 而不是 `liftM`。

```haskell
fmap :: Functor f => (a->b) -> f a -> f b
```

```haskell
fmap negate (Just 3)
  ==> Just (-3)

fmap sort $ firsts [[4,6],[2,1,0],[3,3,3]]
  ==> Just [2,3,4]

runState (fmap negate get) 3
  ==> (-3,3)
```


## 13.11 又一个 Monad

*列表 monad*（即 `[]` 的 `Monad` 实例）表示具有*多个返回值*的计算。它对于搜索替代方案很有用。这是第一个示例。对于每个 `x`，我们都生成 `x` 和 `-x`：

```haskell
[1,2,3] >>= \x -> [-x,x]
  ==> [-1,1,-2,2,-3,3]
```

我们可以通过生成一个空列表来过滤掉不合适的值：

```haskell
[1,2,3] >>= \x -> if x>1 then [x] else []
  ==> [2,3]
```

如果我们使用 do 语法，列表 monad 开始看起来更像是一个循环结构：

```haskell
do word <- ["Blue", "Green"]
   number <- [1,2,3]
   return (word ++ show number)
  ==> ["Blue1","Blue2","Blue3","Green1","Green2","Green3"]
```

更有趣的示例：找到列表中总和为 `k` 的所有对。 （同一元素两次算作一对。）

```haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = do a <- xs
                  b <- xs
                  if (a+b==k) then [(a,b)] else []
```

```haskell
findSum [1,2,3,4,5] 5
  ==> [(1,4),(2,3),(3,2),(4,1)]
```

最后一个更复杂的示例。我们使用列表 monad 从字符串中查找所有回文，然后找到最长的一个。

```haskell
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

```haskell
palindromesIn "aabbacddcaca"
  ==> ["a","aa","a","abba","b","bb","b","a","acddca","c","cddc","d","dd","d","c","cac","a","c"]
longestPalindrome "aabbacddcaca"
  ==> "acddca"
```

这是列表 monad 的令人惊讶的简单实现：

```haskell
instance Monad [] where
  return x = [x]                  -- 产生一个值的操作
  lis >>= f = concat (map f lis)  -- 对所有值计算 f，并合并结果
```

实际上，我们之前已经以列表推导的形式见过列表 Monad。把下面这个 `findSum` 的重写版本，和前面使用 `do` 语法的版本比较一下。

```haskell
findSum :: [Int] -> Int -> [(Int,Int)]
findSum xs k = [(a,b) | a <- xs, b <- xs, a+b==k ]
```


## 13.12 还有 IO

你现在可能已经猜到了，`IO` 是一个 monad。然而，`IO` 类型和 `instance Monad IO` 的实现是编译器内置的。你无法仅使用标准 Haskell 来实现 IO monad，这与 `Maybe` monad、`State` monad 和我们见过的其他 monad 不同。

然而，真正的副作用符合 monad 模式，就像 `State` 和 `Maybe` 一样。就像其他 monad 一样，我们将“操作的纯定义”与“运行操作”的过程分开。作为奖励，你可以将所有通用 monad 操作（`mapM` 等）与 IO 一起使用。

下面是一些使用 monad 操作编写 IO 的示例。

```haskell
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

```haskell
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


## 13.13 其他语言中的 Monad

一旦熟悉了 monad 的概念，你也会开始在其他语言中看到类似 monad 的东西。最著名的示例包括 *Option types*、*Java Streams* 和 *JavaScript Promise*。如果你以前了解这些语言或概念，可能会觉得本节很有启发。如果你对这些内容不熟悉，可以跳过本节。

### 13.13.1 Option

许多语言都有[选项类型](https://en.wikipedia.org/wiki/Option_type)。这种类型在 Java 中称为 `Optional<T>`，在 C++ 中称为 `std::optional<T>`，在 C# 中称为 `Nullable<T>`，等等。这些类型通常具有类似于 Haskell `Maybe` monad 的行为，例如：

- 在 Java 中，[`Optional.flatMap`](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Optional.html#flatMap(java.util.function.Function)) 对应于 `>>=`：它允许你将 `Function<T,<Optional<U>>` 应用于 `Optional<T>` 并获得 `Optional<U>`。
- 在 C# 中，二进制运算自动[提升](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types#lifted-operators) 为 `Nullable` 类型。例如，`a + null` 变为 `null`。

### 13.13.2 Stream

[Java Streams](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/Stream.html) 也有一个类似 monad 的 API。流用于逐步产生许多值。就像 `Optional` 一样，方法 `Stream.flatMap` 让我们取得 `Stream<T>`，将它与 `Function<T,Stream<U>>` 组合，并得到 `Stream<U>`。

例如，如果 `lines` 是 `Stream<String>`，`words` 接受 `String` 并返回 `Stream<String>`，`readInt` 接受 `String` 并返回 `Integer`，我们可以编写：

```java
Stream<Integer> parseNumbers(Stream<String> lines) {
    return lines.flatMap(words).map(read);
}
```

这对应于以下 Haskell 列表 monad 代码：

```haskell
parseNumbers :: [String] -> [Int]
parseNumbers strings = fmap read (strings >>= words)
```

```haskell
parseNumbers ["123 456","7 89"]  ==>  [123,456,7,89]
```

### 13.13.3 Promise

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

```haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = op1 >>= (\value1 -> op2 >>= (\value2 -> return (value1++value2)))
```

```haskell
Prelude> concatMonadic (Just "abc") (Just "def")
Just "abcdef"
```

接下来，让我们考虑 async/await 和 `do` 语法之间的相似之处。两者都是对原始 `Promise.then` 或 `>>=` 机制的更好写法。我们用 async/await 重新实现 `concatPromises`，再用 `do` 语法重新实现 `concatMonadic`。它们的行为保持不变。

``` javascript
async function concatPromises(promise1, promise2) {
  let value1 = await promise1;
  let value2 = await promise2;
  return value1+value2;
}
```

```haskell
concatMonadic :: Monad m => m String -> m String -> m String
concatMonadic op1 op2 = do
  value1 <- op1
  value2 <- op2
  return (value1++value2)
```


## 13.14 Monad：总结

- `Monad` 类型类是一种统一表示不同*执行配方*的方法
  - 失败（`Maybe`）
  - 日志记录
  - 状态
  - 不确定性（列表 Monad）
  - IO
- 你可以用两种等效的方式编写 monad 代码：
  - 直接使用 `Monad` 类操作（`>>=`、`>>`）
  - 使用 `do` 语法
- 当 `M` 是一个 monad 时，`M a` 类型的值是*产生 `a` 类型结果的操作*
- Monad 是一个*设计模式*和一个*库*（`mapM` 等）
  - 使用通用抽象使代码更容易理解
  - 读取 `State` 操作比破译带有状态的复杂递归更容易
- 你可以用 monad 做的一切，你也可以不用它们做
  - 例外：IO
  - 使用 monad 通常可以简化代码
- *警告*：互联网上充斥着试图使用简单类比来解释 monad 的教程
  - 根据我的经验，这是行不通的
  - 有效的是使用不同的 Monad 并慢慢习惯这个概念


## 13.15 附注：标准 Haskell

本讲和上一讲涵盖了 GHC 版 Haskell 与标准 Haskell 2010 不同的许多部分。下面是 GHC 所做更改的简短列表，仅供你了解：

- `length`、`sum`、`foldr` 等普遍适用于 `Foldable` 而不仅仅是列表
- `Functor` 和 `Applicative` 是 `Monad` 的超类
- `fail` 方法已从 `Monad` 类型类移至其自己的 `MonadFail` 类


## 13.16 测验

与下面的 do 块等效的表达式是什么？

```haskell
do y <- z
   s y
   return (f y)
```

1.`z >> \y -> s y >> return (f y)`
2.`z >>= \y -> s y >> return (f y)`
3.`z >> \y -> s y >>= return (f y)`

`\x xs -> return (x : xs)` 是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`\x xs -> return x : xs` 是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是

`(\x xs -> return x) : xs` 是什么类型？

1.`Monad m => a -> [a] -> m [a]`
2.`Monad m => a -> [m a] -> [m a]`
3.`a -> [a] -> Monad [a]`
4.以上都不是


## 13.17 练习

- [Set13a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13a.hs)
- [Set13b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set13b.hs)
