# 第 11 讲：`RealWorld -> (a,RealWorld)`


## 11.1 本讲内容

- IO


## 11.2 你被骗了！

忘记我们谈论的函数式编程和纯性。其实 Haskell 是*世界上最好的命令式编程语言*！让我们开始吧：

```haskell
questionnaire = do
  putStrLn "Write something!"
  s <- getLine
  putStrLn ("You wrote: "++s)
```

```haskell
Prelude> questionnaire
Write something!
Haskell!
You wrote: Haskell!
```

读取输入和写入输出很容易。我们还可以通过网络读取数据。这是一个完整的 Haskell 程序，使用 HTTP 从 URL 中获取单词并打印。

```haskell
import Network.HTTP
import Control.Monad

main = do
  rsp <- simpleHTTP (getRequest "http://httpbin.org/base64/aGFza2VsbCBmb3IgZXZlcgo=")
  body <- getResponseBody rsp
  forM_ (words body) $ \w -> do
     putStr "word: "
     putStrLn w
```

你可以在课程仓库中找到这个程序 [`exercises/Examples/FetchWords.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/FetchWords.hs)，并且可以像这样运行它：

    $ cd exercises/Examples
    $ stack runhaskell FetchWords.hs
    word: haskell
    word: for
    word: ever

这是怎么回事呢？我们来看看类型：

```haskell
Prelude> :t putStrLn
putStrLn :: String -> IO ()
Prelude> :t getLine
getLine :: IO String
```

`IO a` 类型的值是一个“操作”，它“生成”`a` 类型的值。所以 `getLine`是一个产生字符串的IO 操作。 `()` 类型就是所谓的*单元类型*，它的唯一值是`()`。它主要用于 IO 操作不返回任何内容（而只是有副作用）的情况。

与 Java（方法）类型的比较可能会有所帮助：

| Haskell 类型 | Java 类型 |
|:----------------------------|:-----------------------------|
| `doIt :: IO ()` | `void doIt()` |
| `getSomething :: IO Int` | `int getSomething()` |
| `force :: a -> b -> IO ()` | `void force(a arg0, b arg1)` |
| `mogrify :: c -> IO d` | `d mogrify(c arg)` |

IO 操作可以使用 *do-notation* 组合成更大的操作。

```haskell
do operation
   operation arg
   variable <- operationThatReturnsStuff
   let var2 = expression
   operationThatProducesTheResult var2
```

### 11.2.1 示例

你可以在标准库模块 [Prelude](https://hackage.haskell.org/package/base-4.16.4.0/docs/Prelude.html) 和 [System.IO](https://hackage.haskell.org/package/base-4.16.4.0/docs/System-IO.html) 中找到有用的 IO 操作

这是一个 IO 操作，要求用户输入一个字符串，并打印出该字符串的长度。

```haskell
query :: IO ()
query = do
  putStrLn "Write something!"                    -- run an operation, ignore produced value
  s <- getLine                                   -- run an operation, capture produced value
  let n = length s                               -- run a pure function
  putStrLn ("You wrote "++show n++" characters") -- run an operation, passing on the produced value
```

```haskell
Prelude> query
Write something!
lorem ipsum
You wrote 11 characters
```

`do` 块的最后一行产生的值是整个块产生的值。注意 `askForALine` 与 `getLine`、`IO String` 具有相同的类型：

```haskell
askForALine :: IO String
askForALine = do
  putStrLn "Please give me a line"
  getLine
```

除了 `query` 等 IO 操作之外，你还可以在 GHCi 中运行产生值的 IO 操作，如 `askForALine`。如果需要，你可以使用 `<-` 将操作结果捕获到变量中。

```haskell
Prelude> askForALine
Please give me a line
this is a line
"this is a line"
Prelude> line <- askForALine
Please give me a line
this is a line
Prelude> :t line
line :: String
Prelude> line
"this is a line"
```

如果你需要提供操作参数，你可以创建*一个返回操作的函数*。请注意 `ask` 如何具有带有 `->` 的函数类型，就像普通函数一样。我们还使用正常的函数定义语法将参数命名为 `question`。

```haskell
ask :: String -> IO String
ask question = do
  putStrLn question
  getLine
```

```haskell
Prelude> ask "What is love?"
What is love?
Baby don't hurt me!
"Baby don't hurt me!"
Prelude> response <- ask "Who are you?"
Who are you?
The programmer.
Prelude> response
"The programmer."
Prelude> :t response
response :: String
Prelude> :t ask
ask :: String -> IO String
Prelude> :t ask "Who are you?"
ask "Who are you?" :: IO String
```


## 11.3 容易误解的 `return`

Haskell 函数 `return` 的命名有点误导。在其他语言中 `return` 是一个内置关键字，但在 Haskell 中它只是一个函数。 `return :: a -> IO a` 函数获取一个值并将其转换为一个*操作，从而产生该值*。

```haskell
produceThree :: IO Int
produceThree = return 3

printThree :: IO ()
printThree = do
  three <- produceThree
  putStrLn (show three)
```

这听起来不太有用，不是吗？与 do 符号结合起来就是这样。这里我们根据用户回答的是`Y`还是`N`返回一个布尔值：

```haskell
yesNoQuestion :: String -> IO Bool
yesNoQuestion question = do
  putStrLn question
  s <- getLine
  return (s == "Y")
```

```haskell
Prelude> yesNoQuestion "Fire the missiles?"
Fire the missiles?
Y
True
Prelude> answer <- yesNoQuestion "Are you sure?"
Are you sure?
N
Prelude> :t answer
answer :: Bool
Prelude> answer
False
```

**注意！** 这意味着 return *不会停止操作的执行（与 Java 或 C 中的 return 不同）。请记住，在 do 块中，最后一行决定生成哪个值。这意味着该操作产生 `2`：

```haskell
produceTwo :: IO Int
produceTwo = do return 1
                return 2
```

```haskell
Prelude> produceTwo
2
```

让我们换个角度来看这个问题。 `do` 表示法允许我们引起一系列副作用，并最终产生一个值。

    produceThree = do putStrLn "1"   -- side effect, produces (), which is ignored
                      return    2    -- no side effect, produces 2, which is ignored
                      getLine        -- side effect, produces a String, which is ignored
                      return    3    -- no side effect, produces 3, which is passed on

    Prelude> final <- produceThree
    1
    this line is ignored
    Prelude> final
    3

另请注意，这些是相同的操作：

```haskell
do ...
   x <- op
   return x
```

```haskell
do ...
   op
```

由于 `return` 是一个函数，因此你应该记住将任何复杂表达式括起来：

```haskell
return (f x : xs)
-- alternatively:
return $ f x : xs
```


## 11.4 `do` 和类型

让我们更详细地看看 do 表示法的输入。 do-block 构建一个 `IO <something>` 类型的值。例如在

```haskell
foo = do
  ...
  lastOp
```

`lastOp` 的类型必须是 `IO X`（对于某些 `X`）。 `foo` 的类型也将为 `IO X`。接下来我们看一个带参数的例子：

```haskell
bar x y = do
  ...
  lastOp arg
```

`lastOp` 的类型必须为 `Y -> IO X`（以便 `lastOp arg` 的类型为 `IO X`）。 `bar` 的类型将为 `A -> B -> IO X`（在 `bar` 内部我们将有 `x :: A` 和 `y :: B`）。

如果我们使用 `return`：

```haskell
quux x = do
  ...
  return value
```

函数 `quux` 的类型为 `A -> IO B`，其中 `x :: A` 和 `value :: B`。

接下来我们看一下`<-`的打字。如果 `op :: IO X` 并且你有 `var <- op`，则 `var` 将具有 `X` 类型。我们在许多 GHCi 例子中都看到了这一点。

`do` 的最后一行不能是 `foo <- bar`。也不可能是`let foo = bar`。最后一行确定整个操作生成的内容，因此它必须是一个操作（例如，`return something`）。

这是一个有效的例子：

```haskell
alwaysFine :: IO Bool
alwaysFine = do
  putStrLn "What?" -- :: IO ()
  return 2         -- :: IO Int, produced value is discarded
  s <- getLine     -- getLine :: IO String, thus s :: String
  putStrLn s       -- putStrLn :: String -> IO (), thus putStrLn s :: IO ()
  let b = True     -- b :: Bool
  return b         -- :: IO Bool
                   -- Thus, alwaysFine :: IO Bool
```

类型规则保证你无法“逃脱”`IO`。即使 `<-` 从 `IO X` 为你提供 `X`，你也只能在 `do` 中使用 `<-`。然而，`do` 始终表示 `IO Y` 类型的值。换句话说：你可以暂时打开`IO`盒子，但你必须返回其中。 *“IO 中发生的事情，留在 IO 中。”*

稍后我们将详细讨论这意味着什么。现在，只要知道如果你有一个非 IO 类型的函数（例如 `myFunction :: Int -> [String] -> String`），该函数内部就不能发生 IO。它是一个纯函数。


## 11.5 控制结构

对于以下例子，我们需要两个新操作。

```haskell
print :: Show a => a -> IO ()   -- print a value using the show function
readLn :: Read a => IO a        -- get a line and convert it to a value using the read function
```

递归、守卫和 if-then-else 等常用工具也适用于 `IO` 世界。这是使用守卫定义的 IO 操作：

```haskell
printDescription :: Int -> IO ()
printDescription n
  | even n    = putStrLn "even"
  | n==3      = putStrLn "three"
  | otherwise = print n
```

```haskell
Prelude> printDescription 2
even
Prelude> printDescription 3
three
Prelude> printDescription 5
5
```

以下是使用递归和模式匹配打印列表中所有数字的操作：

```haskell
printList :: [Int] -> IO ()
printList [] = return () -- do nothing
printList (x:xs) = do print x
                      printList xs -- recursion
```

```haskell
Prelude> printList [1,2,3]
1
2
3
```

下面是两个稍微复杂一点的递归 IO 操作的例子。他们使用递归调用产生的值。操作 `readAndSum n` 从用户读取 `n` 数字并打印它们的总和。操作`ask questions`向用户显示`questions`中的每个字符串，读取响应，并返回所有响应的列表。

```haskell
readAndSum :: Int -> IO Int
readAndSum 0 = return 0
readAndSum n = do
  i <- readLn            -- read one number
  s <- readAndSum (n-1)  -- recursion: read and sum rest of numbers
  return (i+s)           -- produce result
```

```haskell
Prelude> s <- readAndSum 3
2
4
5
Prelude> s
11
```

```haskell
ask :: [String] -> IO [String]
ask [] = return []
ask (question:questions) = do
  putStr question
  putStrLn "?"
  answer <- getLine         -- get one answer
  answers <- ask questions  -- recursion: get rest of answers
  return (answer:answers)   -- produce result
```

```haskell
Prelude> replies <- ask ["What is your name","How old are you"]
What is your name?
Yog-Sothoth
How old are you?
The question is meaningless
Prelude> replies
["Yog-Sothoth","The question is meaningless"]
```

此外，我们还有一些 `IO` 特定的控制结构，或者更确切地说，函数。这些来自模块 `Control.Monad`。

```haskell
-- when b op performs op if b is true
when :: Bool -> IO () -> IO ()
-- unless b op performs op if b is false
unless :: Bool -> IO () -> IO ()
-- do something many times, collect results
replicateM :: Int -> IO a -> IO [a]
-- do something many times, throw away the results
replicateM_ :: Int -> IO a -> IO ()
-- do something for every list element
mapM :: (a -> IO b) -> [a] -> IO [b]
-- do something for every list element, throw away the results
mapM_ :: (a -> IO b) -> [a] -> IO ()
-- the same, but arguments flipped
forM  :: [a] -> (a -> IO b) -> IO [b]
forM_ :: [a] -> (a -> IO b) -> IO ()
```

使用这些，我们可以重写之前的例子：

```haskell
printList :: [Int] -> IO ()
printList xs = mapM_ print xs
```

```haskell
readAndSum n = do
  numbers <- replicateM n readLn
  return (sum numbers)
```

```haskell
ask :: [String] -> IO [String]
ask questions = do
  forM questions askOne

askOne :: String -> IO String
askOne question = do
  putStr question
  putStrLn "?"
  getLine
```


## 11.6 关于 `do` 和缩进

使用 do 表示法时很容易遇到奇怪的缩进问题。这里有一些经验法则可以帮助你正确行事。

do 和缩进最重要的规则是*do 块中的所有操作必须在同一列中开始*。

此规则的一些例子：

```haskell
-- This is not OK, putStrLn is way too left
foo = do y <- getLine
   putStrLn y

-- This is not OK either
foo = do y <- getLine
           putStrLn y

-- This is OK
foo = do y <- getLine
         putStrLn y

-- This is also OK: putting a line break after do
foo = do
  y <- getLine
  putStrLn y
```

一个相关的规则是*当一个操作跨越多行时，缩进后续行*。如果不缩进，它看起来就像是一个新操作！

```haskell
-- This is not OK, the string starts a new operation
quux = do putStrLn
          "this long string"
          print 1

-- This is OK
quux = do putStrLn
            "this long string"
          print 1
```

这是另一个例子，其中包含嵌套的 do 块和两个不同的有效缩进。

```haskell
-- This is OK
foo x = do quux
           y <- blorg
           when y (do thing
                      otherThing)
           return 3

-- This is also OK: starting putting a line break after do, using $
foo x = do
  quux
  y <- blorg
  when y $ do
    thing
    otherThing
  return 3
```


## 11.7 编写一个程序

在所有这些简短的一次性例子之后，让我们转向更长一些的例子。让我们编写一个程序来从所有 `.hs` 文件中获取所有类型注释。我们使用 `readFile` 和 `listDirectory` 等 IO 操作来读取和查找文件，也使用 `map` 和 `filter` 等纯代码来进行实际处理。首先，回顾一下我们正在使用的库操作：

```haskell
-- split string into lines
lines :: String -> [String]
-- `isSuffixOf suf list` is true if list ends in suf
Data.List.isSuffixOf :: Eq a => [a] -> [a] -> Bool
-- `isInfixOf inf list` is true if inf occurs inside list
Data.List.isInfixOf :: Eq a => [a] -> [a] -> Bool
-- FilePath is just an alias for String
type FilePath = String
-- get entire contents of file
readFile :: FilePath -> IO String
-- list files in directory
System.Directory.listDirectory :: FilePath -> IO [FilePath]
-- is the given file a directory?
System.Directory.doesDirectoryExist :: FilePath -> IO Bool
```

这是程序本身。你还可以在课程仓库中找到它，名称为 [`exercises/Examples/ReadTypes.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/ReadTypes.hs)。

