# Objects Equality In Ruby 

Every object oriented programming language provide a set of methods to test objects equivalence. 
Ruby is no different and propose a whopping four methods to test equality between objects. 
Let's quickly dig into each of them. 

### `equal?` or how to test object identity

This method is inherited from [Object](https://ruby-doc.org/core-2.4.1/Object.html#method-i-eql-3F) and test object identity (both objects have the same object_id hence point to the same memory address).

```ruby
class MovieCharacter
  attr_reader :name

  def initialize(name)
    @name = name
  end
end

rick = MovieCharacter.new('Rick Deckard')
roy = MovieCharacter.new('Roy Batty')
rick.equal? roy               #=> false
rick.equal? rick              #=> true
````

Because testing for object identity is always the same and must stay consistent across all objects,
this method **should never** be overwritten.

### `==` or how to test object state equivalence

This is the method that you should overwrite if you want to test state equivalence between your objects. It's default implementation is inherited from [Object](https://ruby-doc.org/core-2.4.1/Object.html#method-i-eql-3Fis) and test for identity (same as `equal?`).

```ruby
class MovieCharacter
  attr_reader :name

  def initialize(name)
    @name       = name
  end

  # Same if they are indeed MovieCharacter and have the same name
  def ==(other)
    return false unless other.instance_of?(self.class)
    name == other.name
  end
end

henry_jr_jones  = MovieCharacter.new('Indiana Jones')
indiana_jones   = MovieCharacter.new('Indiana Jones')
marcus_brody    = MovieCharacter.new('Marcus Brody')

henry_jr_jones  == indiana_jones      #=> true
indiana_jones   == marcus_brody       #=> false
```

***A few basic rules to follow when implementing state equivalence***

In order to avoid unexpected behaviour, the following rules should be enforced when implementing the `==` method

| Rules         | Definition
| ------------- |-------------
| Reflexivity   | x == x should return true
| Symmetry      | if x == y then y == x
| Transitivity  | if a == b and b ==c then a == c
| Non-nullity   | x == nil should return false

### `===` or how to test subsumption between objects

This method has in fact no relationship with equality but rather test if one object can be subsumed/classified under another object. This method is called the ***case subsumption*** method because it is used by case statements to provide meaningfull semantics.

```ruby
case 'Brian'
  when /Brian/
    puts 'Alright, I\'m the messiah. Now bug off !'
end 
```

Is the same as 
```ruby
if /Brian/ === 'Brian'
  puts 'Alright, I\'m the messiah. Now bug off !'
end 
```

A few Ruby classes are already overwritting this method, let's take a quick look of interesting cases:

```ruby
(1..100) === 6                      #=> true
(1..100) === 101                    #=> false
Integer === 100                     #=> true
100 === Integer                     #=> false   Note the asymetricality here
/Brian/ === 'Brian'                 #=> true
```

By default this method is inherited from [Object](https://ruby-doc.org/core-2.4.1/Object.html#method-i-3D-3D-3D) and will first test for object identity, if it is not the case it delegate to the `==` method.

### `eql?` or how to use your object as a key in a hash 

The sole purpose of this method is to test hash key equivalence. In other words, this is one of the two methods (the other would be `hash`) that you have to overwrite if you want your object to be findable as a key in a hash. 

The `hash` method indicate in which bucket to search, while the `eql?` method test the state equivalence of the object. 

```ruby
class MovieCharacter
  attr_reader :name
  
  def initialize(name)
    @name = name
  end
end

characters = { MovieCharacter.new('Keyser Soze')  => 'Kevin Spacey'} 
puts characters[MovieCharacter.new('Keyser Soze')]        #=> nil

class MovieCharacter
  def hash
    [MovieCharacter,name].hash
  end 

  def eql?(other)
    return false unless other.instance_of?(self.class)
    name == other.name
  end 
end 

characters = { MovieCharacter.new('Keyser Soze')  => 'Kevin Spacey'} 
puts characters[MovieCharacter.new('Keyser Soze')]        #=> Kevin Spacey
```
 
By default, `eql?` is inherited from [Object](https://ruby-doc.org/core-2.4.1/Object.html#method-i-eql-3Fis) and has the same implementation (what the documentation call synonymous) as the [Object](https://ruby-doc.org/core-2.4.1/Object.html#method-i-eql-3Fis) `==` method. It is considered good practice to follow the same rule for descendant classes, meaning to ensure that `==` and `eql?` always return the same consistent answer. 

One quick way to do this is to alias `eql?` to `==`  

```ruby
class MovieCharacter
  def hash
    [MovieCharacter,name].hash
  end 

  def ==(other)
    return false unless other.instance_of?(self.class)
    name == other.name
  end 

  alias_method :eql?, :==
end 
```
What about array you might ask? As array is a specific case of hash, `hash` and `eql?` will also be used to determine object equivalence inside an array. 

```ruby
class MovieCharacter
  attr_reader :name
  
  def initialize(name)
    @name = name
  end
end

characters = [ MovieCharacter.new('Keyser Soze'), MovieCharacter.new('Keyser Soze') ]
puts characters.uniq.size       #=> 2

class MovieCharacter
  def hash
    [MovieCharacter,name].hash
  end 

  def eql?(other)
    return false unless other.instance_of?(self.class)
    name == other.name
  end 
end 

characters = [ MovieCharacter.new('Keyser Soze'), MovieCharacter.new('Keyser Soze') ]
puts characters.uniq.size       #=> 1
````

### Closing advice 

To wrap it up, it is considered polite behaviour when implementing value objects to 

1. Do not overwrite `equal?`
2. Overwrite `==` based on object state and on the class of the object. 
3. Ensure the four rules for the `==` method are well respected.  
4. Override `hash`  
5. Alias `eql?` to `==` 

