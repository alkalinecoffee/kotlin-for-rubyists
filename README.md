# Kotlin for Rubyists

## Getting Started

### 1. Installation

```
brew install kotlin
```

### 2. Interactive shell

```
kotlinc-jvm
```

See https://kotlinlang.org/docs/tutorials/command-line.html for more.

## Basics

### 1. Defining Variables

In Ruby, variables are dynamically typed, in that object types are determined at runtime.  Variables can generally have their values reassigned freely. There are no restrictions on what object types can be assigned to existing variables.

```ruby
str = 'abc'
str = 123
```

Kotlin has a static type system, in that a variable must always conform to a type specified at compile time.

```kotlin
// if assigned immediately, the type can be inferred
var str = "abc"

// or assign with a type
var str: String = "abc"

// if the value is to be assigned later, a type is required:
var str: String

// assigning an existing value of a different type is not allowed
str = 123 // compiler error
```

### 2. Variable Mutability

In Ruby, all variables are mutable.

```ruby
str = 'abc'
str = 'apples'
str = 123
```

In Kotlin, there are read-only variables and mutable variables. Read-only variables can only be assigned once (similar to `private` in Java). The difference is in how they are instantiated.

Read-only variables are instantiated with `val`:

```kotlin
val str: String
str = 'abc'
str = 'apples' // compiler error
```

Mutable variables are instantiated with `var`.

```kotlin
var str: String
a = 'abc'
a = 'apples' // this is OK
```

### 3. String Interpolation

In Ruby, interpolation in strings is straight forward:

```ruby
name = 'Joe'
puts "The name #{name} is #{name.length} characters long."
```

In Kotlin, the syntax is very similar:

```kotlin
val name: String = 'Joe'
println("The name ${name} is ${name.length} characters long.")
```

### 4. If/Unless

Ruby:

```ruby
if a > b
  max = a
else
  max = b
end

# with assignment:
max = if (a > b)
  a
else
  b
end


# shorthand assignment:
max = a > b ? a : b
```

Test cases in Kotlin must be surrounded by parenthesis and blocks are wrapped in curly braces, but otherwise are similar:

```kotlin
if (a > b) {
  max = a
} else {
  max = b
}

// with assignment:
val max = if (a > b) {
  a
} else {
  b
}

// shorthand assignment:
val max = if (a > b) a else b
```

### 5. Case/Switch Operator

Ruby has a very straight-forward case operator:

```ruby
x = 2

case x
when 1
  puts 'x is 1'
when 2, 3
  puts 'x is 2 or 3'
else
  puts 'x is neither 1 nor 2'
end
```

Similar functionality exists with Kotlin, but with some enhancements. Notice that extra functionality for testing ranges (with `in`/`!in`) or types (with `is`) is available, that functions can be used for the test, and that blocks can be specified for result handling.

```kotlin
when (x) {
  1 -> print("x is 1")
  2, 3 -> print("x is 2 or 3")
  in 4..6 -> print("x is between 4 and 6")
  !in 7..8 -> print("x is not between 7 and 8")
  isNine(x) -> print("x is 9")
  is String -> print("x is a string")
  else -> {
    print("x is something else")
  }
}
```

### 6. Loops

Ruby:

```ruby
collection.each do |x|
  puts x
end

// shorthand
collection.each(&:puts)
```

Kotlin:

```kotlin
for (x: Int in collection) {
  print(x)
}

// shorthand
for (x in collection) print(x)
```

## Functions

In Ruby, methods are defined with `def`.

### 1. Syntax

```ruby
def sum(a, b)
  puts a + b
end
```

In Kotlin, functions are defined in the format:

```
fun functionName([arg: argType = <defaultValue>]): <returnType>
```

Argument and return types must be specified:

```kotlin
fun sum(a: Int, b: Int): Int {
  return a + b
}
```

If a function does not return a value, the type `Unit` may be used, but convention is to simply omit the return type completely.

```kotlin
fun sayHello() {
  println("Hello!")
}

// same as

fun sayHello(): Unit {
  println("Hello!")
}
```

### 2. Default Arguments

Ruby supports default arguments for cases where the caller does not provide them. Ruby 2.0 also introduced named arguments, giving the developer more flexibility in how they call methods.

```ruby
def sum(a: 1, b: 2)
  a + b
end

sum()     #=> 3
sum(1, 4) #=> 5
sum(b: 4) #=> 5

# parenthesis are optional
sum 1, 3
```

Similar functionality exists in Kotlin.  The main difference in calling a function is that parenthesis are always required.

```kotlin
fun sum(a: Int = 1, b: Int = 2): Int {
  return a + b
}

sum()      #=> 3
sum(b = 4) #=> 5
sum #=> compiler error
```

### 3. Argument Mutability

In Ruby, method arguments are mutable.

```ruby
def add(x, y)
  x += y
end
```

