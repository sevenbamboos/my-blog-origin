---
title: Hello Elixir
categories: Programming
date: 2018-07-30 20:13:33
tags:
---


Notes from "Programming Elixir"

<!-- more -->

# Language Basic

## Keyword Lists
Shortcut for lists of key/value pairs
```
> [id: 1, name: "abc"]
[{:id, 1}, {:name, "abc}]
```

Square brackets can be left off if a keyword list is the last argument of a function call
```
> DB.save record, id: 1, name: "abc"
# same as DB.save record, [id: 1, name: "abc"]
```

Square brackets can be left off when a keyword list appears as the last item where a list is expected
```
> [1, name: "abc", code: "001"]
# same as [1, {:name, "abc"}, {:code, "001"}]

> {1, name: "abc", code: "001"}
# same as {1, [name: "abc", code: "001"]}
# because keyword-list shortcut applies to list, so the above name and code will be grouped into a list
# although personally I encourage to put square brackets explicitly.
```

## Maps
Maps support the same shortcut as keyword list if key is an atom
```
> %{ id: 1, name: "abc" }
# same as %{ :id => 1, :name => "abc" }
```

If keys are atoms, maps also support dot notation
```
> product = %{ id: 1, name: "abc" }
> product.id
# same as product[:id]

# Note KeyError if no matching key when using dot notation instead of nil
> product.not_existing
** (KeyError)
> product[:not_existing]
nil
```

# Variable Scope
Local variables defined inside module is accessible to top level, not including inside functions
```
defmodule Foo do

  info = "Foo module information"

  def bar do
    # info is NOT visible here
    # IO.puts info
  end

  # info is visible here
  IO.puts info

end
```

To make it accessible, use module attributes. Note that the attribute value will be defined in the order of its appearance.
```
defmodule Foo do
  @attr "abc"
  def function_1, do: @attr # return abc

  @attr "123"
  def function_2, do: @attr # return 123
end
```
Attributes are not variables, but for configuration and metadata.

with Expression works effectively for temporary variables
```
name = with {:ok, file} = File.open("lib/elixir_hello.ex"),
                content = IO.read(file, :all),
                    :ok = File.close(file),
       [_, module_name] = Regex.run(~r{defmodule .*?(\w+) do}, content)
       do
         "Module Name:#{module_name}"
       end
# Note file, content and module_name are not available when with expression finishes
```

<- makes pattern matching return nil instead of KeyError
```
> with [a|_] = [1,2,3], do: a
1

> with [a|_] <- [1,2,3], do: a
1

> with [a|_] = nil, do: a
** (KeyError)

> with [a|_] <- nil, do: a
nil
```

## Function
[] and {} are operators, so literal lists and tuples can be turned into functions
```
> divrem = &{ div(&1,&2), rem(&1,&2) }
> divrem.(13, 5)
{2, 3}
```

|| and && are not allowed in guard clause. Neither for functions/operators with side-effects
```
def gcd(x, y) when x >= y and y >= 0, do: gcd(y, rem(x, y))
# not def gcd(x, y) when x >= y && y >= 0, do: gcd(y, rem(x, y))
```

When function has multiple clauses and default parameters, then a function head with no body that contains the default parameters
```
defmodule Foo do
  def bar(x, y \\ 123)
  def bar(x, y) when y > 0, do: ...
  def bar(x, y), do: ...
end
```

## List
Join operator | supports multiple values to its left
```
[1,2,3 | [4,5,6]]
```

## Map
When using pattern matching for maps, the left part can be partially matched. But the key must be provided
```
> maps = %{id: 1, name: 'abc'}
> %{id: aID} = maps
aID = 1

> %{idKey: 1} = maps
** (MatchError)
```

## Struct
The way to define a struct containing another struct. Note ```%Name{}```
```
defmodule Name do
  defstruct first_name: "", last_name: "Unknown"
end

defmodule Person do
  defstruct id: 0, name: %Name{}
end
```