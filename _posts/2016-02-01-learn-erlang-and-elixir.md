---
layout: post
title: Learn Erlang and Elixir
---

# useful libraries

```

- https://github.com/bitwalker/swarm
  - global distributed rgistry
  - a custering utility
```

<br />

# Learn Erlang and Elixir

```elixir

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

<br />

```

- Each module lives in its own file.
- You give the module a name.
- In Erlang you need to explicitly export the functions to make them public.
- Non-exported functions become private.
- defp in elixir makes a function private.
```

<br />

### Atoms vs Variables

```erlang

% erlang

im_an_atom.

But_i_am_a_var.
X=100.
```

```elixir

# elixir

:iam_an_atom

iam_a_var
x = 100
```

<br />

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

<br />

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

<br />

### Invoking a function from a module

```
% erlang
lists:last([1, 2]).       

# elixir
List.last([1, 2])         
```

<br />

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

<br />

### Why Elixir -to- Enums -to- Streams -to- GenStage

```elixir

- better abstractions to work with collections
- manipulate collections in lazy way
- manipulate in a concurrent way
- manipulate in a distributed way
```

<br />

### Enum vs. Streams

```

- whole collection in memory
- versus. lazy traversal of collection
```

<br />

### Streams vs. Stream.async()

```

- lazy evaluation vs. using separate processes to process parts of the pipeline
```

<br />

### Stream.async() vs. supervising

```

- manual implementation leading to error prone implementation
- how to manage back-pressure i.e. ensure a process from receiving too many messages
- GenStage strives to provide this answer
```

<br />

### case for **GenStage**

```

- exchange events between producers & consumers
- a new Elixir behaviour for exchanging events between Elixir processes.
- understands back-pressure
- expect GenStage to replace the use cases for GenEvent
- will provide a composable abstraction for consuming data from third-party systems.
- Act of data dispatch is managed
```

<br />

### **GenStage** stages

```

- stages receive or send data from other stages
- can assume the role of a producer or consumer at once
- can be source if it only produces
- can be a sink if it only consumes
- the request/demand to a producer is tracked
- demand flows upstream & events downstream
- :max_demand & :min_demand are set on the consumers
```

<br />

### **GenStage** events

```

- events are sent by a dispatcher
- events may be in-memory
- events may be in external queue system
```

<br />

### **GenStage** buffer events

```

- Assuming a consumer has crashed
- The producer may want to buffer the events that have arrived from upstream
- If the intake is more that demand than the events may be buffered
- :buffer_size is the option
```

<br />

### **GenStages** notifications vs. broadcast

```

- send notifications to all consumers
- out-of-band information
- is different from broadcast
```

<br />

### GenServer

```

- provides the client-server interaction bits
- has standard set of interface functions
- tracing & error reporting
- fits into an OTP supervision tree
- can be a part of the supervision tree
```

<br />

### Type safe elixir

```elixir

# refer - elixir docs

defmodule LousyCalculator do
  @spec add(number, number) :: {number, String.t}
  def add(x, y), do: {x + y, "You need a calculator to do that?!"}

  @spec multiply(number, number) :: {number, String.t}
  def multiply(x, y), do: {x * y, "Jeez, come on!"}
end

# versus.

defmodule LousyCalculator do
  @typedoc """
  Just a number followed by a string.
  """
  @type number_with_remark :: {number, String.t}

  @spec add(number, number) :: number_with_remark
  def add(x, y), do: {x + y, "You need a calculator to do that?"}

  @spec multiply(number, number) :: number_with_remark
  def multiply(x, y), do: {x * y, "It is like addition on steroids."}
end
```

<br />

```

- a doc for types
- create custom types similar to golang
- reuse types
```

<br />

### Behaviors are same as interfaces in Java

```elixir

defmodule Parser do
  @callback parse(String.t) :: any
  @callback extensions() :: [String.t]
end

defmodule JSONParser do
  @behaviour Parser

  def parse(str), do: # ... parse JSON
  def extensions, do: ["json"]
end

defmodule YAMLParser do
  @behaviour Parser

  def parse(str), do: # ... parse YAML
  def extensions, do: ["yml"]
end
```

<br />

```

- you get compilation errors if you miss any callback directive !
```

<br />

### Elixir - Special Forms

```

- __MODULE__
  - returns the current module name as an atom or nil otherwise
```

<br />

### Erlang term Storage - ETS

```

- in-memory store
- tables in ets are created & owned by individual processes
- when owner process terminates, its tables are destroyed
- ETS is limited to 1400 tables per node
- ETS has no schema
- data must be stored as a tuple whose first element is the key
```

<br />

### Sample Code - overloaded functions, guards, type specs, type checks

```elixir

# refer - https://github.com/bitwalker/swarm/

@spec register(term, mfa | pid) :: {:ok, pid} | {:error, term}
def register(name, {m,f,a})
  when is_atom(m) and is_atom(f) and is_list(a) do
  GenServer.call(__MODULE__, {:register, name, {m,f,a}})
end
def register(name, pid) when is_pid(pid) do
  GenServer.call(__MODULE__, {:register, name, pid})
end
```

<br />

### Supervisors vs. GenServers

```

- Supervisors glues together the GenServers
- Supervises other processes called child processes
- Builds a hierarchical process structure:
  - a nice way to structure fault-tolerant applications
- Spawns processes, typically GenServers
  - manages them
  - restarts them when they crash
```
