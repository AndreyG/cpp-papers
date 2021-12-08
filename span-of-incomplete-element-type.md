# Support `span` of elements of incomplete type

<pre>Document Number: D????R0
Date: 2021-12-08
Author: Andrey Davydov (andrey.a.davydov@gmail.com)
Audience: Library Evolution Incubator (LEWGI)</pre>

## Introduction
This paper proposes allowing `span` to be instantiated with incomplete element type.

## Motivation and Scope
Currently `span` requires element type to be a complete object type [([span.overview])](https://eel.is/c++draft/views.span#span.overview-4).
This requirement contradicts to guideline prefer `span` over the common idiom of a “pointer plus length” pair of parameters.
For instance, C++ Core Guidelines contains
[the following recommendation](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#i23-keep-the-number-of-function-arguments-low):
> **Example** The safety Profiles recommend replacing
> ```cpp
> void f(int* some_ints, int some_ints_length);  // BAD: C style, unsafe
> ```
> with
> ```cpp
> void f(gsl::span<int> some_ints);              // GOOD: safe, bounds-checked
>

Let's consider the following quite natural and innocent code:
```cpp
class Element;

class Storage {
  ...
  span<Element> get_elements();
};

extern void process(span<Element>);

void do_something(Storage& storage) {
  process(storage.get_elements());
}
```
Right now (C++20) it is not valid due to the requirement that `class Element` must be complete, but replacement of `span<Element>` with `pair<Element*, size_t>`
"fixes" the code, because nor declaration of the function `Storage::get_elements()` nor declaration of the function `process` require complete type `Element`.
The goal of this proposal is to make the example above valid as is.

## Impact on the Standard
This proposal is a pure library extension.

## Proposed Wording
Remove 22.7.3.1 p4 [[span.overview](https://eel.is/c++draft/views.span#span.overview-4)].

### TODO
Modify 22.7.3.*: add requirement "ElementType must be a complete object type that is not an abstract class type" to some members of `span`.
