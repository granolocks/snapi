# Snapi

Snapi is a modular API functionality definition tool.

[![Gem Version](https://badge.fury.io/rb/snapi.png)](http://badge.fury.io/rb/snapi)

## Usage

### Installation

Install by simply typing `gem install snapi`. This has only been tested on
`1.9.3` but is should run fine on `1.9+` and `2.x+`

### Snapi

The main access point to the functionality that has been defined is through the
top level `Snapi` module.

#### Functionality Disclosure

`Snapi` is intended to provided *functionality disclosure* so that different
APIs can be defined for different purposes quickly and in a way that self
documents and organizes the functionality.


### Capabilities

Capabilities are core to Snapis API organizational structure. A capability
represents a collection of functions and can, for many intents and purposes, be
thought of as a kind of module.

To create a capability start with a Ruby class and include the
`Snapi::Capability` module.

```ruby
require 'snapi'

class SayHello
  include Snapi::Capability
  function :hello_world

  def self.hello_world(params)
    puts "Hello World"
  end
end
```

#### Working with Snapi Capabilities

`Snapi` provides a number of helpful ways to view and access and work with
capabilities.

##### Check for the presence of a capability:

```ruby
Snapi.has_capability?(:capability)
#=> true / false
```

##### Access a specific capability by name:

```ruby
Snapi[:capability]
#=> the class in question
```
##### See all capabilities

```ruby
Snapi.capabilities
#=> Hash of Capability information

Snapi.valid_capabilities
#=> Array of the names of valid capabilities
```

### Functions

Functions are declared inside the capability class.  At a minimum the functions
take a name which maps to a class method defined on the capability class.

The minimal function as declared above looks like:

```ruby
function :hello_world
```

:exclamation: *Note:* class methods which serve functions demand having an
arity of 1. They should expect a hash containing keyed values which map to the
arguments defined in the `Snapi` function definition.

#### Arguments

Functions are handy but they are much more useful when you begin to declare
arguments for them. Lets define a more interested method for our capability.

```ruby
function :hello do |fn|
  fn.argument :friend do |arg|
    arg.required false
    arg.type :string
  end
end
```

##### Argument Attributes
###### default_value
###### format
##### list
###### required
###### type
###### values
###### name
###### description

#### Return Type

##### Pending

    fn.return :structured

### Library Class

The `library` option on a capability can be used

```ruby
library ExternalRubyClass
```

:exclamation: *Note:* This class *must* have valid class methods with an arity
of 1 for each function declared in the capability defition. 

### Sinatra Extension

A Sinatra application can be extended with this functionality as follows.

```ruby
class MyAPi < Sinatra::Base
  register Sinatra::Namespace

  namespace "/snapi" do
    register Snapi::SinatraExtension
  end
end
```

When loaded this application will offer the following routes:

```
...
# /plugins/
# /plugins/say_hello/
# /plugins/say_hello/hello_world
...
```

## Project Goals & Name

I literally woke up in the middle of the night with the idea for a modular
Sinatra Api plugin system and wrote down the name "Snapi" which is a
contraction of "Sinatra Api". It might better be spelled "SnApi" but I prefer
"Snapi" because it snake cases to `snapi`, not `sn_api`. I like the concept of
snap-in modularity.

As of now the only real behavior is that of declaring functions and arguments
for a class as a way of doing meta-programming funcationality / arrity
disclosure.

The ultimate goal being for an API to be able to define itself dynamically and
dislose that functionality to remote systems and even do things like:

* Dynamic Form Generation
* CLI Tool / Option Parser
* Self Documentation

