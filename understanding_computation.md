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

> ruby中每个值都是一个对象，彼此之间靠发消息通信，每个对象都有自己的方法集合，决定了这些对象如何响应消息
> 使用def定义自己的方法
```ruby
o = Object.new
def o.add(x, y)
    x + y
end
```
> ruby通过self追踪当前对象。在所有方法定义之外，当前对象是特殊顶层对象main,没有指明任何接收者对象的消息都会发给它。

### 类和模块

```ruby
class Calculator
    def divide(x, y)
        x / y
    end
end

c = Calculator.new
c.class
c.divide(10 ,2)

class MultilplyCalculator < Calculator
    def multiply(x, y)
        x * y
    end
end

mc = MultiplyCaculator.new
mc.class
mc.class.superclass

module Addition
    def add(x, y)
        x + y
    end
end

```

### 其他特性

> 局部和赋值
```ruby
greeting = 'hello'
width, height, depth = [1000, 2000, 350]
```

> 字符串插值
> 如果被插入的表达式返回的不是一个字符串类型的对象，这个对象就会自动收到一个to_s消息
```ruby
"hello #{'dlrow'.reverse}"
```
> 检查对象

```ruby
o = Object.new
def o.inspect
    '[my object]'
end
```

> 打印字符串
```ruby
x = 128
while x < 1000
    puts "x is #{x}"
    x = x + 2
end
```
> 可变参数方法
```ruby
def join_with_commas(*words)
    words.join(', ')
end

def join_with_commas(before, *words, after)
    before + words.join(', ') + after
end
```

> 代码块
```ruby
def do_three_times
    yield
    yield
    yield
end

do_three_times { puts 'hello' }

def do_three_times
    yield('first')
    yield('second')
    yield('third')
end

do_three_times { |n| puts "#{n}: hello" }
```

> 枚举类型： Enumerable模块，被数组、散列表、范围以及其他标识值的集合的类包含。
```ruby
(1..10).count { |number| number.even? }
(1..10).select { |number| number.even? }
(1..10).any? { |number| number < 8 }
(1..10).all? { |number| number < 8 }
(1..10).each do |number|
    if number.even?
        puts "#{number} is even"
    else
        puts "#{number} is odd"
    end
end
```
> 发生消息缩写&:message
```ruby
(1..10).select(&:even?)
['one', 'two', 'three'].map(&:upcase)
['one', 'two', 'three'].map(&:chars)
['one', 'two', 'three'].flat_map(&:chars)
(1..10).inject(0) { |result, number| result + number }
(1..10).inject(1) { |result, number| result * number }
(1..10).inject('Words:') { |result, word| "#{result} #{word}" |
```

> 结构体

```ruby
class Point < Struct.new(:x, :y)
    def +(other_point)
        Point.new(x + other_point.x, y + other_point.y)
    end
end
```

> 给内置对象扩展方法，match pacthing

```ruby
class Point
    def -(other_point)
        Point.new(x - other_point.x, y - other_point.y)
    end
end

class String
    def shout
        upcase + '!!!'
    end
end
```

> 定义常量: 任意以大写字母开头的变量都是常量
```ruby
NUMBERS = [4, 8. 15, 15, 20, 21]

class Greetings
    ENGLISH = 'hello'
    FRENCH = 'bonjour'
    GERMAN = 'guten Tag'
end
```

> 删除常量
```ruby
NUMBER.last
Object.send(:remove_cast, :NUMBERS)
Object.send(:remove_cast, :Greetings)
```


## 程序的含义

###　小步语义

> 又名**结构化操作语义**(structural operational semantic),**转换语义**(transition semantic)

