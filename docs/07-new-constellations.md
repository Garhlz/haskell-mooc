# 第 7 讲：新的星座

本讲座介绍了“类型化函数式编程”的“设计模式”。这些模式在编写 Haskell 程序时都很有用，并且为练习之前讲座中的技能提供了一个很好的舞台。


## 7.1 用盒子建模

有时你不需要新类型，而只需重用标准类型即可。例如，用 `String` 表示汽车牌照号。但如果代码中充满了 `String`，就很容易意外混淆，比如汽车的型号和 `registerCar :: String -> String -> CarRegistry -> CarRegistry` 等函数中的注册。

对于这种情况，通常创建一个仅包含 `String` （“盒装”字符串）的新类型：

```haskell
data Plate = Plate String
  deriving (Show, Eq)
```

我们现在可以给 `registerCar` 一个稍微好一点的类型， `String -> Plate -> CarRegistry -> CarRegistry`。此外，我们可以将 `Plate` 的可能操作限制为字符串操作的子集。例如，无需合并两辆汽车的车牌号码。因此我们不需要提供函数 `concatPlates :: Plate -> Plate -> Plate`。我们还可以为 `Plate` 定义一个“智能构造函数”，用于检查寄存器编号的格式是否正确：

```haskell
parsePlate :: String -> Maybe Plate
parsePlate string
  | correctPlateNumber string  =  Just (Plate string)
  | otherwise                  =  Nothing
```

这是另一个例子：表示金钱。如果只是用 `Int` 存储，编译器无法保护我们免受诸如两个金额相乘这样的错误。如果实现自己的 `Money` 类型来包装 `Int`，就能获得类型安全。此外，我们可以概括这样一个事实：货币表示为整数美分。

```haskell
data Money = Money Int
  deriving Show

renderMoney :: Money -> String
renderMoney (Money cents) = show (fromIntegral cents / 100)

(+!) :: Money -> Money -> Money
(Money a) +! (Money b) = Money (a+b)

scale :: Money -> Double -> Money
scale (Money a) x = Money (round (fromIntegral a * x))

addVat :: Money -> Money
addVat m = m +! scale m 0.24
```

```haskell
renderMoney (Money 100 +! Money 150)
  ==> "2.5"

scale (Money 299) 0.24
  ==> Money 72

addVat (Money 299)
  ==> Money 371
```

**注意！** 如果你熟悉面向对象编程，这有点像封装。


## 7.2 用不同分支建模

Haskell 的代数数据类型在基于*不同情况*建模方面非常强大。将类型视为定义可能情况的集合以及“处理”这些情况的函数（通常通过模式匹配）通常很有用。让我们看两个例子。

在 Haskell 中定义自定义类型很容易，所以使用更具描述性的类型而不是布尔值或字符串更方便。考虑一个人员名单。在其他语言中，如果你想按姓名升序对人员进行排序，你可以使用类似 `sortPersons(persons, "name", true)` 的调用。在 Haskell 中你可以这样做：

```haskell
data Person = Person {name :: String, age :: Int}
  deriving Show

data SortOrder = Ascending | Descending
data SortField = Name | Age

sortByField :: SortField -> [Person] -> [Person]
sortByField Name ps = sortBy (comparing name) ps
sortByField Age ps = sortBy (comparing age) ps

sortPersons :: SortField -> SortOrder -> [Person] -> [Person]
sortPersons field Ascending ps = sortByField field ps
sortPersons field Descending ps = reverse (sortByField field ps)

persons = [Person "Fridolf" 73, Person "Greta" 60, Person "Hans" 65]
```

```haskell
sortPersons Name Ascending persons
  ==> [Person {name = "Fridolf", age = 73},Person {name = "Greta", age = 60},Person {name = "Hans", age = 65}]
sortPersons Age Descending persons
  ==> [Person {name = "Fridolf", age = 73},Person {name = "Hans", age = 65},Person {name = "Greta", age = 60}]
```

请注意，你如何不会意外地拼写字段名称（与字符串不同），以及你如何不需要记住 `true` 是指升序还是降序。