In Kotlin, arguments by default are immutable. Reassigning of an argument requires reassigning the variable.

```kotlin
fun add(x: Int, y: Int): Int {
  var value = x
  value = x + y
  return value
}
```

Alternatively, the variable can be _shadowed_, or assigned to a variable of the same name. This will cause a compiler warning, but is otherwise valid.

```kotlin
fun add(x: Int, y: Int): Int {
  var x = x
  x += y
  return x
}
```

### 5. Return Values

Return values are implicit in Ruby. The final value of the method is always returned, unless `return` is used to to return to the caller early.

```ruby
def return_an_int
  123
  # or
  return 123
end
```

In Kotlin, use for `return` depends on the structure of the function. If the function has a block body and a return type specified, `return` must be used. However, `return` is not needed if the function is written with a shorthand expression body (that is, without a block body).

```kotlin
fun returnAnInt(): Int {
  return 123
}

fun returnAnInt(): Int = 123

fun returnNothing() {
  "this function returns nothing, so 'return' keyword not necessary"
}
```

### 4. Procs/Lambdas

In Ruby, methods can accept blocks as arguments.

```ruby
def contains_even?(collection)
  collection.any? { |x| x % 2 == 0 }
end

contains_even?([1, 2, 3])
#=> true
```

Higher-order functions (functions that accept and/or return a function) exist in Kotlin:

```kotlin
fun containsEven(collection: Collection<Int>): Boolean {
  return collection.any { x -> x % 2 == 0 }
}

containsEven(listOf(1, 2, 3))
#=> true
```

In Ruby, lambdas can be assigned to variables and called directly:

```ruby
sum -> { |x, y| x + y }

sum.call(1, 2) #=> 3
```

Again, same thing in Kotlin. As long as the return type is inferrable from the function parameters, it can be left out.

```kotlin
val sum = { x: Int, y: Int -> x + y }

# or with return type specified

val sum: Int = { x: Int, y: Int -> x + y }

sum(1, 2) #=> 3
```

### 5. Single Expression Functions

Methods in Ruby can be written in a single expression manner, with semicolons used optionally:

```ruby
def double(x) x * 2 end
def double(x); multiply(x, 2); end
```

Kotlin supports single expression functions. Notice that in certain cases, the return type is omitted as it can be inferred by the compiler:

```kotlin
fun double(x: Int) = x * 2
fun double(x: Int): Int = multiply(x, 2)
```

### 6. Local Functions

Although rarely used in this fashion, Ruby has support for defining methods inside of other methods.

```ruby
def add_one(x)
  def add_one_to_x
    x += 1
  end
  add_one_to_x
end

x(2) #=> 3
```

Similar functionality exists in Kotlin:

```kotlin
fun add_one(x: Int): Int {
  fun addOneToX(): Int = x + 1
  return addOneToX()
}

x(2) #=> 3
```

## Classes

### 1. Syntax

In Ruby, classes are core to the language, as they describe every object instance used within the language. Instances are instantiated through the `initialize` method, where instance variables are typically set. Instances are instantiated using the `#new` method.

```ruby
def Person
  def initialize(name)
    @name = name
  end
  
  def say_hello
    puts "Hi, my name is #{@name}"
  end
end

eminem = Person.new('Slim Shady')
eminem.say_hello #=> "Hi, my name is Slim Shady"
```

In Kotlin, the parameters for creating an object (called _properties_) are specified when the class is declared. Just like with function parameters, these are immutable by default, so the `var` keyword can be used to specify mutable properties. Instances don't require `#new`--just the class and any required properties are required. As with code blocks in Kotlin, class declarations are wrapped in curly braces.

```kotlin
class Person {
  var name: String?
  fun sayHello() {
    println("Hi, my name is ${name}")
  }
}

eminem = Person("Slim Shady")
eminem.sayHello() #=> "Hi, my name is Slim Shady"
```

### 2. Getters/Setters

In Ruby, `attr_reader`/`attr_writer`/`attr_accessor` may be used to specify which instance variables are accessible externally and whether they are settable.

```ruby
class City
  attr_reader :name
  attr_accessor: population
  
  def initialize(name, population=nil)
    @name = name
    @population = population
  end
end

city = City.new('Springfield')
city.name #=> "Springfield"
city.population = 10000
```

Similarly, the `val` and `var` keywords used for determining mutability can be used in the class declaration and/or the class body. If the mutability keyword is omitted, the property simply isn't accessible externally. Specifying properties in the class header is that class's _primary constructor_.

```kotlin
class City(val name: String) {
  var population: Integer // this could also have been placed in the line above
}

val city = City("Springfield")
city.name #=> "Springfield"
city.population = 10000
```

### 3. Custom Getters/Setters

In Ruby, there is no special built-in functionality for a custom getter/setter. Instead, custom methods can be used to replace the built-in getter and setter for a variable.

