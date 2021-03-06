### 原文：[10 Killer Elixir Tips](https://medium.com/blackode/10-killer-elixir-tips-2a9be1bec9be)

## 1. 多个[OR]

```elixir
# Regular Approach
find = fn(x) when x>10 or x<5 or x==7 -> x end

# Our Hack
hell = fn(x) when true in [x>10,x<5,x==7] -> x end
```

## 2. i(term)
打印任何term的数据类型信息，请在`iex`中尝试一下如此神奇的魔法

```elixir
iex> i(1..5)
```

## 3. 自定义iex配置
将下面的内容保存为`.iex.exs`并放至home目录中，然后打开iex查看奇妙的变化吧

```elixir
# IEx.configure colors: [enabled: true]
# IEx.configure colors: [ eval_result: [ :cyan, :bright ] ]
IO.puts IO.ANSI.red_background() <> IO.ANSI.white() <> " ❄❄❄ Good Luck with Elixir ❄❄❄ " <> IO.ANSI.reset
Application.put_env(:elixir, :ansi_enabled, true)
IEx.configure(
 colors: [
   eval_result: [:green, :bright] ,
   eval_error: [[:red,:bright,"Bug Bug ..!!"]],
   eval_info: [:yellow, :bright ],
 ],
 default_prompt: [
   "\e[G",    # ANSI CHA, move cursor to column 1
    :white,
    "I",
    :red,
    "❤" ,       # plain string
    :green,
    "%prefix",:white,"|",
     :blue,
     "%counter",
     :white,
     "|",
    :red,
    "▶" ,         # plain string
    :white,
    "▶▶"  ,       # plain string
      # ❤ ❤-»" ,  # plain string
    :reset
  ] |> IO.ANSI.format |> IO.chardata_to_string
)
```

![iex custom configuration](https://cdn-images-1.medium.com/max/1600/1*iy-IELdB8fjTo5H0sABlBQ.png)

## 4. 自定义Sigils
让名为`x`的sigil调用各自的`sigil_x`定义

### 自定义Sigils
```elixir
defmodule MySigils do
  #returns the downcasing string if option l is given then returns the list of downcase letters
  def sigil_l(string,[]), do: String.Casing.downcase(string)
  def sigil_l(string,[?l]), do: String.Casing.downcase(string) |> String.graphemes
  #returns the upcasing string if option l is given then returns the list of downcase letters
  def sigil_u(string,[]), do: String.Casing.upcase(string)
  def sigil_u(string,[?l]), do: String.Casing.upcase(string) |> String.graphemes
end
```

### 用法
```shell
iex> import MySigils
iex> ~l/HELLO/
"hello"
iex> ~l/HELLO/l
["h", "e", "l", "l", "o"]
iex> ~u/hello/
"HELLO"
iex> ~u/hello/l
["H", "E", "L", "L", "O"]
```

## 5. 自定义错误

```elixir
defmodule BugError do
   defexception message: "BUG BUG .." # message is the default
end
```

### 用法

```shell
$ iex bug_error.ex
iex> raise BugError
** (BugError) BUG BUG ..
iex> raise BugError, message: "I am Bug.."
** (BugError) I am Bug..
```

## 6. 从嵌套的Map中取值
`get_in`函数可以通过用键组成的列表在嵌套的Map结构中取出对应的值

```elixir
nested_map = %{ name: %{ first_name: "blackode"} }     # Example of Nested Map
first_name = get_in(nested_map, [:name, :first_name])  # Retrieving the Key

# Returns nil for missing value
nil = get_in(nested, [:name, :last_name])              # returns nil when key is not present
```

参考文档：[Kernel.get_in/2](https://hexdocs.pm/elixir/Kernel.html#get_in/2)

## 7. with语句

特殊形式（special form）`with`用于按顺序进行一系列的模式匹配，如果所有子句匹配成功，则返回`do:`中的结果。
但如果其中有子句无法成功匹配，则立即返回未匹配表达式的结果

```elixir
iex> with 1 <- 1+0,
          2 <- 1+1,
          do: IO.puts "all matched"
"all matched"
iex> with 1 <- 1+0,
          2 <- 3+1,
          do: IO.puts "all matched"
4
## since  2 <- 3+1 is not matched so the result of 3+1 is returned.
```

## 8. 编写Protocol

### 定义一个Protocol

协议（Protocol）可以根据参数的类型将其向特定函数进行分派（dispatch）。
在下面的例子中，宏`defprotocol`和`defimpl`用于定义不同类型的协议和协议实现。

```elixir
defprotocol Triple do
  def triple(input)
end

defimpl Triple, for: Integer do
  def triple(int) do
    int * 3
  end
end

defimpl Triple, for: List do
  def triple(list) do
    list ++ list ++ list
  end
end
```

### 用法

在`iex`中加载代码并运行
```shell
iex> Triple.triple(3)
9
Triple.triple([1, 2])
[1, 2, 1, 2,1,2]
```

## 9. 三元操作符
Elixir中并没有类似`true ? "yes" : "no"`这样的三元操作符，不过下面的写法一样可行

```elixir
"no" = if 1 == 0, do: "yes", else: "no"
```

## 10. ????
我故意没有第10个技巧，如果您恰巧有觉得不错的技巧，请在下面的评论中共享出来吧！


如果您喜欢的话，请给原作者[点赞](https://medium.com/blackode/10-killer-elixir-tips-2a9be1bec9be)
吧


未完待续...