```haskell
module Examples.ReadTypes where

import Control.Monad (forM)
import Data.List (isInfixOf, isSuffixOf)
import System.Directory (listDirectory, doesDirectoryExist)

-- a line is a type signature if it contains :: but does not contain =
isTypeSignature :: String -> Bool
isTypeSignature s = not (isInfixOf "=" s) && isInfixOf "::" s

-- return list of types for a .hs file
readTypesFile :: FilePath -> IO [String]
readTypesFile file
  | isSuffixOf ".hs" file = do content <- readFile file
                               let ls = lines content
                               return (filter isTypeSignature ls)
  | otherwise             = return []

-- list children of directory, prepend directory name
qualifiedChildren :: String -> IO [String]
qualifiedChildren path = do childs <- listDirectory path
                            return (map (\name -> path++"/"++name) childs)

-- get type signatures for all entries in given directory
-- note mutual recursion with readTypes
readTypesDir :: String -> IO [String]
readTypesDir path = do childs <- qualifiedChildren path
                       typess <- forM childs readTypes
                       return (concat typess)

-- recursively read types contained in a file or directory
-- note mutual recursion with readTypesDir
readTypes :: String -> IO [String]
readTypes path = do isDir <- doesDirectoryExist path
                    if isDir then readTypesDir path else readTypesFile path

-- main is the IO action that gets run when you run the program
main :: IO ()
main = do ts <- readTypes "."
          mapM_ putStrLn ts
```

