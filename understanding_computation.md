## 刚好够用的Ruby基础

### 值

> Ruby是面向表达式的语言，每一段有效的代码执行之后都要产生一个值
> 支持布尔型、数值型和字符串
> 符号类型以:开头，是一个轻量级的、不可变值，作为字符串的简单化、非内存密集化的替身

```ruby
(true && false) || true
(3+3) * (14/2)
'hello' + 'world'
:my_symbol == :my_symbol
```

> 数据结构

```ruby
# array
numbers = ['zero', 'one', 'two']
numbers[1]
numbers.push('three', 'four')
numbers.drop(2)

# range
ages = 18..30
ages.entries
ages.include?(19)
ages.include?(33)

# hash
fruit = {'a' => 'apple', 'b' => 'banana', 'c' => 'coconut'}
fruit['b']
fruit['d'] = 'date'
fruit

dimensions = { width: 1000, height: 2000, depth: 200 }
dimensions[:depth]
```
> proc是一段未经求值的Ruby代码，根据需要进行求值
```ruby
multiply = -> x, y { x * y }
multiply.call(6, 9)
multiply.call(2, 3)
multiply[3, 4]
```
> 控制流
```ruby
if 2 < 3 
    'less'
else
    'more'
end

quantify = 
-> number {
    case number
    when 1
        'one'
    when 2
        'a couple'
    else
        'many'
    end
}

quantify.call(2)
quantify.call(10)

when x < 1000
    x = x * 2
end
```

### 对象和方法


