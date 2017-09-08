# Method arguments in ruby

While coding a project I just noticed I didn't had a clear view on all the available options of methods arguments in ruby. Let's take this occasion to make a quick terse review of Ruby arguments possiblities.

Basically we can break up the arguments in three main categories. 

## 1. Positional arguments 

### Required  

Those arguments are the most used ones, their position matter and they are required.

```ruby
def make_coffee(type,size,temperature)
    "Serving a #{size} #{temperature} #{type}"
end 

make_coffee('latte','medium','hot') #=> Serving a medium hot latte"
```

If you omit one of the argument ruby will raise an `ArgumentError` exception that tells you how many arguments are missing.

### With default values 

You can avoid required arguments by declaring default values (if no value for the argument is supplied, the default value will be used instead).

```ruby
def make_coffee(type,size = 'medium',temperature = 'hot')
    "Serving a #{size} #{temperature} #{type}"
end 

make_coffee('latte') #=> Serving a medium hot latte"
```

Arguments taking default values can be put anywhere in the method signature but they must always be grouped.

```ruby
def make_coffee(type = 'latte',size,temperature='hot') #=> not allowed 
def make_coffee(type = 'latte',temperature='hot',size) #=> allowed 
```

## 2. Keyword arguments 

### Required  

Let's now focus on keyword arguments, their position don't matter and they are required.

```ruby
def make_coffee(type:,size:,temperature:)
    "Serving a #{size} #{temperature} #{type}"
end 

make_coffee(type: 'latte',temperature: 'hot', size: 'medium') #=> Serving a medium hot latte"
```

The two main advantage of keyword arguments is readibility and sequence independence. 
  - **Readibility**: By specificaly naming the arguments during the call you don't have to look at the method signature to understand the semantic of the variable being passed. 
  - **Position independence**: Because every argument is named, the position in which the arguments are passed don't matter.

If you omit one of the argument ruby will also raise an `ArgumentError` exception. But this time it will tell you which argument is missing. 

### With default values 

As with positional arguments default values can be also be declared. 

```ruby
def make_coffee(type:,size: 'medium',temperature: 'hot') 
    "Serving a #{size} #{temperature} #{type}"
end 

make_coffee(type: 'latte',size: 'small') #=> Serving a small hot latte"
```

Because ruby can now exactly identify each arguments, default values can be defined anywhere 

```ruby
def make_coffee(type = 'latte',size,temperature='hot') #=> allowed
```

### Keyword vs options hash 

Prior to 2.0 (no keyword arguments), ruby developer were using options hash to attain the same readibility and position 
independence of arguments. 

```ruby
def make_coffee(type,options = {})

  default_options = {
    size: 'medium',
    temperature: 'hot'
  }

  options = default_options.merge(options)
  "Serving a #{options[:size]} #{options[:temperature]} #{type}"
end

make_coffee('latte',temperature: 'hot', size: 'small') #=> Serving a medium hot latte"
```

## 3. Optional arguments 

Last but not least the optional arguments allow you to pass any number of arguments to a ruby method.

### Using the splat operators (*)

The splat operator will capture all remaining arguments and transform it into an array.

```ruby
def make_coffee(type,*options) 
  "Serving a #{options[0]} #{options[1]} #{type}"
end 

make_coffee('latte','medium','hot') #=> Serving a medium hot latte"
```

### Using the double splat operator (**)

The double splat operator will capture all remaining **Keyword** arguments and transform it into a hash.

```ruby
def make_coffee(type,**options) 
  "Serving a #{options[:size]} #{options[:temperature]} #{type}"
end 

make_coffee('latte',size: 'medium',temperature: 'hot') #=> Serving a medium hot latte"
```