我们可以通过进入目录 `exercises/Examples` 并运行以下命令来运行该程序：

```haskell
$ stack runhaskell ReadTypes.hs
deposit :: String -> Int -> Bank -> Bank
withdraw :: String -> Int -> Bank -> (Int,Bank)
runBankOp :: BankOp a -> Bank -> (a,Bank)
... and so on
```

当然，确切的输出会根据目录的内容而有所不同。


## 11.8 这些意味着什么？

让我们回到函数世界。我们如何协调 IO 操作与 Haskell 作为一种“纯”和“惰性”语言的关系？像 `putStrLn :: String -> IO ()` 这样的东西是一个返回操作的*纯*函数。怎样才算纯呢？当 `x` 相同时，`putStrLn x`也相同。换句话说：操作是一系列副作用的“纯描述”。只有*执行*该操作才会导致这些副作用。当 Haskell 程序运行时，只执行一个操作 - 它称为 `main :: IO ()`。其他操作只需链接到`main`即可运行。

在 GHCi 中，如果你输入的表达式计算结果为某个操作，GHCi 会为你运行该操作。这是 `print` 纯性的演示：

```haskell
Prelude> x = print 1   -- creates operation, doesn't run it
Prelude> x             -- runs the operation
1
Prelude> x             -- runs it again!
1
```

