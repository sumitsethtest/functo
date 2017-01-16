# Functo

Composable method objects in ruby.

## Usage

```ruby
class AddsOne
  include Functo.call :add, :number

  def add
    number + 1
  end
end

AddsOne[1]
# => 2

class Multiplies
  include Functo.call :multiply, :foo, :bar

  def multiply
    foo * bar
  end
end

Multiplies[2, 3]
# => 6
```

Functo objects can be used in place of a `Proc`.

```ruby
[1, 2, 3].map(&AddOne)
# => [2, 3, 4]
```

### Composition

```ruby
MultipliesAddsOne = Multiplies >> AddsOne

MultipliesAddsOne[2, 3]
# => 7
```

`>>` splats intermediate results. Use `>` to compose without splatting intermediate results.

```ruby
class SplitDigits
  include Functo.call :split, :number

  def split
    number.to_s.split(//).map(&:to_i)
  end
end

class Sum
  include Functo.call :sum, :arr

  def sum
    arr.reduce(:+)
  end
end

SumDigits = SplitDigits >> Sum
SumDigits[1066]
# => ArgumentError: wrong number of arguments (4 for 1)

SumDigits2 = SplitDigits > Sum
SumDigits2[1066]
# => 13
```

Any object that responds to `call` can be made composable.

```ruby
SquareRoots = Functo.wrap ->(n) { Math.sqrt(n) }

SquareRootsAddsOne = SquareRoots >> AddsOne
SquareRootsAddsOne[16]
# => 5.0
```

### Filters

```ruby
class DividesTwo
  include Functo.call :divide, number: ->(n) { Float(n) }

  def divide
    2 / number
  end
end

DividesTwo['4']
# => 0.5
```

A filter can be any object that responds to `call` or `[]`.

For example using [dry-types](https://github.com/dry-rb/dry-types).

```ruby
require 'dry-types'

module Types
  include Dry::Types.module
end

class Squares
  include Functo.call :square, number: Types::Strict::Int

  def square
    number**2
  end
end

Squares[4]
# => 16

Squares['4']
# => Dry::Types::ConstraintError: "4" violates constraints
```

## Acknowledgements

Functo was inspired by:

* [concord](https://github.com/mbj/concord) by mbj
* [procto](https://github.com/snusnu/procto) by snusnu

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/sbscully/functo.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