```ruby
class City
  def name
    @name.upcase
  end
  
  def name=(n)
    @name = n
  end
end

city = City.new
city.name = 'Springfield'
city.name #=> "SPRINGFIELD"
```

In Kotlin, custom getters and setters can be directly attached to the property at declaration. The format for this is:

```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

Similar to javascript, the `this` keyword can be used to read from the current object. In the `set()` function, `field` can be used to reference the current property.

```kotlin
class City {
  var name: String
      get() = this.toUpperCase()
      set(value) = {
        field = value
      }
}
```

### 4. Initializers

In Ruby, an initializer is specified by the `#initialize` method. This method can only be specified once.

```ruby
class Person
  attr_reader :name, :city, :state, :occupation
  
  def initialize(name, city, state=nil)
    if city == 'New York'
      state = city
    end
    
    if name == 'Jerry'
      occupation = 'Comedian'
    end
  end
end
```

Similar to Ruby, an `init` block in Kotlin may be specified to provide more logic around instance creation. In fact, multiple initilizer blocks may be provided and are run in the order in which they are written, and can be interwoven with property declaration. Also notice the `lateinit` keyword: this allows you to specify a property that will be set at a later time (otherwise, you'll be forced to set a value at property declaration).

```kotlin
class Person(val name: String, val city: String) {
  lateinit var state: String // tell the compiler that you'll set this later on
  
  init {
    if (city == "New York") {
      state = "New York"
    }
  }
  
  val occupation: String
  
  init {
    if (name == "Jerry") {
      occupation = "Comedian"
    }
  } 
}
```

### 5. Class Inheritance

In Ruby, all classes are subclasses of `Object`. Classes can be subclassed easily with the `MyClass < Superclass` syntax. Calling a method of the same name is done via `super`.

```ruby
class Animal
  attr_reader :name
  
  def initialize(name)
    @name = name
  end
end

class Dog < Animal
end

Dog.new('Baxter').name #=> "Baxter"
```

In Kotlin, all classes are subclasses of `Any`, which provides only a handful of built-in functions. One of the main differences is that classes that act as a superclass must be specified with the `open` keyword, denoting that they are available for subclassing. Another difference is that if the base class has a primary constructor, those values must be set once the subclass is declared.

```kotlin
open class Animal(var name: String)

class Dog(name: String) : Animal(name)

Dog("Baxter").name #=> "Baxter"
```

### 6. Function Overriding

Ruby allows you to create a method in a subclass that overrides a method of the same name defined in the superclass.

```
class Animal
  def talk
    puts '...'
  end
end

class Cat < Animal
  def talk
    puts 'meow!'
  end
end
```

In Kotlin, functions are not overrideable by default. Kotlin is designed to force the developer to be explicit in their intention. Similar to how you must specify subclass-able classes with the `open` keyword, you must annotate any overrideable functions with `open`, and any functions that are meant to override the function declaration in the parent class must be annotated with `override`:

```kotlin
open class Animal {
  fun notOverrideable() {}
  
  open fun talk() {
    println("...")
  }
}

class Cat : Animal() {
  override fun talk() {
    println("meow!")
  }
}
```

### 7. Calling the Superclass Implementation

In Ruby, `super` is a reserved keyword that allows you to call the superclass' method of the same name.

```ruby
class Motorcycle
  def wheels
    2
  end
end

class Trike < Motorcycle
  def wheels
    super + 1
  end
end

Trike.new.wheels #=> 3
```

In Kotlin, the `super` keyword also exists as a reference to the superclass. However, you cannot call `super` by itself--you must call the specific function of the superclass.

```kotlin
open class Motorcycle {
  open fun wheels() = 2
}

class Trike : Motorcycle() {
  override fun wheels() = super.wheels() + 1
}

Trike().wheels() #=> 3
```

## Modules/Interfaces

### 1. Syntax

In Ruby, modules are commonly used as a way to re-use code by including modules in various classes.

```ruby
module HelloHelper
  def say_hello_to(other_person)
    puts "Hello, #{other_person}"
  end
end

class Person
  include HelloHelper
  
  def initialize(name)
    @name = name
  end
end

newman = Person.new('Newman')
newman.say_hello_to('Jerry') #=> "Hello, Jerry"
```

In Kotlin, similar functionality is provided by interfaces. Interfaces, much like modules in Ruby, are declared similarly to classes. 

```kotlin
interface HelloHelper {
  fun sayHelloTo(otherPerson: String) {
    println("Hello, ${otherPerson}")
  }
}

class Person(name: String) : HelloHelper

val jerry = Person("Jerry")
jerry.sayHelloTo("Newman") #=> "Hello, Newman"
```

If multiple interfaces are to be implemented, simply pass them as a list:

```kotlin
class Person : InterfaceOne, InterfaceTwo, InterfaceThree
```