*操作是值*，就像数字、列表和函数一样。我们可以编写对操作进行操作的代码。该函数需要两个操作，`a` 和 `b`，并返回一个操作，询问用户想要运行哪一个操作。

```haskell
choice :: IO x -> IO x -> IO x
choice a b =
  do putStr "a or b? "
     x <- getLine
     case x of "a" -> a
               "b" -> b
               _ -> do putStrLn "Wrong!"
                       choice a b
```

```haskell
Prelude> choice (putStrLn "A!!!!") (putStrLn "B!!!!")
a or b? z
Wrong!
a or b? a
A!!!!
```

使用指定为参数的操作可以让我们编写像我们之前遇到的 `mapM_` 这样的函数。实现是一个递归 IO 操作，将另一个IO 操作作为参数。概念上很复杂，但是当你阅读代码时就很简单：

```haskell
mapM_ :: (a -> IO b) -> [a] -> IO ()
mapM_ op     [] = return ()       -- do nothing for an empty list
mapM_ op (x:xs) = do op x         -- run operation on first element
                     mapM_ op xs  -- run operation on rest of list, recursively
```

```haskell
Prelude> mapM_ print [1,2,3]
1
2
3
```


## 11.9 还有一件事：IORef

到目前为止，我们能够在 IO 中产生的唯一副作用是终端（`getLine`、`print`）和文件（`readFile`、`listDirectory`）IO。用 Java、Python 或 C 编写的命令式程序也有其他类型的副作用，我们无法用纯 Haskell 来表达。其中之一是*可变（即可变）状态*。纯函数无法读取可变状态，因为否则同一函数的两次调用可能不会返回相同的值。