```ruby
class Number < Struct.new(:value) 
end

class Add < Struct.new(:left, :right)
end

class Multiply < Struct.new(:left, :right)
end

class Number
    def to_s
        value.to_s
    end

    def inspect
        "<#{self}>"
    end
end

class Add
    def to_s
        "#{left} + #{right}"
    end

    def inspect
        "<#{self}>"
    end
end

class Multiply
    def to_s
        "#{left} * #{right}"
    end

    def inspect
        "<#{self}>"
    end
end

def reducible?(expression)
    case expression
    when Number
        false
    when Add, Multiply
        true
    end
end

class Number
    def reducible?
        false
    end
end

class Add
    def reducible?
        true
    end
end

class Multiply
    def reducible?
        true
    end
end

class Add
    def reduce(environment)
        if left.reducible?
            Add.new(left.reduce(environment), right)
        elsif right.reducible?
            Add.new(left, right.reduce(environment))
        else
            Number.new(left.value + right.value)
        end
    end
end

class Multiply
    def reduce(environment)
        if left.reducible?
            Multiply.new(left.reduce(environment), right)
        elsif right.reducible?
            Multiply.new(left, right.reduce(environment))
        else
            Number.new(left.value * right.value)
        end
    end
end

Object.new(:remove_const, :Machine)

class Machine < Struct.new(:statement, :environment)
    def step
        self.statement, self.environment = statement.reduce(environment)
    end

    def run
        while statement.reducible?
            puts "#{statement}, #{environment}"
            step
        end
        puts "#{statement}, #{environment}"
    end
end

class Boolean < Struct.new(:value)
    def to_s
        value.to_s
    end

    def inspect
        "<<#{self}>>"
    end

    def recible?
        false
    end
end

class LessThan < Struct.new(:left, :right)
    def to_s
        "#{left} < #{right}"
    end

    def inspect
        "<<#{self}>>"
    end

    def reduce(environment)
        if left.reducible?
            LessThan.new(left.reduce(environment), right)
        elsif right.reducible?
            LessThan.new(left, right.reduce(environment))
        else
            Boolean.new(left.value < right.value)
        end
    end
end

class Variable < Struct.new(:name)
    def to_s
        name.to_s
    end

    def inspect
        "<<#{self}>>>"
    end

    def reducible?
        true
    end

    def reduce(environment)
        environment[name]
    end
end

class DoNothing
    def to_s
        "do-nothing"
    end

    def inspect
        "<<#{self}>>"
    end

    def ==(other_statement)
        other_statement.instance_of(DoNothing)
    end

    def reducible?
        false
    end
end

class Assign : Struct.new(:name, :expression)
    def to_s
        "#{name} = #{expression}"
    end

    def inspect
        "<<#{self}>>"
    end

    def reducible?
        true
    end

    def reduce(environment)
        if expression.reducible?
            [Assign.new(name, expression.reduce(environment)), environment]
        else
            [DoNothing.new, environment.merge({ name => expression })]
        end
    end
end

class If < Struct.new(:condition, :consequence, :alternate)
    def to_s
        "if (#{condition}) { #{consequence} } else { #{alternate} }"
    end

    def inspect
        "<<#{self}>>"
    end

    def reducible?
        true
    end

    def reduce(environment)
        if condition.reducible?
            [If.new(condition.reduce(environment), consequence, alternate), environment]
        else
            case condition
            when Boolean.new(true)
                [consequence, environment]
            when Boolean.new(false)
                [alternate, environment]
            end
        end
    end
end


class Sequence < Struct.new(:first, :second)
    def to_s
        "#{first}; #{second}"
    end

    def inspect
        "<<#{self}>>"
    end

    def reducible?
        true
    end

    def reduce(environment)
        case first
        when DoNothing.new
            {second, environment}
        else
            reduced_first, reduced_environment = first.reduce(environment)
            [Sequence.new(reduced_first, second), reduced_environment]
        end
    end
end

class While < Struct.new(:condition, :body)
    def to_s
        "while (#{condition}) { #{body} }"
    end

    def inspect
        "#{self}"
    end

    def reducible?
        true
    end

    def reduce(environment)
        [If.new(condition, Sequence.new(body,  self), DoNothing.new), environment]
    end
end

```

> 小步语义的优点：适合需要反复执行、逐步推进的迭代场景
> 小步语义的缺点：不够直接，没有体现整个程序是如何执行的，只展示了是每一步是如何慢慢规约的。

> 应用：Scheme中的R6RS, PLT Redex, OCaml


### 大步语义

> 又名**自然语义**(natural semantic)，**关联语义**(relational semantic)
> 思想：定义如何从一个表达式或者语句直接得到它的结构——递归
> 大步语义的规则描述了如何只对程序的抽象语法树访问一次就计算出整个程序的结果。

```ruby
class Number
    def evaluate(environment)
        self
    end
end

class Boolean
    def evaluate(environment)
        self
    end
end

class Variable
    def evaluate(environment)
        environment[name]
    end
end

class Add
    def evaluate(environment)
        Number.new(left.evaluate(environment).value + right.evaluate(environment).value)
    end
end

class Multiply
    def evaluate(environment)
        Number.new(left.evaluate(environment).value * right.evaluate(environment).value)
    end
end

class LessThan
    def evaluate(environment)
        Number.new(left.evaluate(environment).value < right.evaluate(environment).value)
    end
end

class Assign
    def evaluate(environment)
        environment.merge({ name => expression.evaluate(environment) })
    end
end

class DoNothing
    def evaluate(environment)
        environment
    end
end

class If
    def evaluate(environment)
        case condition.evaluate(environment)
        when Boolean.new(true)
            consequence.evaluate(environment)
        when Boolean.new(false)
            alternative.evaluate(environment)
        end
    end
end

class Sequence
    def evaluate(environment)
        second.evaluate(first.evaluate(environment))
    end
end

class While
    def evaluate(environment)
        case condition.evaluate(environment)
        when Boolean.new(true)
            evaluate(body.evaluate(environment))
        when Boolean.new(false)
            environment
        end
    end
end
```

