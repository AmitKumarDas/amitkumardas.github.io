---
layout: post
title: Learn Erlang or Elixir from code sample - Part 1
---

# Learn Erlang or Elixir from code sample - Part 1

## Study below sample elixir code & analyze its syntax

- refer - [crash course](http://elixir-lang.org/crash-course.html#anonymous-functions)
- refer - [elixir forum post](https://elixirforum.com/t/help-with-performance-file-io/802)

```
# elixir

defmodule Foo do

  def run(file_name) do
    File.open! file_name, [:read], fn f ->
      IO.stream(f, :line) |> Enum.each(&process_line/1)
    end
  end

  defp process_line(line) do
    String.rstrip(line) |> String.split(",")
  end

end

[ file_name | _ ] = System.argv

Foo.run(file_name)
```

### module

- Each module lives in its own file.
- You give the module a name.
- In Erlang you need to explicitly export the functions to make them public.
- Non-exported functions become private.
- defp in elixir makes a function private.

### atom

- Notice the capital letters in Erlang
- Notice the ```:``` in Elixir

```
% erlang.

im_an_atom.

But_i_am_a_var.
X=100.

# elixir

:iam_an_atom

iam_a_var
x = 100
```

### Maps

- A bit tough to master if you are coming from Java world.
- Notice the definition
- Notice the update
- Notice the assertion
- Notice how each others' comments are used.
- Notice how ```pattern matching``` is used.
- Notice how atom in exilir can be defined without use of ```:```
- Notice how field access is allowed in Elixir if all keys are atoms.

```
% erlang

Map = #{key => 0}.
Updated = Map#{key := 1}.
#{key := Value} = Updated.
Value =:= 1.
%=> true

# elixir

map = %{:key => 0}
map = %{map | :key => 1}
%{:key => value} = map
value === 1
#=> true

# elixir, below is ok if all keys are atom

map = %{key: 0}
map = %{map | key: 1}
map.key === 1
```

### keyword lists

- Elixir offers a literal syntax for creating a list of **two-item** tuples
  - where the **first item** in the **tuple** is an **atom**
  - known as **keyword lists**:

```
# elixir

kw = [another_key: 20, key: 10]
kw[:another_key]

% erlang

Proplist = [{another_key, 20}, {key, 10}].
proplists:get_value(another_key, Proplist).
```

### Invoking a function from a module

```
% erlang
lists:last([1, 2]).       

# elixir
List.last([1, 2])         
```

### Need of ```&``` in elixir

```
# elixir

# use & to define anonymous functions in a **concise way**
Enum.map [1, 2, 3, 4], &(&1 * 2)
#=> [2, 4, 6, 8]

# use & to pass named functions as arguments
Enum.map [1, 2, 3, 4], &Math.square/1

% erlang
% pass named functions as arguments in erlang
% fun Math:square/1
```