模块 `Data.IORef` 中的 Haskell 类型 `IORef a` 是对 `a` 类型值的可变引用

```haskell
newIORef :: a -> IO (IORef a)                -- create a new IORef containing a value
readIORef :: IORef a -> IO a                 -- produce value contained in IORef
writeIORef :: IORef a -> a -> IO ()          -- set value in IORef
modifyIORef :: IORef a -> (a -> a) -> IO ()  -- modify value contained in IORef with a pure function
```

以下是在 GHCi 中使用 IORef 的一些例子：

```haskell
Prelude> :m +Data.IORef
Prelude Data.IORef> myRef <- newIORef "banana"
Prelude Data.IORef> readIORef myRef
"banana"
Prelude Data.IORef> writeIORef myRef "apple"
Prelude Data.IORef> readIORef myRef
"apple"
Prelude Data.IORef> modifyIORef myRef reverse
Prelude Data.IORef> readIORef myRef
"elppa"
```

下面是使用 `IORef` 对列表中的值求和的例子。请注意与命令式循环的相似之处。

```haskell
sumList :: [Int] -> IO Int
sumList xs = do r <- newIORef 0                       -- initialize r to 0
                forM_ xs (\x -> modifyIORef r (x+))   -- for every xs, add it to r
                readIORef r                           -- get last value of r
```