> 打开尾递归优化
```ruby
RubyVM::InstructionSequence.compile_option = {
    tailcall_optimization: true,
    trace_instruction: false,
}
```

> 应用：ML编程语言，OCaml中的核心，W3C，

### 指称语义

> 又名**不动点语义**(fixed-point semantic)，**数学语义**(mathematical semantic)
> 指称语义：从实现语言向指称语言转换，实现语言和指称语言可以不同。如从Ruby语言往JavaScript转换。
> 使用Kernel#eval把Ruby代码组成的字符串转换成可调用的Proc对象实际执行

```ruby
class Number
    def to_ruby
        "-> e { #{value.inspect } }"
    end
end

class Boolean
    def to_ruby
        "-> e { #{value.inspect} }"
    end
end

class Variable
    def to_ruby
        "-> e { e[#[name.inspect]] }"
    end
end

class Add
    def to_ruby
        "-> e { (#{left.to_ruby}).call(e) + (#{right.to_ruby}).call(e) }"
    end
end

class Multiply
    def to_ruby
        "-> e { (#{left.to_ruby}).call(e) + (#{right.to_ruby}).call(e) }"
    end
end

class LessThan
    def to_ruby
        "-> e { (#{left.to_ruby}).call(e) < (#{right.to_ruby}).call(e) }"
    end
end

class Assign
    def to_ruby
        "-> e { e.merge({ #{name.inspect} => (#{expression.to_ruby}).call(e) }) }"
    end
end

class DoNothing
    def to_ruby
        "-> e { e }"
    end
end

class If
    def to_ruby
        "-> e { if (#{condition.to_ruby}).call(e)" +
            "then (#{consequence.to_ruby}).call(e)" + 
            "else (#{consequence.to_ruby}).call(e)" +
            " end }"
    end
end

class Sequence
    def to_ruby
        "-> e { (#{second.to_ruby}).call((#{first.to_ruby}).call(e)) }"
    end
end

class While
    def to_ruby
        "-> e { " +
            " while (#{condition.to_ruby}).call(e); e = (#{body.to_ruby}).call(e); end;" +
            " e" +
            " }"
    end
end

```

> 语义类型比较：while是一个区分小步语义、大步语义和指称语义的好例子。小步语义是以抽象机器的规约规则形式写成的；大步语义以求值规则的形式写成的，规则说明了如何把最终的环境直接计算出来，包含了对本身的递归调用；支撑语义展示如何用指称语言对表示语言进行转换。

> 应用：早期Scheme标准，XSLT文本转换语言


### 形式化语义实践

> 形式化：合适的指称语义关注的是通过把程序转换成定义良好的数学对象以获得程序的核心含义
> 采用基于单调函数上不动点的一种计算模型——域理论
> 形式化语义的重要应用：为一种编程语言的含义给出一个无歧义的定义，而不是让其依赖于像自然语言规范文档和“由实现规范”这样更加随意的方法。
> 指称语义的一个优点：比操作语义抽象层次更高，它忽略了程序如何执行的细节，而只关心如何把它转换成一个不同的表示。


### 实现语法解释器

> Treetop(http://treetop.rubyforge.org/)：Ruby可用的语法解析工具，能让语法解析器自动生成。

```ruby
grammar Simple
    rule statement
        while / assign
    end

    rule while
        'while (' condition:expression ') {' body:statement '}' {
            def to_ast
                While.new(condition.to_ast, body.to_ast)
            end
        }
    end

    rule assign
        name:[a-z]+ ' = ' expression {
            def to_ast
                Assign.new(name.text_value.to_sym, expression.to_ast)
            end
        }
    end

    rule expression
        less_than
    end

    rule less_than
        left:multiply ' < ' right:less_than {
            def to_ast
                LessThan.new(left.to_ast, right.to_ast)
            end
        }
        /
        multiply
    end

    rule multiply
        left:term ' * ' right:multiply {
            def to_ast
                Multiply.new(left.to_ast, right)
        }
        /
        term
    end

    rule term
        number / variable
    end

    rule number
        [0-9]+ {
            def to_ast
                Number.new(text_value.to_i)
            end
        }
    end

    rule variable
        [a-z]+ {
            def to_ast
                Variable.new(text_value.to_sym)
            end
        }
    end
end
```

```ruby
require 'treetop'

Treetop.load('simple')
parse_tree = SimpleParser.new.parse('while (x < 5) { x = x * 3 }')

statement = parse_tree.to_ast
statement.evaluate({ x: Number.new(1) })
statement.to_ruby
```