让我们继续看下一个例子。许多 Haskell 函数不适用于空列表（考虑 `head []`）。如果你正在编写需要跟踪列表是否可能为空或保证不为空的代码，则可以使用 [Data.List.NonEmpty](https://hackage.haskell.org/package/base-4.16.4.0/docs/Data-List-NonEmpty.html) 模块中的 `NonEmpty` 类型。

考虑 `NonEmpty` 的定义：

```haskell
data NonEmpty a = a :| [a]
```

这个类型表示“没有空列表”这种情况。类型 `NonEmpty a` 将始终由类型为 `a` 的值以及其他一些 `a` 组成，这些值收集在列表中。以下是 `NonEmpty Int` 的一些例子值：

```haskell
1 :| [2,3,4]
1 :| []
```

顺便说一句，这也是*中缀构造函数*的例子。我们之前已经遇到过另一个中缀构造函数，即列表构造函数 `(:)`。任何以冒号（`:` 字符）开头的运算符都可以用作中缀构造函数。我们可以在 `(:|)` 上进行模式匹配，就像在 `(:)` 上一样，正如你将在下面的例子中看到的那样。

以下是在普通列表和非空列表之间进行转换的函数。请注意，我们不能使用函数 `[a] -> NonEmpty a`，而必须使用 `Maybe` 来表示列表确实为空的可能性。另请注意 `toList` 只有一个方程，由于 `NonEmpty` 类型，我们不能出现 `toList []` 情况。

```haskell
nonEmpty :: [a] -> Maybe (NonEmpty a)
nonEmpty [] = Nothing
nonEmpty (x:xs) = Just (x :| xs)

toList :: NonEmpty a -> [a]
toList (x :| xs) = x : xs
```

```haskell
nonEmpty [1,2,3]     ==>  Just (1 :| [2,3])
nonEmpty [1]         ==>  Just (1 :| [])
nonEmpty []          ==>  Nothing
toList (1 :| [2,3])  ==>  [1,2,3]
```

以下是为 `NonEmpty` 实现的 `head` 和 `last`：

```haskell
neHead (x :| _) = x
neLast (x :| []) = x
neLast (_ :| xs) = last xs
```

```haskell
neHead (1:|[2,3])  ==>  1
neLast (1:|[2,3])  ==>  3
```

顺便说一句，这些函数可用作 `Data.List.NonEmpty.head` 和 `Data.List.NonEmpty.last` 以及许多其他有用的函数。

总之，如果你编写表示值的所有可能情况的类型，然后编写处理这些情况的函数，那么你的代码将简单且正确。


## 7.3 幺半群

在函数式编程中经常出现的一种模式是 *monoid*（不要与 *monad* 混淆！）。对幺半群的解释通常非常数学化，但其思想很简单：将事物组合起来。

### 7.3.1 关联运算

我们使用的许多函数和运算符都是*关联的*。这只是一种表达不需要括号的奇特方式。例如，所有这些表达式的值都是 16，因为加法是结合的：

```haskell
(1 +  3) + (5 + 7)
 1 + (3  + (5 + 7))
 1  + 3  +  5 + 7
```

在 Haskell 中很容易找到关联运算的例子。例如，用于连接列表的 `++` 运算符是关联的：无论你是执行 `([1] ++ [2,3]) ++ [4]` 还是 `[1] ++ ([2,3] ++ [4])` - 结果都是 `[1,2,3,4]`。

另一个很好的例子是函数组合运算符。 `(head . tail) . tail` 和 `head . (tail . tail)` 都计算列表的第三个元素。

然而，并非所有运算符都是结合运算符。最熟悉的例子是减法和求幂。  `(1-2)-3` 是 `-4` 但 `1-(2-3)` 是 `2`。类似地，`(2^3)^2` 是 `64`，而 `2^(3^2)` 是 512。在使用非结合运算符时需要小心括号。

另一个不具有关联性的运算符是列表构造函数 `(:)`。这次的原因更加根本：虽然 `True:(False:[])` 没问题，但 `(True:False):[]` 甚至没有打字！为了使操作具有关联性，它需要采用两个相同类型的参数。

除了运算符之外，函数也可以是关联的。语法看起来有点不同，但如果它们相同，则函数 `f` 是关联的：

```haskell
f x (f y z)
f (f x y) z
```

两个广泛使用的关联函数是 `min` 和 `max` 函数：

```haskell
min 2 (min 1 3) ==> 1
min (min 2 1) 3 ==> 1

max 2 (max 1 3) ==> 3
max (max 2 1) 3 ==> 3
```

### 7.3.2 半群

从数学上来说，关联函数（或运算符）形成*半群*。 Haskell 有一个类型类 `Semigroup` （在模块 `Data.Semigroup` 中定义），当类型具有明确的关联操作时可以使用该类型类。

```haskell
class Semigroup a where
  -- An associative operation.
  (<>) :: a -> a -> a
```

列表是 `Semigroup` 的实例，其中 `(++)` 为 `(<>)`：

```haskell
[1] <> [2,3] <> [4]  ==>  [1,2,3,4]
```

具有多个不同关联运算符的类型通常不会成为 Semigroup 的实例。一个例子是 `Int`，它具有许多关联函数，例如 `+`、 `*` 和 `max`。相反，Haskell 标准库使用装箱（请参阅本讲座前面的部分）。以下是 `Sum` 和 `Product` 的定义：

```haskell
data Sum a = Sum a
instance Num a => Semigroup (Sum a) where
  Sum a <> Sum b  =  Sum (a+b)

data Product a = Product a
instance Num a => Semigroup (Product a) where
  Product a <> Product b   =  Product (a*b)
```

顺便说一句，这是装箱的另一个好处：能够声明不同类型的类实例！

请注意 `Num a` 约束如何让我们在包含的类型 `a` 上使用 `Num` 操作，例如 `+` 和 `*`。我们可以有像 `Sum "abc" :: Sum String` 这样的值，但它们不会有 `Semigroup` 实例！

同样，我们有盒子类型 `Min` 和 `Max`。让我们在 GHCi 中玩一下：

```haskell
Prelude> import Data.Semigroup
Prelude Data.Semigroup> Product (2::Int) <> Product 3 <> Product 1
Product {getProduct = 6}
Prelude Data.Semigroup> Sum 3 <> Sum 5 <> Sum 7
Sum {getSum = 15}
Prelude Data.Semigroup> Product 2 <> Product 3 <> Product 1
Product {getProduct = 6}
Prelude Data.Semigroup> Min 4 <> Min 3 <> Min 5
Min {getMin = 3}
Prelude Data.Semigroup> Max 4 <> Max 3 <> Max 5
Max {getMax = 5}
```

### 7.3.3 终于，幺半群

如果我们再听听数学家的说法，*幺半群* 是一个带有*中性元素*的半群。中性元素是零：与其他元素组合时不执行任何操作的元素。以下是一些例子：

```haskell
-- 0 is the neutral element of (+)
3 + 0        ==>  3
0 + 3        ==>  3

-- 1 is the neutral element of (*)
1 * 5        ==>  5
5 * 1        ==>  5

-- [] is the neutral element of (++)
[] ++ [1,2]  ==>  [1,2]
[1,2] ++ []  ==>  [1,2]
```

Haskell 类型类 `Monoid` （来自模块 `Data.Monoid`）表示幺半群。

```haskell
class Semigroup a => Monoid a where
  -- The neutral element
  mempty :: a
```

以下是与我们的三个中性元素例子相对应的 `Monoid` 实例：

```haskell
instance Num a => Monoid (Sum a) where
  mempty = Sum 0

instance Num a => Monoid (Product a) where
  mempty = Product 1

instance Monoid [] where
  mempty = []
```

那么，对于程序员来说什么是幺半群呢？如果有一种方法可以将类型的两个元素组合在一起，使得括号无关紧要，并且还有一个“空元素”可以与事物组合而不改变它们，那么该类型就形成了幺半群。当这样想时，幺半群在编程中经常出现！

### 7.3.4 为什么需要它？

这个`Monoid`类有什么用呢？我们不能只写 `1 + 2` 而不是 `Sum 1 <> Sum 2` 吗？是的，我们可以，但某些库函数适用于所有 `Monoid` 类型。

我们同时需要一个中性元素和一个关联二元运算符的原因是，为了将多个元素*减少*或*折叠*为一个值，这正是我们所需要的两件事。这是以下人员的工作：

```haskell
mconcat :: Monoid a => [a] -> a
```

附注：定义 `mconcat` 的一种方法是 `foldr (<>) mempty`。你还记得`foldr`吗？

让我们看看为什么我们需要 `Monoid` 的属性来实现 `mconcat`。首先，我们需要 `mempty` 来处理空列表：

```haskell
mconcat [] :: Sum Int          ==>  Sum 0
```

其次，我们需要关联性才能将列表 `[x,y,z]` 减少为唯一值。如果 `<>` 不具有关联性，则 `mconcat [x,y,z]` 可能有两个可能的值，即 `(x<>y)<>z` 和 `x<>(y<>z)`。

最有用的 `Monoid` 函数是 `foldMap`：

```haskell
foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m
```

这种类型签名看起来很可怕，但具体情况更简单：

```haskell
foldMap Max [1::Int,4,2]  ==>  Max 4
foldMap Product [1::Int,4,2]  ==>  Product 8
-- We need the ::Int to avoid an "Ambiguous type variable" error when printing the result
```

让我们来分解一下这种类型。我们知道 `Foldable t => t a` 类型的一个例子是 `[a]`，因此我们可以将该类型重写为

```haskell
foldMap' :: Monoid m => (a -> m) -> [a] -> m
```

我们可以用我们已知的函数构建这个函数：

```haskell
foldMap' f xs = mconcat (map f xs)
```

哦，顺便说一句，多亏了 `(Monoid a, Monoid b) => Monoid (a,b)` 实例，我们甚至可以一次计算最大值和乘积：

```haskell
foldMap (\x -> (Max x, Product x)) [1::Int,4,2]  ==>  (Max 4, Product 8)
```

请注意，你不需要在自己的代码中使用幺半群，但在使用 Haskell 库时你最终会遇到它们，因此最好了解它们是什么。

### 7.3.5 如何使用它？

由于各种历史和性能原因，`Monoid` 和 `Semigroup` 类的定义不仅仅是

```haskell
class Semigroup a where
  (<>) :: a -> a -> a
class Semigroup a => Monoid a where
  mempty :: a
```

尽管你大多可以假装它们是。实际的定义是：

```haskell
class Semigroup a where
  -- | An associative operation.
  (<>) :: a -> a -> a

  -- Combine elements of a nonempty list with <>
  sconcat :: NonEmpty a -> a
  sconcat as = ... -- default implementation omitted

  -- Combine a value with itself using <>, n times
  stimes :: Integral b => b -> a -> a
  stimes n x = ... -- default implementation omitted
```

```haskell
class Semigroup a => Monoid a where
  mempty  :: a

  mappend :: a -> a -> a
  mappend = (<>)

  -- Combine elements of a list with <>
  mconcat :: [a] -> a
  mconcat = ... -- default implementation omitted
```

如你所见，除了 `<>` 和 `mempty` 之外的所有操作都有默认定义，因此正常的 `Monoid` 实例声明如下所示：

```haskell
instance Semigroup MyType where
  x <> y = ...

instance Monoid MyType where
  mempty = ...
```


## 7.4 开放和封闭抽象

新手 Haskell 程序员经常问（或者至少应该问！）的一个问题是：我什么时候应该使用类型类？本节提供了一个答案。

让我们看一个具体的例子。交通工具可以是汽车或飞机。我们可以使用代数数据类型（正如我们在本章前面所看到的）来建模，也可以使用类型类来建模。这是数据类型版本：

```haskell
data Vehicle = Car String | Airplane String

sound :: Vehicle -> String
sound (Car _) = "brum brum"
sound (Airplane _) = "zooooom"
```

这是类型类版本。请注意每种情况如何获取自己的数据类型，这些数据类型被收集在一个类型类中。

```haskell
data Car = Car String
data Airplane = Airplane String

class VehicleClass a where
  sound :: a -> String

instance VehicleClass Car where
  sound (Car _) = "brum brum"

instance VehicleClass Airplane where
  sound (Airplane _) = "zooooom"
```

这些解决方案有什么区别？基于数据的解决方案是“封闭的”，这意味着一组情况是固定的，我们可以在一个地方处理所有情况。基于类的解决方案是*开放的*，这意味着我们可以添加新情况，甚至在其他模块中也是如此。

当我们需要可扩展性时，开放抽象是很好的选择。在基于类的解决方案中，另一个模块可以定义自行车：

```haskell
data Bike = Bike String

instance VehicleClass Bike where
  sound (Bike _) = "whirrr"
```

当我们想知道我们已经处理了所有情况时，封闭抽象是很好的，例如考虑函数 `canCollide`，它检查两辆车是否可以碰撞：

```haskell
canCollide :: Vehicle -> Vehicle -> Bool
canCollide (Car _)      (Car _)      = True
canCollide (Airplane _) (Airplane _) = True
canCollide _            _            = False
```

这在基于类的解决方案中很难可靠地实现。例如，考虑如何处理 `Bike` 和 `Car` 之间的冲突检查。


## 7.5 用语言建模

有时，实现一种迷你编程语言来描述软件的各个部分是很有用的。这些的奇特术语是“嵌入式领域特定语言（EDSL）”。 Haskell 非常适合建模和解释语言。该语言的表达式使用（通常是递归的）代数数据类型来表示。该语言可以由递归函数“解释”（即求值或运行）。

以下是用于描述网上商店中产品价格计算的语言例子。

```haskell
data Discount = DiscountPercent Int         -- A percentage discount
              | DiscountConstant Int        -- A constant discount
              | MinimumPrice Int            -- Set a minimum price
              | ForCustomer String Discount -- Discounts can be conditional
              | Many [Discount]             -- Apply a number of discounts in row
```

该语言由函数 `applyDiscount` 解释，该函数接受客户名称、价格、折扣并返回价格。

```haskell
applyDiscount :: String -> Int -> Discount -> Int
applyDiscount _        price (DiscountPercent percent) = price - (price * percent) `div` 100
applyDiscount _        price (DiscountConstant discount) = price - discount
applyDiscount _        price (MinimumPrice minPrice) = max price minPrice
applyDiscount customer price (ForCustomer target discount)
    | customer == target  = applyDiscount customer price discount
    | otherwise           = price
applyDiscount customer price (Many discounts) = go price discounts
  where go p [] = p
        go p (d:ds) = go (applyDiscount customer p d) ds
```

在这里，我们应用 -50%、-\$30 的折扣链，最低价格为 \$35：

```haskell
applyDiscount "Bob" 120 (DiscountPercent 50)
  ==> 60
applyDiscount "Bob" 60 (DiscountConstant 30)
  ==> 30
applyDiscount "Bob" 30 (MinimumPrice 35)
  ==> 35
applyDiscount "Bob" 120 (Many [DiscountPercent 50, DiscountConstant 30, MinimumPrice 35])
  ==> 35
```

这里我们为 Ssarah 和 Yvonne 提供不同的折扣：

```haskell
applyDiscount "Yvonne" 100 (Many [ForCustomer "Yvonne" (DiscountConstant 10), ForCustomer "Ssarah" (DiscountConstant 20)])
  ==> 90
applyDiscount "Ssarah" 100 (Many [ForCustomer "Yvonne" (DiscountConstant 10), ForCustomer "Ssarah" (DiscountConstant 20)])
  ==> 80
```

正如你所看到的，即使是简单的 `Discount` 类型也可以生成复杂的行为，因为它是自引用（递归）的。使用 `Discount`，我们能够将网上商店的折扣逻辑表示为*数据*，而不是编写代码。

将逻辑表示为数据而不是代码有多种原因。与代码不同，数据可以轻松存储在文件或数据库中，甚至可以通过网络传输。我们还可以将相同的数据用于多种目的，例如，我们可以在管理用户界面中可视化折扣规则。


## 7.6 练习

- [Set7](https://github.com/moocfi/haskell-mooc/blob/master/exercises/Set7.hs)
