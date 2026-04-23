# 第 14 讲：开始使用库

现在你已经了解了 Monad，也就差不多掌握了 Haskell 的核心内容，可以开始编写使用库的实际程序。本讲将介绍这类实际程序中常用的一些库。使用这些库也是练习 Monad、阅读文档和理解类型错误的好机会。

**注意！** 在阅读库的文档时，请记住注意库的版本。你可以在[`tests.cabal` 文件](https://github.com/moocfi/haskell-mooc/blob/master/exercises/tests.cabal) 中查看课程中使用的版本。课程材料中的链接始终将你带到正确的版本，`stack haddock --open <package>` 命令也是如此。另请参阅[阅读第 1 部分中的文档](https://haskell.mooc.fi/part1#reading-docs)。


## 14.1 `Text` 和 `ByteString`

到目前为止，我们一直用 Haskell `String` 类型处理字符串。然而，`String` 只是 `[Char]`，一个字符列表。在内存和时间方面，这都是极其低效的。一旦开始处理整个文件或网络请求，就必须使用更高效的字符串类型。

`String` 有两种更高效的替代品，语义略有不同：

- `Data.Text` 表示 *[Unicode 字符](https://en.wikipedia.org/wiki/Unicode)* 序列，效率更高。处理文本时使用。
- `Data.ByteString` 表示*字节序列*。处理二进制数据时使用。

这两种类型都有*惰性*和*严格*变体。 [`Data.Text` 的文档](https://hackage.haskell.org/package/text-1.2.5.0/docs/Data-Text.html) 很好地总结了差异：

> 严格 `Text` 类型要求整个字符串立即装入内存。惰性 `Text` 类型能够使用较小的内存占用来流式传输大于内存的字符串...每个模块都提供几乎相同的 API...

这些类型都提供 `pack` 和 `unpack` 函数，用于与普通 `String` 相互转换。它们还附带了熟悉的列表函数的特殊版本，如 `reverse`、`take`、`map` 等。

### 14.1.1 `Text` 示例

让我们通过一个简短的 GHCi 会话来演示 `Data.Text` 的用法。按照[文档](https://hackage.haskell.org/package/text-1.2.5.0/docs/Data-Text.html)，`Data.Text` 模块应该以*限定*方式导入。我们可以用函数 `T.pack` 将 `String` 转换为 `Text`。注意 `Text` 值打印时和 `String` 一样。

```haskell
Prelude> import qualified Data.Text as T
Prelude T> :t T.pack
T.pack :: String -> T.Text
Prelude T> phrase = T.pack "brevity is the soul of wit"
Prelude T> :t phrase
phrase :: T.Text
Prelude T> phrase
"brevity is the soul of wit"
```

我们可以用 `Data.Text` 中的函数操作 `Text` 值。其中许多的名称类似于 `String` 函数或 `Prelude` 列表函数。

```haskell
Prelude T> :t T.length
T.length :: T.Text -> Int
Prelude T> T.length phrase
26
Prelude T> T.head phrase
'b'
Prelude T> T.take 4 phrase
"brev"
Prelude T> :t T.words
T.words :: T.Text -> [T.Text]
Prelude T> T.words phrase
["brevity","is","the","soul","of","wit"]
Prelude T> :t T.map
T.map :: (Char -> Char) -> T.Text -> T.Text
Prelude T> T.map (\c -> if c=='o' then '0' else c) phrase
"brevity is the s0ul 0f wit"
```

一个有用的细节是 `Text` 有一个 `Monoid` 实例，可以将 `Text` 值组合在一起。你还可以使用函数 `T.append` 和 `T.concat`。

```haskell
Prelude T> phrase <> phrase
"brevity is the soul of witbrevity is the soul of wit"
Prelude T> T.append phrase phrase
"brevity is the soul of witbrevity is the soul of wit"
Prelude T> T.concat [phrase,phrase,phrase]
"brevity is the soul of witbrevity is the soul of witbrevity is the soul of wit"
```

如果你想编写一个在 `Text` 上进行模式匹配的递归函数，就像在 `String` 上一样，你可以使用函数 `T.uncons :: T.Text -> Maybe (Char, T.Text)` 将 `Text` 拆分为头部和尾部。这是一个简单的示例：

```haskell
countLetter :: Char -> T.Text -> Int
countLetter c t =
  case T.uncons t of
    Nothing -> 0
    Just (x,rest) -> (if x == c then 1 else 0) + countLetter c rest
```

```haskell
Prelude T> countLetter 't' phrase
3
```

#### 14.1.1.1 严格与惰性

请注意，`Data.Text` 实现严格 `Text` 类型。你需要使用 `Data.Text.Lazy` 作为惰性版本。如前所述，这两种类型之间的一个区别是严格类型不适用于无限字符串：

```haskell
Prelude T> T.head (T.pack (repeat 'x'))
-- 永不返回
Prelude T> import qualified Data.Text.Lazy as TL
Prelude T TL> TL.head (TL.pack (repeat 'x'))
'x'
```

另一个实际问题是，在使用库时，你可能会遇到严格 `Text` 和惰性 `Text` 之间的不匹配。你通常可以根据需要使用 `toStrict` 或 `fromStrict` 来修复此问题。

```haskell
Prelude T TL> lazyPhrase = TL.pack "brevity is the soul of wit"
Prelude T TL> :t lazyPhrase
lazyPhrase :: TL.Text
Prelude T TL> :t phrase
phrase :: T.Text
Prelude T TL> lazyPhrase == phrase

<interactive>: error:
    • Couldn't match expected type ‘TL.Text’
                  with actual type ‘T.Text’
      NB: ‘T.Text’ is defined in ‘Data.Text.Internal’
          ‘TL.Text’ is defined in ‘Data.Text.Internal.Lazy’
    • In the second argument of ‘(==)’, namely ‘phrase’
      In the expression: lazyPhrase == phrase
      In an equation for ‘it’: it = lazyPhrase == phrase

Prelude T TL> :t TL.toStrict
TL.toStrict :: TL.Text -> T.Text
Prelude T TL> :t TL.fromStrict
TL.fromStrict :: T.Text -> TL.Text
Prelude T TL> TL.toStrict lazyPhrase == phrase
True
```

### 14.1.2 `ByteString` 示例

我们可以使用 `ByteString` 而不是 `Text` 来完成几乎相同的 GHCi 会话。但是，请注意 `ByteString` 是如何从 `Word8` 值而不是 `Char` 值构建的。 `Char` 可以表示任意 unicode 代码点，就像 `'Å'` 这样的字符一样，但 `Word8` 表示一个字节：从 0 到 255 的数字。不幸的是，有点令人困惑，`ByteString` 值的打印方式与 `String` 类似。

```haskell
Prelude> import Data.Word
Prelude Data.Word> import qualified Data.ByteString as B
Prelude Data.Word B> binary = B.pack [99,111,102,102,101,101]
Prelude Data.Word B> :t binary
binary :: B.ByteString
Prelude Data.Word B> :t B.pack
B.pack :: [Word8] -> B.ByteString
Prelude Data.Word B> binary
"coffee"
Prelude Data.Word B> :t B.length
B.length :: B.ByteString -> Int
Prelude Data.Word B> B.length binary
6
Prelude Data.Word B> :t B.head
B.head :: B.ByteString -> Word8
Prelude Data.Word B> B.head binary
99
Prelude Data.Word B> B.take 4 binary
"coff"
Prelude Data.Word B> :t B.map
B.map :: (Word8 -> Word8) -> B.ByteString -> B.ByteString
Prelude Data.Word B> B.map (+1) binary
"dpggff"
```

与 `Text` 相同的警告适用于严格 `ByteString` 和惰性 `ByteString` 之间的差异：

```haskell
Prelude B Data.Char> B.head (B.pack (repeat 99))
-- 永不返回
Prelude Data.Word B> import qualified Data.ByteString.Lazy as BL
Prelude Data.Word B BL> BL.head (BL.pack (repeat 99))
99
Prelude Data.Word B BL> binary == BL.pack [99]

<interactive>: error:
    • Couldn't match expected type ‘B.ByteString’
                  with actual type ‘BL.ByteString’
      NB: ‘BL.ByteString’ is defined in ‘Data.ByteString.Lazy.Internal’
          ‘B.ByteString’ is defined in ‘Data.ByteString.Internal’
    • In the second argument of ‘(==)’, namely ‘BL.pack [99]’
      In the expression: binary == BL.pack [99]
      In an equation for ‘it’: it = binary == BL.pack [99]

Prelude Data.Word B BL> :t BL.toStrict
BL.toStrict :: BL.ByteString -> B.ByteString
Prelude Data.Word B BL> :t BL.fromStrict
BL.fromStrict :: B.ByteString -> BL.ByteString
Prelude Data.Word B BL> binary == BL.toStrict (BL.pack [99])
False
```

### 14.1.3 附注：编码

你可能想知道为什么我们同时拥有 Text 和 ByteString。差异很微妙但又真实。当我们对 `Text` 进行操作时，我们会逐个字符地进行操作，而不管这些字符是什么以及它们是如何编码的。当我们对 `ByteString` 进行操作时，我们对字节进行操作，无论这些字节代表什么。

字符、数字和数据结构是帮助我们人类处理复杂编程任务的抽象。计算机内存本质上只是一个巨大的字节序列。机器并不关心我们如何解释这些字节。 `Text` 和 `ByteString` 之间的本质区别在于字节的分组和解释方式。

为了说明这种差异，我们将看看 [UTF-8 文本编码](https://en.wikipedia.org/wiki/UTF-8)。文本编码是将*字符*表示为*字节*的方法。 UTF-8 可以表示[Unicode](https://en.wikipedia.org/wiki/Unicode)定义的所有数百万个字符。由于字节只能存储 0 到 255 之间的值，这意味着一个字符可以编码为多个字节。 UTF-8 字符串“Ha∫keλ!”的位和字节可以有多种解释：

![](img/string.svg)

（如果你在图片和“Ha∫keλ！”中看到不同的字符，则意味着你的浏览器错误地解释了编码，或者你使用的字体不支持所有字符。）

如果我们使用不同的编码读取相同的位流，我们会看到其他字符。例如，上面的字符串将被解释为“Haâˆ«keÎ»!”使用[Latin-1 文本编码](https://en.wikipedia.org/wiki/Latin-1)。

顺便说一句，在处理原始二进制数据时，使用十六进制数字系统通常很方便，它使用单个符号 `0`、`1`、...、`9`、`A`、`B`、...、`F` 来表示四位的所有 16 种可能的组合。在本课程中我们不需要十六进制，但如果你有兴趣了解有关十六进制的更多信息，你可以查看[维基百科](https://en.wikipedia.org/wiki/Hexadecimal)。

我们可以使用代码探索相同的示例。函数 `Data.Text.Encoding.encodeUtf8 :: Text -> ByteString` 使用 UTF-8 将 Text 中的字符编码为 ByteString 中的字节。

```haskell
Prelude> import qualified Data.Text as T
Prelude T> import qualified Data.ByteString as B
Prelude T B> T.length (T.pack "haskell")
7
Prelude T B> T.length (T.pack "Ha∫keλ!")
7
Prelude T B> import Data.Text.Encoding
Prelude T B Data.Text.Encoding> encodeUtf8 (T.pack "haskell")
"haskell"
Prelude T B Data.Text.Encoding> encodeUtf8 (T.pack "Ha∫keλ!")
"Ha\226\136\171ke\206\187!"
Prelude T B Data.Text.Encoding> B.length (encodeUtf8 (T.pack "haskell"))
7
Prelude T B Data.Text.Encoding> B.length (encodeUtf8 (T.pack "Ha∫keλ!"))
10
```

如果我们处理的是 [ASCII 文本](https://en.wikipedia.org/wiki/ASCII)，也就是可以用单字节表示的字符，那么可以在一定程度上互换使用 `Text` 和 `ByteString`。命名空间 `Data.ByteString.Char8` 和 `Data.ByteString.Lazy.Char8` 提供了在 `ByteString` 上使用 `Char` 值（而不是 `Word8`）的函数。不过必须小心，确保所有字符确实都是纯 ASCII 字符，否则会出现令人意外的结果。

```haskell
Prelude T B> import qualified Data.ByteString.Char8 as B8
Prelude T B B8> B8.pack "abc"
"abc"
Prelude T B B8> :t B8.pack
B8.pack :: String -> B.ByteString
Prelude T B B8> :t B.pack
B.pack :: [Word8] -> B.ByteString
Prelude T B B8> B8.cons 'a' (B8.pack "bc")
"abc"
Prelude T B B8> putStrLn (B8.unpack (B8.pack "€λ훈"))  -- non-ASCII characters get truncated
¬»È
Prelude T B B8> putStrLn (T.unpack (T.pack "€λ훈"))
€λ훈
```


## 14.2 Monad：回顾

接下来我们将研究 IO monad 内部的库。这是我们在上一课中学到的关于 monad 的简短回顾。

- 当 `M` 是 monad 时，`M X` 类型的值是可以“执行”以“生成”`X` 类型的值的“操作”。
- Monad 操作可以使用以下方式实现
  - `Monad` 类型类的方法（`return`、`>>=`、`>>`），
  - `do`-符号，
  - 以及 `mapM` 等库函数。
- 与其他语言不同，`return` 不是关键字，不会导致操作停止执行。相反，`return x` *是始终生成 x 且不执行任何其他操作的操作*。
- 这就是 `do` 语法的样子：

```haskell
foo y = do
  operation1         -- 运行一个操作
  val <- operation2  -- 运行一个操作并保留产生的值
  operation3 val y   -- 使用参数运行一个操作
  mapM_ (\x -> operation4 val x) things  -- 使用通用 monad 操作和 lambda
  operation5 val     -- do 的最后一行决定整个代码块产生什么值
```


## 14.3 编写 HTTP 服务器：WAI 和 Warp

有时感觉世界上的一切都发生在 [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 和 [Web APIs](https://en.wikipedia.org/wiki/Web_API) 上。你的网络浏览器、你的智能手机应用程序、[你的银行](https://developer.nordeaopenbanking.com/)、[你的咖啡壶](https://tools.ietf.org/html/rfc2324)、[甚至你的门铃](https://support.ring.com/hc/en-us/articles/205385394-The-Protocols-and-Ports-Used-by-Ring-Devices)，都使用 HTTP 协议与服务器通信。

让我们看看如何在 Haskell 中搭建一个简单的 HTTP 服务器。这里会用到两个标准的底层组件：[WAI](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html) 和 [Warp](https://hackage.haskell.org/package/warp-3.3.23)。WAI（Web 应用接口）提供了一种定义 HTTP 请求处理逻辑的方式。Warp 则是一个简单的 HTTP 服务器，用来运行我们通过 WAI 定义的逻辑。现在听起来可能有点抽象，但一个简单示例会有所帮助。

文件 [`exercises/Examples/HelloServer.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/HelloServer.hs) 实现了一个始终以“Hello World!”响应的 HTTP 服务器。你可以进入 `exercises/Examples` 目录，并运行 `stack runhaskell HelloServer.hs` 试一试。之后可以在浏览器中访问 <http://localhost:3421> 查看服务器响应。

```haskell
module Examples.HelloServer where

import qualified Data.ByteString.Lazy.Char8 as BL
import Network.HTTP.Types.Status (status200)
import Network.Wai (Application, responseLBS)
import Network.Wai.Handler.Warp (run)

port :: Int
port = 3421

main :: IO ()
main = run port application

-- Application 的类型：Request -> (Response -> IO ResponseReceived) -> IO ResponseReceived
application :: Application
application request respond =
  respond (responseLBS status200 [] (BL.pack "Hello World!"))
```

让我们看看这个示例中的类型。这里信息量不少。首先，`Application` 是表示 Web 服务器逻辑的类型别名。[Warp 中的 `run` 函数](https://hackage.haskell.org/package/warp-3.3.23/docs/Network-Wai-Handler-Warp.html#v:run) 可以运行一个 `Application`：

```haskell
run :: Port -> Application -> IO ()
type Application = Request -> (Response -> IO ResponseReceived) -> IO ResponseReceived
```

我们很快就会讨论 `Request` 和 `Response` 是什么，但从这个类型可以看出，`Application` 是一个 IO 操作，它接收一个 `Request` 类型的请求，以及一个 IO 操作 `respond :: Response -> IO ResponseReceived`。像 `respond` 这样的参数在许多上下文中被称为“回调”。它让我们能够把控制权交还给调用 `Application` 的库。`Application` 操作必须产生和 `respond` 相同的特殊类型 `ResponseReceived`。你可以把这个类型看成一个令牌，证明 `Application` 已经调用过 `respond`。

这可能听起来有点吓人，但看看代码，其实事情相对简单：我们的 `server` 是一个 `Application`，并接收两个参数：`request` 和 `respond`。

WAI 使用许多类型（例如 `Port`、`Request`、`Response`、`Status`）来表示 HTTP 概念。当你遇到它们时，在文档中查找它们会很有用。例如 [`Port` 只是 `Int`](https://hackage.haskell.org/package/warp-3.3.23/docs/Network-Wai-Handler-Warp.html#t:Port) 的别名。作为另一个示例，我们可以看到 [`responseLBS` 函数](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html#v:responseLBS) 的类型

```haskell
responseLBS :: Status -> ResponseHeaders -> ByteString -> Response
```

其中 [`Status` 在 `Network.HTTP.Types.Status`](https://hackage.haskell.org/package/http-types-0.12.3/docs/Network-HTTP-Types-Status.html#t:Status) 中定义，[`ResponseHeaders` 是来自 `Network.HTTP.Types.Header`](https://hackage.haskell.org/package/http-types-0.12.3/docs/Network-HTTP-Types-Header.html#t:ResponseHeaders) 的 `[Header]` 类型别名，`ByteString` 是惰性 `ByteString`，结果类型 [`Response` 定义在 `Network.WAI`](https://hackage.haskell.org/package/wai-3.2.3/docs/Network-Wai.html#t:Response) 中。

最后，请注意，我们采用了快捷方式，使用函数 `Data.ByteString.Lazy.Char8.pack` 将 `String` 转换为 `ByteString`。这仅适用于 ASCII 文本。

总是以相同文本响应的 Web 服务器并不是那么有趣。接下来我们看看如何针对不同的请求给出不同的响应。 HTTP 请求中有很多部分，但在本讲中，我们将重点关注*路径*。在 `http://example.com/abcd/ef/file` 这样的 URL 中，`/abcd/ef/file` 部分是路径。 WAI 有函数

```haskell
pathInfo :: Request -> [Text]
```

这为我们提供了所请求 URL 的路径，以 `/` 字符分割。

文件 [`exercises/Examples/PathServer.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/PathServer.hs) 实现了一个具有三个不同页面的 Web 服务器：

- <http://localhost:3421/source> 是应用程序自身的源码，从文件系统读取
- <http://localhost:3421/secret/file> 是一个秘密字符串
- <http://localhost:3421/anything/else> - 对于所有其他路径，显示“未找到：任何内容/其他”文本

和以前一样，你可以通过进入 `exercises/Examples` 目录并运行 `stack runhaskell PathServer.hs` 来运行服务器。


## 14.4 使用数据库：sqlite-simple

实现 HTTP 服务器之后，我们便能加入那张由应用程序相互通信织就的全球网络——也就是互联网。但如果程序记不住任何东西，通信又有什么意义呢？真正的应用程序即使在重启后也需要能够*持久化数据*。实现这一目的的常见方式是使用数据库。

数据库有很多种，但可以说使用最广泛的简单数据库是 [SQLite](https://www.sqlite.org/index.html)。SQLite 是一个库，可让你将数据存储在文件中，并使用 [SQL，结构化查询语言](https://en.wikipedia.org/wiki/SQL) 对其进行处理。使用 SQLite 时，无需像 [PostgreSQL](https://www.postgresql.org/) 或 [MySQL](https://www.mysql.com/) 那样运行单独的数据库服务器。

如果你不熟悉 SQL，请不要担心，你不需要在练习中编写自己的任何查询。如果你现在想学习一些 SQL，网上有很多教程。请参阅 [W3Schools](https://www.w3schools.com/sql/)、[SQL Zoo](https://sqlzoo.net/) 或 [Codecademy](https://www.codecademy.com/learn/learn-sql)。

Haskell 有许多用于使用 SQLite 的库，但我们在这里看一个名为 [sqlite-simple](https://hackage.haskell.org/package/sqlite-simple-0.4.18.2) 的库。让我们稍微探索一下 GHCi 中的库。

所有函数都位于 `Database.SQLite.Simple` 内部。你可以通过为 `open` 指定文件名来打开数据库，这是一个生成 `Connection` 的 IO 操作。

```haskell
Prelude> import Database.SQLite.Simple
Prelude Database.SQLite.Simple> :t open
open :: String -> IO Connection
Prelude Database.SQLite.Simple> db <- open "example.sqlite"
```

要运行 SQL 查询，可以使用 IO 操作 `query_`。它接收一个 `Connection` 和一个 `Query`，并产生结果列表。`Query` 类型只是包裹 `Text` 的简单 `newtype`。`query_` 的结果类型是多态的：任何满足 `FromRow` 类型类的类型，都可以从数据库中读取出来。如果这让人困惑，可以把它和 `read` 的类型 `Read a => String -> a` 做比较。`FromRow` 类似于这个数据库里的 `Read`：它表示可以从数据库中读取的类型。无论如何，我们先从数据库中读取数字 `1`：

```haskell
Prelude Database.SQLite.Simple> :t query_
query_ :: FromRow r => Connection -> Query -> IO [r]
Prelude Database.SQLite.Simple> :info Query
newtype Query = Query {fromQuery :: Data.Text.Internal.Text}
    -- 定义于 ‘Database.SQLite.Simple.Types’
    -- ... 省略其余输出
Prelude Database.SQLite.Simple> import qualified Data.Text as T
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT 1;")
Prelude Database.SQLite.Simple T> res <- query_ db q :: IO [[Int]]
Prelude Database.SQLite.Simple T> res
[[1]]
```

顺便说一句，前面这些初始示例都使用简单的 `SELECT x, y, z;` 查询，只返回常量数据。稍后我们再考虑数据库中的实际表。

如果没有类型签名，我们会从 GHCi 收到错误，它无法决定我们要从数据库中读取哪种类型：

```haskell
Prelude Database.SQLite.Simple T> res <- query_ db q

<interactive>:17:8: error:
    • Ambiguous type variable ‘r0’ arising from a use of ‘query_’
      prevents the constraint ‘(FromRow r0)’ from being solved.
      Probable fix: use a type annotation to specify what ‘r0’ should be.
      -- 省略其余错误信息
```

在继续之前，让我们仔细看看 `FromRow`。如果你以前接触过 SQL，你就会知道 SQL 查询返回许多*行*，每行由许多*值*（也称为*列*）组成。为了能够将 SQL 查询的结果解释为 Haskell 数据，我们需要一种方法来解释这些值和行。因此 sqlite-simple 定义了两个类，`FromField` 和 `FromRow`，以及一堆如下所示的实例。（你可以从[文档](https://hackage.haskell.org/package/sqlite-simple-0.4.18.2/docs/Database-SQLite-Simple.html#t:FromRow) 或通过使用 `:info FromRow` 等询问 GHCi 来找到这些实例。）

```haskell
instance FromField Int
instance FromField Bool
instance FromField String
instance FromField Text
instance FromField a => FromRow [a]
instance (FromField a, FromField b) => FromRow (a,b)
instance (FromField a, FromField b, FromField c) => FromRow (a,b,c)
```

大体上，基本的 Haskell 数据类型满足 `FromField` 类，各种 Haskell 集合满足 `FromRow` 类。我们之前的示例使用 `FromRow [a]` 和 `FromField Int` 实例，从 `query_` 得到了 `[[Int]]`。下面是一个使用其他数据类型的简单查询：

```haskell
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT 1, true, 'string';")
Prelude Database.SQLite.Simple T> query_ db q :: IO [(Int,Bool,String)]
[(1,True,"string")]
```

如果 SQL 和 Haskell 类型不匹配会发生什么？好吧，你会遇到运行时错误，就像你尝试调用 `read "True" :: Int` 一样。

```haskell
Prelude Database.SQLite.Simple T> query_ db q :: IO [(Int,Int,Int)]
*** Exception: ConversionFailed {errSQLType = "TEXT", errHaskellType = "Int", errMessage = "need an int"}
```

为了镜像 `FromRow` 和 `FromField` 类，sqlite-simple 还定义了 `ToRow` 和 `ToField` 类用于写入数据库。这是 `query` 函数的类型，它允许我们使用*参数化查询*。

```haskell
query :: (ToRow q, FromRow r) => Connection -> Query -> q -> IO [r]
```

以下是 `ToRow` 和 `ToField` 的一些实例：

```haskell
instance ToField Int
instance ToField Bool
instance ToField String
instance ToField Text
instance ToField Int

instance ToField a => ToRow [a]
instance (ToField a, ToField b) => ToRow (a, b)
instance (ToField a, ToField b, ToField c) => ToRow (a, b, c)
instance ToField a => ToRow (Only a)
```

参数化查询使用 `?` 字符来表示可以传入参数的槽。下面是一个简单的示例：

```haskell
Prelude Database.SQLite.Simple T> input = (1,"hello") :: (Int,String)
Prelude Database.SQLite.Simple T> parameterized = Query (T.pack "SELECT ?+1, true, ?;")
Prelude Database.SQLite.Simple T> query db parameterized input :: IO [(Int,Bool,String)]
[(2,True,"hello")]
```

**注意！** 当查询只有一个参数时，你可以使用两个 `ToRow` 实例：`ToField a => ToRow [a]` 和 `ToField a => Only a`。`Only` 数据类型定义在 `Data.Tuple.Only` 中，是对 Haskell 没有单元素元组这一事实的变通方案。或者，长度为 1 的列表也可以。同样，如果查询返回的每一行只有一列，你可以使用 `[[X]]` 或 `[Only X]` 作为返回类型。下面是一个示例：

```haskell
Prelude Database.SQLite.Simple T> q = Query (T.pack "SELECT lower(?);")
Prelude Database.SQLite.Simple T> query db q (Only "HELLO") :: IO [Only String]
[Only {fromOnly = "hello"}]
Prelude Database.SQLite.Simple T> query db q ["HELLO"] :: IO [[String]]
[["hello"]]
```

这几乎就是你需要了解的 sqlite-simple 内容：`open`、`query_`、`query`、`FromRow`、`ToRow`。哦，对了，还有一件事。如果不需要查询结果，可以使用 `execute` 和 `execute_` 函数。例如，它们在向数据库插入内容时很有用。

```haskell
execute_ :: Connection -> Query -> IO ()
execute :: ToRow q => Connection -> Query -> q -> IO ()
```

你将在 [`exercises/Examples/Phonebook.hs`](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Examples/Phonebook.hs) 下找到一个使用 sqlite-simple 维护电话簿的示例程序。该程序将电话簿保存在名为 `phonebook.db` 的文件中，其工作方式如下（从课程仓库中的 `exercises/Examples` 目录运行）：

    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    bob
    Phone?
    1234
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    bob
    Phone?
    5678
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    a
    Name?
    samantha
    Phone?
    1357
    $ stack runhaskell Phonebook.hs
    (a)dd or (q)uery?
    q
    Name?
    bob
    2 numbers:
    ["1234"]
    ["5678"]

附注：如果你因为 SQL 查询缺乏编译期类型检查而感到不满，可以看看 Haskell 中一些更高级的 SQL 库，例如 [Beam](https://haskell-beam.github.io/beam/) 或 [Opaleye](https://hackage.haskell.org/package/opaleye)。本课程使用 sqlite-simple 是为了保持简单，并避免过多关注 SQL 细节。


## 14.5 练习

- [Set14a](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set14a.hs): Text 和 ByteString
- [Set14b](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set14b.hs): HTTP 和 SQLite
