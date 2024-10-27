layout: true
title: Polymorphism
class: animation-fade
highlightLanguage:cpp

---

class: middle
background-color: gray
background-image: url(images/IMG_4495.JPEG)
background-size: cover
template: inverse

<div class="footer"><div class="flexcontainer" class="primary"><span>.body[[https://daixtrose.de](https://daixtrose.de)]</span><span>.center[C++ User Group Aachen 2024]</span><span>.body[&copy; 2024 Markus Werle ([github](http://github.com/daixtrose) [linkedin](https://www.linkedin.com/in/markus-werle-04305a12a/))]</span></div></div>


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

# .white[Polymorphism in C++]

`expect(std::chrono::duration == 40min) // #NoEstimates`

---

background-image: url(images/DSC_0479.jpg)
class: impact-large

## What to Expect

<br>
<br>
<br>

.col-4[

## Classic Approach

abstract base class

]
.col-4[
  <br>
]
.col-4[  

## Modern Approach

concepts

]


---

background-image: url(images/IMG_9638.JPEG)
class: impact

# Classic Approach

---

# Classic Approach

Define the interface via an **abstract base class**

```c++
class ISuperCoolFeatures {
public:
    // Pure virtual member functions
    virtual std::string coolFeature() const = 0; 
    virtual void set(std::string s) = 0; 
    // Virtual destructor
    virtual ~ISuperCoolFeatures() = default; 
};
```

???

In classic C++, interfaces are defined via [abstract base classes](https://en.cppreference.com/w/cpp/language/abstract_class) from which the implementation (publicly) inherits. In this example, we have defined such an abstract class:

Instead of defining the member function body (which is possible in a regular class), the member functions of an abstract interface class are declared "pure virtual" by adding `= 0` to their declaration.

Any so-called **implementation** of the interface publicly inherits from this abstract base class and then declares and defines member functions that match the declarations of the pure virtual functions in the abstract interface class:


---

```c++
class ISuperCoolFeatures {
public:
    virtual std::string coolFeature() const = 0; 
    virtual void set(std::string s) = 0; 
    virtual ~ISuperCoolFeatures() = default; 
};
```

```c++
class Impl : public ISuperCoolFeatures {

// ... private members and member functions omitted ...

public:
    std::string coolFeature() const `override` { /* ... */ }
    void set(std::string s) `override` { /* ... */ }
};
```

---

```c++
class Impl
    : public ISuperCoolFeatures {
private:
    // Default member initializer (C++11)
    std::string s_ { "<default value>" }; 

public:
    std::string coolFeature() const `noexcept` override { 
        return s_; 
    }
    void set(std::string s) `noexcept` override {
        s_ = std::move(s);
    }
};
```

---

# Use of Interfaces in Function Calls 

A variable (aka instantiation) of type `Impl` can be passed as argument to any function that expects an argument of its interface type `ISuperCoolFeatures`.

In our example, we define a function which has one argument of type `ISuperCoolFeatures`, and returns a `std::string`:

```c++
std::string consume(`ISuperCoolFeatures`& f);
```

We then pass an argument of type `Impl` to it, e.g. like this:

```c++
 // Using a concrete implementation
 `Impl` i;
 consume(i);
```

---

```c++
namespace classic {

std::string consume(ISuperCoolFeatures& f)
{
    auto answer = "42";
    f.set(answer);
    return "The answer to all questions is " + f.coolFeature();
}

} // namespace classic
```

```c++
classic::Impl i;
std::cerr << classic::consume(i) << '\n';
```

---

background-image: url(images/IMG_0321.JPEG)
class: impact

# Modern Approach

---

# Modern Approach

```c++
template <typename T> `concept` has_super_cool_features 
  = `requires`(T t, std::string s) {
    { t.coolFeature() } -> std::`convertible_to`<std::string>;
    { t.set(s) } -> std::`same_as`<void>;
  };
```

We then declare a **template** function that takes arguments whose type adheres to the **constraints** defined by the **concept**:

```c++
std::string consume(has_super_cool_features `auto`& s);
```


???

In modern C++, interfaces can also be defined via [concepts](https://en.cppreference.com/w/cpp/language/constraints), which can be used to declare what features a concrete class must have. It is slightly more powerful in what can be described.

It also has a great advantage that it is non-intrusive, i.e. it does not require a concrete class to inherit from a certain interface class. This is of advantage when you are not owner of the code of a class that you want to use and cannot change its declaration and definition.

This means that objects of completely unrelated classes can be passed as arguments to a member function (aka method) or a free function, if they all have certain member functions defined.

From a code structuring point of view, this means that the cohesion between different parts of the code is reduced.

In this example, we have defined such an interface specification as a concept


---

# Modern Implementation

```c++
namespace modern {

class Impl /* : no inheritance here! */ {
private:
    std::string s_ { "<default value>" }; 

public:
    std::string coolFeature() const noexcept { 
      return s_; 
    }
    void set(std::string s) noexcept { s_ = std::move(s); }
};

} // namespace modern
```

---

# Modern Implementation

```c++
namespace modern {

class Impl /* : no inheritance here! */ {
    // ...
};

// Check if the class adheres to the concept 
// (i.e. has the interface we want it to have).
static_assert(`has_super_cool_features<Impl>`,
    "Impl class does not meet the requirements of "
    "the has_super_cool_features concept");

} // namespace modern
```

---

# Use of Concepts in Function Calls

We declare a **template** function that takes arguments whose type adheres to the **constraints** defined by the **concept**:


```c++
std::string consume(has_super_cool_features auto& s);
```

and can use it in the very same way like in the classic case:

```c++
Impl i;
consume(i);
```

---

# Modern `consume` 

Content of `consume_class_that_adheres_to_concept.`.highlight-bg[`hpp`]:

```c++
#include <polymorphism/has_super_cool_features.hpp>
#include <string>

namespace modern {

std::string consume(has_super_cool_features auto& s);

} // namespace modern
```

The **implementation** of this **template** function is .highlight-bg[not visible] at the point where `consume` is used (linker error). 

> How can we avoid to expose the implementation?  

---

# Modern `consume` 

Content of `consume_class_that_adheres_to_concept.`.highlight-bg[`ipp`]:

```c++
#include <polymorphism/consume_class_that_adheres_to_concept.hpp>
#include <string>

namespace modern {

std::string consume(has_super_cool_features auto& f)
{
    f.set("42"); // side effect == BAD
    return "The answer to all questions is " + f.coolFeature();
}

} // namespace modern
```

---

# Modern `consume` 

Content of `consume_class_that_adheres_to_concept.`.highlight-bg[`cpp`]:

```c++
#include <consume_class_that_adheres_to_concept.`ipp`>
#include <polymorphism/`impl_without_interface.hpp`>

namespace modern {

// Explicit template instantiation `for Impl`
template std::string consume<Impl>(Impl&);

}
```

---

background-image: url(images/IMG_0323.JPEG)
class: impact

# Pros<br>&<br>Cons

---

# Pros & Cons

.col-6[
## Classic

- Uses an **abstract** (interface) class.
  - The `vtable` *might* have an impact on performance
  - Very strict adherence to interface definition
  - CV qualifiers must match exactly   
]
.col-6[
## Modern

- Requires (sic!) a **concept**
  - You use the **latest compiler** anyway, do you?
  - Concepts allow sloppier constraints via `std::convertible_to`
  - Qualifiers can be disregarded 
]

```c++
template <typename T>
concept has_super_cool_features = requires(T t, std::string s) {
    { t.coolFeature() } 
      -> std::convertible_to<`std::string_view`>;
    { t.set(s) } -> std::same_as<void>; };
```


---

# Pros & Cons

.col-6[
## Classic

- Every type that is passed as a parameter **must inherit** from the **interface**.  
  - Tight coupling<br>(via interface)
  - The interface ***definition*** must be visible at **point of *class definition***.   
  - Later use cases (like introducing tests) yield backpressure to the class design.
]
.col-6[
## Modern

- Every type that is passed as a parameter **must adhere** to the **concept**.
  - Zero *explicit* coupling<br>(yet semantics must fit!)
  - The interface ***constraint*** must be visible at the **point of *usage***.      
  - Later use cases can be easily introduced without effect on the class design.
]

---

# Pros & Cons

.col-6[
## Classic

- Splitting interfaces into subsets is possible via multiple inheritance. 
]
.col-6[
## Modern

- Splitting interfaces into subsets is possible via separate concepts.
  - `template <typename T> concept c = c1<T> && c2<T>;`  
]

---

# Splitting Interfaces (Classic)

```c++
class IHasSet {
public:
    virtual void set(std::string s) = 0; 
    virtual ~IHasSet() = default; 
};

class IHasCoolFeature {
public:
    virtual std::string coolFeature() const = 0; 
    virtual ~IHasCoolFeature() = default;
};

class ISuperCoolFeatures : public IHasSet, IHasCoolFeature {};
```

---

# Splitting Interfaces (Modern)

```c++
template <typename T> 
concept has_set = requires(T t, std::string s) {
    { t.set(s) } -> std::same_as<void>;
};

template <typename T> 
concept has_cool_feature = requires(T t, std::string s) {
    { t.coolFeature() } -> std::convertible_to<std::string_view>;
};

template <typename T> 
concept has_super_cool_features = 
    // order matters for short circuit!
    has_cool_feature<T> && has_set<T>;
```

---


# Enthusiasm First

> .primary[#### Let us imagine we are in a startup which will create a pacemaker.]

.col-8[

## Yes, we can!

- We will use the greatest and latest version of **Modern C++**.
- Where do we begin?
- What is our next step?
- Electronics or software first?
- ...

## Where are the *requirements*?
  
]
.col-4[

![](images/Bob_the_builder.jpg)

]

---

# The basics

## Getting started

Use [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) to write your slides. Don't be afraid, it's really easy!

--

## Making points

Look how you can make *some* points:
--

- Create slides with your **favorite text editor**
--

- Focus on your **content**, not the tool
--

- You can finally be **productive**!

---

# There's more

## Syntax highlighting

You can also add `code` to your slides:
```html
<div class="impact">Some HTML code</div>
```

## CSS classes

You can use .alt[shortcut] syntax to apply .big[some style!]

...or just <span class="alt">HTML</span> if you prefer.

---

# And more...

## 12-column grid layout

Use to the included **grid layout** classes to split content easily:
.col-6[
  ### Left column

  - I'm on the left
  - It's neat!
]
.col-6[
  ### Right column

  - I'm on the right
  - I love it!
]

## Learn the tricks

See the [wiki](https://github.com/gnab/remark/wiki) to learn more of what you can do with .alt[Remark.js]

---

# And more...

## 12-column grid layout

Use to the included **grid layout** classes to split content easily:
.col-8[
  ### Left column

  - I'm on the left
  - It's neat!
]
.col-4[
  ### Right column

  - I'm on the right
  - I love it!
]


---

# Some C++ Code

```c++
import std;

template<std::size_t SIZE>
class Foo {};

template <`template<auto>` class T, auto K>
auto extractSize(const T<K>&) {
*    return K;
}

int main() {
    // The new way of life
    std::println("Hello World"); 
}
```

---

template: roentgen
class: middle

# Thank You for your attention!

---