大多数时候不需要使用 `IORef`。 Haskell 风格更喜欢递归、参数和返回值。然而，现实世界的程序有时可能需要一两个 IORef。


## 11.10 IO 总结

`IO X` 类型的值是*IO 操作*，在运行时**生成** X 类型的值。操作是纯值。只有“运行”该操作才会产生副作用。

IO 操作可以使用 `do` 表示法组合在一起：

```haskell
op :: X -> IO Y
op arg = do operation                 -- run operation
            operation2 arg            -- run operation with argument
            result <- operation3 arg  -- run operation with argument, store result
            let something = f result  -- run a pure function f, store result
            finalOperation            -- last operation produces the the return value
```

`return x` 是一个总是产生值 `x` 的操作。当`x :: a`、`return x :: IO a`时。

有用的 IO 操作：

```haskell
-- printing & reading
putStr :: String -> IO ()
putStrLn :: String -> IO ()
print :: Show a => a -> IO ()
getLine :: IO String
readLn :: Read a => IO a

-- control structures from Control.Monad
when :: Bool -> IO () -> IO ()   -- when b op performs op if b is true
unless :: Bool -> IO () -> IO () -- unless b op performs op if b is false
replicateM :: Int -> IO a -> IO [a]   -- do something many times, collect results
replicateM_ :: Int -> IO a -> IO ()   -- do something many times, throw away the results
mapM ::  (a -> IO b) -> [a] -> IO [b] -- do something for every list element
mapM_ :: (a -> IO b) -> [a] -> IO ()  -- do something for every list element, throw away the results
forM ::  [a] -> (a -> IO b) -> IO [b] -- the same, but arguments flipped
forM_ :: [a] -> (a -> IO b) -> IO ()

-- files
readFile :: FilePath -> IO String
```


## 11.11 测验

这个IO 操作的类型是什么？

```haskell
foo x = do putStrLn x
           y <- getLine
           return (length y)
```

1.`String -> IO String`
2.`IO Int`
3.`String -> IO Int`
4.`IO String -> IO Int`

以下哪一行可以用来代替 `????`

```haskell
quux :: String -> IO [String]
quux q = do y <- getLine
            z <- getLine
            putStrLn (y++z)
            ????
```

1.`q <- getLine`
2.`return (y++z)`
3.`return [q]`
4.`ans <- return [y,z]`

`blorg [1,2,3]` 打印什么值？也就是说，它调用 `print x` 的值是 `x`。 `blorg` 产生的值不计算在内。

```haskell
blorg [] = return 0
blorg (x:xs) = do m <- blorg xs
                  print x
                  return (m+x)
```

1.打印`1`、`2`、`3`
2.打印`1`、`2`、`3`、`6`
3.打印`3`、`2`、`1`
4.打印`3`、`2`、`1`、`6`

`Int -> IO Int` 类型的函数可以执行以下哪些操作？

1. 无法定义该类型的函数。
2. 返回一个常量值。
3. 运行给定的IO 操作并返回其值。
4. 向用户查询数字并返回。

`IO Int -> Int` 类型的函数可以执行以下哪些操作？

1. 无法定义该类型的函数。
2. 返回一个常量值。
3. 运行给定的IO 操作并返回其值。
4. 向用户查询数字并返回。


## 11.12 练习

- [Set11a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set11a.hs) - 基本IO练习
- [Set11b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set11b.hs) - 高级 IO 练习
