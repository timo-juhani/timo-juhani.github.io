+++
title = 'Immutable Data'
date = 2024-02-25T15:11:08+02:00
draft = false
tags = ["coding"]
+++

Although I'm not a coder by profession, I've been coding quite a bit during the recent months. While doing that I've tried to improve my coding principles.

I stumbled onto functional programming. While the concept is everything but new in 2024 I had not really looked into it if you don't count in pure functions.

So the scenario is like this: In Python it's quite common that you end up using data structures like a list of dictionaries as a defacto weapon of choice. And of course there is nothing wrong with that. In my cases however those have proven to be error-prone from time to time.

Mainly I'm talking about code like this:

```
input = [
      {"name": "Bob", "age": 24},
      {"name": "Alice", "age": 30}
]
```

The main issue with this approach is that the input data and data structure can be modified at will. That's not ideal to say the least and could cause quite annoying issues in the long run.

Using immutable data structures solves this issue effectively. Immutable? In plain Finnish it means that the data can't be altered. In Python you would implement it like this:

```
import collections

Person = collections.namedtuple("Person", ["name", "age"])

input = (
      Person(name="Bob", age=24),
      Person(name="Alica", age=30),
)
```

Person class is a namedtuple locking the keys used for the data. This way there is no way a fat finger typo breaks the code when adding new data. The tuple input locks the data itself. In comparison: If a list would be used instead of a tuple there would be no way we could stop changes to the orignal input data as the program runs.

Moving forward I'll try to actively find opportunities to employ this technique. To me protecting the input data makes a whole lot of sense. 