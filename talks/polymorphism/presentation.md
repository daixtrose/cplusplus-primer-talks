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

.col-6[
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
<br>

## .white[The Concept of<br>Polymorphism in C++]
]
.col-6[.small[


<br>
<br>

> https://godbolt.org/z/MKecPK3fz

```c++
using namespace boost::ut;
using namespace std::chrono;
auto t_0 = high_resolution_clock::now();
give_talk();
auto t_1 = high_resolution_clock::now();
auto duration = 
    duration_cast<minutes>(t_1 - t_0);
constexpr auto max_nminutes = 
    minutes{90};
expect(duration < max_nminutes) 
    << std::format(
    "Talk went longer than {} minutes",
    max_nminutes);
```
]]

???

https://godbolt.org/z/MKecPK3fz

---

# This Talk is a Test 

> I aim at leveraging the user experience by specific measures 

## Supporting people who are visually impaired

- I use the [Intel One Mono Typeface](https://github.com/intel/intel-one-mono/) for **readability**
  - Example: `int main() {}`  
- I use a **rather large font** which limits the number of lines of code per slide 
- I provide an URL to the **slides** in advance (see QR code on next slide)

## Supporting people who are smarter than I am 

- I provide an URL to the **slides** in advance (see QR code on next slide)
- I provide the **source code** in advance (see QR code on next slide)

Please give me some feedback after the talk about what was helpful for you. 

---

background-image: url(images/2024-03-30_Amsterdam_X-T3_DSCF0516.jpg)

.col-5[

## Code

> https://github.com/daixtrose/cplusplus-primer/tree/main/polymorphism

<img id="url-repo">

]
.col-2[ 
    ![]()
]
.col-5[

## Slides

> https://www.daixtrose.de/talks/cplusplus-primer/talks/polymorphism/presentation.html



<img id="url-slides">

]

---

background-image: url(images/2024-03-29_Amsterdam_X-T3_DSCF0382.jpg)
class: impact-large

## Fotos are mine

Published under [.white[CC BY-SA]](https://creativecommons.org/licenses/by-sa/4.0/legalcode) .image-fixed-40[![](images/CC_BY-SA_88x31.png)]


---

background-image: url(images/DSC_0608_DxO.jpg)
background-size: cover

# It's Very Easy To Give a Talk ...
# ... about Concepts 

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

> Greetings from [David Dunning and Justin Kruger](https://en.wikipedia.org/wiki/Dunning%E2%80%93Kruger_effect): You can give a talk, too!

---

background-image: url(images/IMG_0212.JPEG)
background-size: cover

# Thanks 

** This talk would not have happened without the support<br>of these people:**

.col-6[

### .white[C++ UG Aachen Team]

.white[
- Sven
- Daniel
- Detlef
- Kilian
- Martin
- Michael
- Tobias
]

]
.col-6[

### .white[Stackoverflow Users] 

.white[

- [.white[wohlstad]](https://stackoverflow.com/users/18519921/wohlstad)
- [.white[Wutz]](https://stackoverflow.com/users/1480324/wutz)
- [.white[NathanOliver]](https://stackoverflow.com/users/4342498/nathanoliver)
- [.white[Kirisame Igna]](https://stackoverflow.com/users/22401258/kirisame-igna)

]

### .white[Friends] 

.white[

- .white[Barbara]
- .white[Ansel]

]
]
---

background-image: url(images/IMG_0323.JPEG)

# .white[Ready for Takeoff?]



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
    virtual ~ISuperCoolFeatures() /* noexcept */ = default; 
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
// Liskov substitution principle: 
// public inheritance models an 'is-a' relationship 
class Impl : `public` ISuperCoolFeatures {
// ... private members and member functions omitted ...
public:
    std::string coolFeature() const `override` { /* ... */ }
    void set(std::string s) `override` { /* ... */ }
    // No need to overwrite defaulted base class destructor! 
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

In our example, we define a function which has one argument of type `ISuperCoolFeatures &` (a reference), and returns a `std::string`:

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
    f.`set`(answer); // Slideware! Never do side effects in prod! 
    return "The answer to all questions is " + f.`coolFeature`();
}

} // namespace classic
```

```c++
classic::Impl i;
std::cerr << classic::consume(i) << '\n';
```


---

background-image: url(images/DSC_0511_DxO.jpg)

---

background-image: url(images/DSC_0252.JPG)
class: impact

# Concepts

## An Incomplete Introduction

<a href="https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0734r0.pdf">p0734r0.pdf</a>

---

# Concepts

- This feature was one of the earlier features that Bjarne wanted to have in the language (see https://isocpp.org/blog/2016/02/a-bit-of-background-for-concepts-and-cpp17-bjarne-stroustrup)
  >  In about **1987**, I tried to design templates with proper interfaces. I failed. I wanted three properties for templates: 
  > - full generality/expressiveness, 
  > - zero overhead compared to hand coding, and 
  > - **good interfaces**. 
  >
  > I got Turing completeness, better than hand-coding performance, and lousy interfaces. **The lack of well-specified interfaces led to the spectacularly bad error messages we saw over the years.** The other two properties made templates a run-away success.
  > The solution to the interface specification problem was named “concepts” by Alex Stepanov. 

---

# Concepts

A concept is a **named set of requirements**. The definition of a concept has the form

```
template <template-parameter-list>
concept concept-name = constraint-expression;
```


```c++
template<typename T>
concept R = requires (T i) {
    typename T::type; // T must have a member named type
    // special syntax to describe the return value 
    // of the expression in {}
    {*i} -> std::same_as<const typename T::type &>;
    // p0734r0.pdf mentions {*i} -> const typename T::type &;
    // but this gets rejected by all 3 major compilers!
};
```

??? 

Implicit **SFINAE**: If the expressions in the so-called requires expression are valid, then the template parameter `T` adheres to the constraint:


---

# Concepts

```c++
 // A concept is a named requirement  
 template<typename T>
 concept C = `requires (T x) { x + x; }`;
 //          |- requires `expression` -|

 template<typename T> `requires C<T>` // <-- requires `clause`
 T add(T a, T b) { return a + b; }
```

Putting it together in one for a `requires requires`:

```c++
 template<typename T>
 `requires requires` (T x) { x + x; }
 T add(T a, T b) { return a + b; }
```

---

# Concepts

```c++
template<typename T> concept C = 
    // `something that evaluates to true` OR a requires expression 
```

```c++
template<typename T> concept C = sizeof(T) > 2;

template<typename T> `requires C<T>` // requires `clause`
 T foo(T a, T b) { /* whatever */ }
```

or

```c++
template<typename T>
requires `(` sizeof(T) > 2 `)` // requires clause
T foo(T a, T b) { /* whatever */ }
``` 

---

# Concepts

```c++
template<typename T> concept C = 
    /* `sth that evaluates to true` OR a requires expression */;
```

Four different ways to say the same thing

```c++
template<typename T> `requires C<T>` void f2(T t); // west requires
template<typename T> void f3(T t) `requires C<T>`; // east requires
template<`C` T> void f1(T t);
void f1(`C auto` t) {
    using T = `decltype`(t);
} 
``` 

---

# Concepts

## Omitted in this talk

- I do present information on how to apply concepts to **classes**. 
- Nicolai Josuttis has a good talk about that (presented at Meeting C++ 2024). 

---

From Nicolai Josuttis' comprehensive talk at Meeting C++ 2024: 
- `if constexpr` and concepts play well together (aka the death of SFINAE)

```c++
void add(auto & coll, auto const & val) {
    if constexpr (requires { `coll.push_back(val);` }) {
        `coll.push_back(val)`; // duplication of statement  
    } else {
        coll.insert(val); // hope for an insert dies last
    }
}

std::vector<int> coll_1;
std::set<int> coll_2;

add(coll_1, 42); // calls push_back()
add(coll_2, 42); // calls insert()
``` 

---

```c++
template <typename T, typename Val> concept `HasPushBack` = 
    requires (T t, Val v) { 
        { t.push_back(v) } `-> std::same_as<void>`; };

void add(auto& coll, auto const& val) {
    if constexpr (`HasPushBack<decltype(coll),` `decltype(val)>`) {
        coll.push_back(val);  
    } else {
        coll.insert(val);  
    }
} // https://godbolt.org/z/Yrr6jYGrx

std::vector<int> coll_1;
std::set<int> coll_2;

add(coll_1, 42);  // calls push_back()
add(coll_2, 42);  // calls insert()
``` 

---

```c++
void add(auto & coll, auto const & val) {
    if constexpr (requires { coll.push_back(val); }) {
        coll.push_back(val);  
    } else {
        coll.insert(val); 
    }
}
```

```c++
void add(auto & coll, auto const & val) {
    if constexpr (requires {           
        `{` coll.push_back(val) `} -> std::same_as<void>;` }) {
        coll.push_back(val); 
    } else {
        coll.insert(val); 
    }
}
```

---

# Concepts

The C++ Standard has predefined concepts, see https://en.cppreference.com/w/cpp/named_req. Example:

```c++
template<class From, class To>
concept convertible_to =
    std::is_convertible_v<From, To> `&&`
    requires {
        static_cast<To>(std::declval<From>());
    };
```

The concept `convertible_to<From, To>` specifies that an expression of the same type and value category as those of `std::declval<From>()` can be implicitly and explicitly converted to the type `To`, and the two forms of conversion produce equal results. 

---

# Concepts - More Than One Argument

```c++
// Two arguments!
template<class `From`, class `To`> concept convertible_to;

// - `To` is explicitly set, like in a partial specialization
// - `From` will be assigned from `T`.
// Assignment is from left to right. 
// T is first argument to the concept.
template <std::convertible_to<`std::string`> T>
void foo(T const & x) { // east const forever, Daniel!
    std::string v = x;
}
```

If you want to use `template<C T>`, then C must have one "open" argument. 

---

# Concepts - More Than Two Arguments 

```c++
#include <concepts>

template <typename `A`, typename `B`, typename `C`>
concept something = 
    std::semiregular<A>
    `&&` std::same_as<int, B>
    `&&` std::same_as<double, C>;

// B <- int, C <- double     
template <`something<int, double>` T> void foo (T t) { /* ... */ }

int main() {
    // A <- T <- const char*
    foo("hello");
}
```

---

background-image: url(images/20150728_114910.jpg)


# .white[Concepts - a Summary]

.huge[
- Concepts describe **constraints** on a type.
- Concepts describe the **interface** of a type (in a sloppy way).

- There is more ...] 

.col-12[
    Read [@Foonathan's article](https://www.think-cell.com/en/career/devblog/if-constexpr-requires-requires-requires)    
]



---

background-image: url(images/IMG_0321.JPEG)
class: impact

# Modern Approach

---

Instead of an abstract base class 

```c++
class ISuperCoolFeatures {
public:
    virtual std::string coolFeature() const = 0; 
    virtual void set(std::string s) = 0; 
    virtual ~ISuperCoolFeatures() = default; 
};
```

use a constraint (expressed via a concept)

```c++
template <typename T> `concept` has_super_cool_features 
  = `requires`(T t, std::string s) {
    { t.coolFeature() } -> std::`convertible_to`<std::string>;
    { t.set(s) } -> std::`same_as`<void>;
  };
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

class Impl /* : `no inheritance` here! */ {
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
```

--

```c++
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

# Modern `consume`: Splitting the Code   

```
├── include
│   └── polymorphism
│       ├── `consume_class_that_adheres_to_concept.hpp`
│       ├── consume_class_with_interface.hpp
│       ├── has_super_cool_features.hpp
│       ├── i_super_cool_features.hpp
│       ├── impl_with_interface.hpp
│       └── impl_without_interface.hpp
├── src
    ├── `consume_class_that_adheres_to_concept.cpp`
    ├── `consume_class_that_adheres_to_concept.ipp`
    ├── consume_class_with_interface.cpp
    └── main.cpp
```

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

background-image: url(images/2019-03-16_IMG_4405.JPG)
class: impact

# `break();`<br>(20 minutes)

---

background-image: url(images/DSC_0647.JPG)
class: impact

# Pros & Cons

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

- Requires (pun intended) a **concept**
  - You use the **latest compiler** anyway, do you?
  - Concepts allow sloppier constraints via `std::convertible_to`
  - Qualifiers can be disregarded 
]

```c++
template <typename T>
concept has_super_cool_features = requires(T t, std::string s) {
    { t.coolFeature() } 
      -> std::convertible_to<`std::string_view`>;
    { t.set(`s`) } -> std::same_as<void>; };
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

- An interface is a **closed contract**   
  - "That's all you can get!<br>Nothing more to see here!"
  - When passed as a parameter, you do not have access to other members of the implementation. 
  - Additional member functions of the implementation are **hidden**. 
  - Object-Oriented Programming purists will love this. 
]
.col-6[
## Modern

- A concept is an **open contract** 
  - "This is the *minimal set*,<br>there *might* be more."     
  - When passed as a parameter, you can still see/use other members of the implementation. 
  - Additional member functions of the implementation are still accessible.
  - Object-Oriented Programming purists will wrinkle their nose.  
]

---

# Pros & Cons

.col-6[
## Classic

- "You cannot access public members. 
- A virtual member *function* is required." 
  
]
.col-6[
## Modern

- "Yes, we can."
- 👇 https://godbolt.org/z/bq3TE5q6s

]

.col-12[

```c++
template <typename T>
concept has_member_a = requires(T t) {
    { t.a } -> std::same_as<double &>;
};

struct Bar { double a; };
static_assert(has_member_a<Bar>);
```

]

---

background-image: url(images/DSC_0641.JPG)
background-size: cover

## Splitting Interfaces


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

class ISuperCoolFeatures 
    : public IHasSet, public IHasCoolFeature {};
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
    has_cool_feature<T> `&&` has_set<T>;
```

---

background-image: url(images/DSC_0643.JPG)
background-size: cover

<br>
<br>
<br>
<br>
<br>
<br>

## Testing

---

# Testing 

- Showing an example with [µt](https://github.com/boost-ext/ut) as testing framework for no good reason. It looks promising and lightweight, and it was written by [Krzysztof Jusiak](https://github.com/krzysztof-jusiak) whom I adore for his outstanding C++ programming skills and his [C++ Tip of The Week](https://github.com/tip-of-the-week/cpp) collection of crazy C++ riddles.
- [µt](https://github.com/boost-ext/ut) does not provide a mocking framework.
  - Watch Peter Sommerlad's CppCon 2017 talk "**Mocking Frameworks considered harmful**" on https://www.youtube.com/watch?v=uhuHZXTRfH4 
- I haven't found a **mocking framework that works with concepts**. 
  - This is left as homework for the audience.  

---

# Testing with Mocks

```c++
struct Mock {
    std::list<std::string> collectedSetArguments;
    mutable std::atomic<std::size_t> nCallsToCoolFeature { 0 };
    std::string coolFeature() const {
        `++nCallsToCoolFeature`;
        return collectedSetArguments.empty()
            ? "<default value>"
            : collectedSetArguments.back();
    }
    void set(std::string s)
    {
        collectedSetArguments.`emplace_back(std::move(s))`;
    }
};
```

---

# Testing with Mocks

```c++
"[modern mock]"_test = [] {
    static constexpr auto EXPECTED_COOLFEATURE_CALLS = 2; 
    `mocking::Mock impl`;
    expect("<default value>"s == impl.coolFeature());
        auto result = `modern::consume(impl)`;

        expect(EXPECTED_COOLFEATURE_CALLS 
            == impl.nCallsToCoolFeature);
        expect("The answer to all questions is 42"s 
            == result);
        // side effect
        expect("42"s == impl.coolFeature()); 
    };
```

---

background-image: url(images/20140920_100420.jpg)
background-size: cover

# .white[Let's Play With Our Options]


> 

---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template<typename T>
concept has_set = requires {
    { `static_cast<void(T::*)(std::string const &)>(&T::set)` };
};

struct C1
{
    void set([[maybe_unused]] std::string const & s) {}
};

static_assert(has_set<C1>);
```


---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template<typename T>
concept has_set = requires {
    { static_cast<void(T::*)(std::`string` const &)>(&T::set) };
};

struct C2
{
    void set([[maybe_unused]] std::`string_view` const & s) {}
};

static_assert(`!`has_set<C2>);
```

---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template<typename T>
concept has_set = requires {
    { static_cast<void(T::*)(std::`string` const &)>(&T::set) };
};

struct C2 
{ // `MSVC v19 fails!` https://godbolt.org/z/6PYeGv4bW
    void set([[maybe_unused]] std::`string_view` const & s) {}
    void set([[maybe_unused]] std::`string` const & s) {} 
};

static_assert(has_set<C2>);
```

---


# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T> 
concept has_set = requires(T t, `std::string` s) {
    { t.set(s) } -> std::same_as<void>;
};
```

The requirement for `s` could be a little bit more relaxed. What are our options?

<img width="100%" src=images/2015-07-28_Dartmoor_2.jpg>


???

https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGEjaSuADJ4DJgAcj4ARpjE/qQADqgKhE4MHt6%2B8UkpjgIhYZEsMXFcAXaYDmlCBEzEBBk%2BfmW2mPZ5DDV1BAUR0bHxCrX1jVktQ929RSX%2BAJS2qF7EyOwc5gDMocjeWADUJutuBACeCZgA%2BgTETIQKB9gmGgCCG1s7mPuHQ8ShwOcAbnhMAB3e6PF5mTYMbZePYHNzfX5g57ggiYFgJAxoz5HU6MVgfAAqpF2JzOzDYuyEnwAIrshugQCBEUZkU80NDMAkCLsEEwFOclDyDnTiJgAI5ePBihQQQmkknUhSzfYAdisz12WrVFlJADohRBlWq6QBae70giM5kE878%2BH/VB4dBg9Yal6qmkHd3g75eBy7NxccEmdXg7W7R3O%2BmYAgQEwAVisSZYTGOMXOXgYXiULoTXvzlutLOAuw5Q32ZgAbPSVaGrJ6Q423SHnhNHMg7QolPUIHyBUL4UH7rNva2nn6A24zE33RGo%2BgY3HE8mLKn0xcsznMHmC3SGUySwCgcCywIK%2BYa8b66GvSjmz627UO12e3H%2B4LY0OZ%2BtsKOWyiT7EP6PJuOss7htqC5LvGSYruuGZbrmiZ7kWh5XL8x4gmeDAXtWtY4RWDCoJgqgrNyOq3k2XoAS8T5MC%2B/Jvn2/KfgQQ7gb%2B/6PnRE5XCBgaSBBmpQU6i6Giu8Fpoh2bIfmKG7KEPJ4Cq5Y8sRpHkcKYaNveNE8e2eCdkxsTvqxg6HG4QlcWOgG8UpuypqEEB1mGdm3hw8y0JwCa8H4HBaKQqCcG41jWPSizLB8Gw8KQBCaJ58wANYgAmGh6hoACckgaJI6wABz5aqUiqus6xVvonCSH5CVBZwvAKCAATxQFnmkHAsAwIgKACPwxCppy5CUGgGJ0LEjWwCNCRjcQACSjL/MgCQJACXCZecWKYEM5yqFW0ioNyaSNRwAD0B6mJY1ibRWJ0AFpMLsJ0AOpiLQj0APJmG1EBTTN80oAYRhrS0B0dMdpoMiKF1WJYkK7BDVq0HgUQikjUTQ%2BFpovbQb1Y6RVwPaaZzoIYHbfb99BzYywBSGYQWHeenAIy66w0hjsPrPD2O409%2BPXPDxOk0ZXPEaaWZJcRwIMET1zAKmCgi6gYuyTuRN1ASaLEPD708LM8w7oQJDOnogPAP97M2GjE0uV5Pk1a1dUcB4DB9QNKy7AAaiesS7BAuCG1rMUqusJ1cL5cUJXrpApZIVZ6qqCZVhoCZcKqVZmAmCaqvllUcNVpAsCA1Z6pIqdmNnVZlWVVZpTn/mBcFHANU1EetfMHXdRTsRDT9qCjZTAOGDT%2BUtDQtCa41EBRLVUShHUxycLFs/MMQxzvVE2iVC1sUjWwgjvQwtALw7WBRF4wBuK9x2xVgA3AOIJ/SlveD/FttWkZUXhorVSltLVaPXFXh4LAtUMKF24LwV%2BxAojJEwDSdEQ8kZGEjnwAwwAFBexBO9cki9eD8EECIMQ7ApAyEEIoFQ6gHa6C4PoIeKAwqWH0Mja28xQZHSZpDVmFs4amnepzZmaNUbMKtBbLmr0uZ80JoLQQwssZkkwKaJGLBbgCwIAgMUTB0C8FQFAn4WAWGtHaGkFwDB3CeCaHoYIoQ%2BjFAGDQnIqQBCjGaIkZIjiGBTH6KUQxz8BBdBGOYsYPiqh%2BOGD0ax0w7G2DCc4vQEx6ieNsaUeYChIorAkLbDg4d67aM4LsVQ%2BUqymj2mWU2uwuD5T1FwDKvt/ZEEDpCLgsxeAtS0O3JAiwCAJC/j3LuxBwgEk4AUopJTthD3KXHdKgUDb1ONjQ/BwhRDiBIQs8haharUNIMCa4CRcGZOybVRu70v7dJ5KgKg%2BTCnFMkKU8ZFSqk1IgB4fuPsg4tMjvMBAmBNEDBcrnfOhczCSD1OsbO6wEz5UkJIFOWd8rlVIDkx2zcaGtO%2Bl1PsPUXlkAoL3LFIBFrLVWuta6BAdp7T4HQCelBp4O2XvPXBpA6Wr3XpvBwtVd6MAIAfI%2BtVT7n0vjja%2BvBb6INWDfJ%2BVRX7HWmaoT%2B38IHkEEH/BVAD57ANWIFMBDKoEwKUPAu%2BSDQBt1QUwdBmDgTYMYAyhZhDlnSFWUodZVCi60OQRbJhUQDFsMZqdc6DCLAksevdD6X0gq6OdG/SAKS2i%2BL8BAVwsSaFWMKF4vQDiOiJtcbkNIiSZg0IqCEzoMTAkuILR0fx4SU1JLicWzIpawm5rsSktJxD9n2wbnk3aNyWAKEWpGNaepA1%2B3wPUys6wmnvONRizppzel92mpTAZbAhklJ7X2/4A6SW8BmUbRk8zZC2uIfa2QazKGBV0HTbZTBdkQLbQiw5nBjldK/rsc5%2BTV29uQP2zKg6GJbR5E8%2BdM0x1mGaa3NpmSAVFzMHqBMeVJBmHypCTKSc4UZ3vQ7RuzdmooI7hivpc68U0wQxS8e41qUzznqvBlTK14by3uyvue8uWH2PoFPlF8r4MpFUYB%2B7GJWOCle/WVyAv4at4L/byDtVVAIwOJuKPxwGxR1bA/ViDfgoKoGgjBJ5LX%2BVijapZR7SHyEdWenQIB1iuuMP6j1XqGa4Q4VaKG/qeHcwkaoAmAsdxCy/UTNRGjFy8PWNo8N%2Bj4DRqMc4eNpjM3JpsXmrN7jM3ppzRE1N%2BaY2ForZmst1QG3perfm2tFjiuTEK3m5tSx0lNNzgczDnarklOAMgL9Ug9RmFqSOkgY6J3ge%2Bh0k5PScV9KXasYZ1zdgtba%2BsKp27ut6L0IZohEhj1kLMxsyzWydl7Lq%2B23JHAn2nNfRcibzXWvlOBZ1wDWKx3rDA6iqOUmoPFyrEnTK6xY4j3yplUqCY6aIqw7YPQj3kogEkD%2BzKydMqJy4FwRD/23vSCkyFjDHam79ae5wUNhcygBEB/VTH8woEpGcJIIAA%3D%3D

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S `= std::string`> 
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C1
{
    void set([[maybe_unused]] `std::string` const & s) {}
};

static_assert(has_set<C1>);
```

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C2
{
    void set([[maybe_unused]] `std::string_view` const & s) {}
};

// implicit conversion takes place
static_assert(has_set<C2>);
```

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C3
{
    void set([[maybe_unused]] `std::string_view` const & s) 
        `const noexcept` {}
};

static_assert(has_set<C3>);
```

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C4
{
    void set([[maybe_unused]] int i) const noexcept {}
};

static_assert(`!`has_set<C4>); // TODO: Explain 
```
---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C4
{
    void set([[maybe_unused]] int i) const noexcept {}
};

static_assert(has_set<C4, `int`>); // Aha!
```
---


# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T> 
concept has_set = requires(T t, std::string s) {
    { t.set(s) } -> std::same_as<void>;
};
```

The requirement for `s` could be relaxed. 

```c++
template <typename T> 
concept has_set = requires(T t, `/* `what do we put here?` */` s) {
    { t.set(s) } -> std::same_as<void>;
};
``` 

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
#include <type_traits>
#include <string_view>

template <typename T, typename S> 
concept has_set = `requires`(T t, S s) { 
    `requires` std::`convertible_to`<S, std::string_view>;
    { t.set(s) } -> std::same_as<void>;
}; // https://godbolt.org/z/nPodYz8qG
```

The concept `convertible_to<From, To>` specifies that an expression of the same type and value category as those of `std::declval<From>()` can be implicitly and explicitly converted to the type To, and the two forms of conversion produce equal results.

---

# How to Relax an Argument Constraint

```c++
template <typename T, typename S>
concept has_set = requires(T t, S s) {
    `requires std::convertible_to<S, std::string_view>;`
    { t.set(s) } -> std::same_as<void>;
};

struct C1
{
    void set([[maybe_unused]] `std::string const &` s) {}
};


static_assert(has_set<C1, `std::string const &`>);
```

---

# How to Relax an Argument Constraint

```c++
template <typename T, typename S>
concept has_set = 
    std::convertible_to<S, std::string_view>
    `&&` // <---------
    requires(T t, S s) {
        { t.set(s) } -> std::same_as<void>;
    };

struct C1
{
    void set([[maybe_unused]] std::string const & s) {}
};
// Can we get rid of the `explicit mention of set's argument?` 
static_assert(has_set<C1, std::string const &>);
```

---

# How to Relax an Argument Constraint on a Method Parameter?  

```c++
template <typename T>
concept has_set = requires(T t) {
    { t.set(/* `what do we put here?` */) } -> std::same_as<void>; 
};
```

---

```c++
struct Something {
    operator std::string_view(); // declarations are sufficient!
    operator std::string();
    operator char *();
};

namespace { Something `s`{}; } // do not leak a symbol!

template<typename T>
concept has_set = requires(T t) {
    { t.set(s) } -> std::same_as<void>;
};

struct Bar { void set(std::string_view); };
static_assert(has_set<Bar>);
```

https://godbolt.org/z/YscW7Pjz6

---

```c++
struct Something { // `TODO: This should be anonymous`
    operator std::string_view();
    operator std::string();
    operator char *();
};

template<typename T>
concept has_set = requires(T t) {
    // { t.set(`Something{}`) } -> std::same_as<void>;
    { t.set(`declval<Something>()`) } -> std::same_as<void>;
};

struct Bar { void set(std::string_view); };

static_assert(has_set<Bar>);
```

---

# How to Relax ...

```c++
template <typename T>
concept has_set = requires(T t) {
    { t.set(
        [](){ 
            `struct` { // hidden unnamed struct 
                operator std::string_view();
                operator std::string();
                operator char *(); } `s`; // s not visible outside 
            `return s;` }`()` // invoke body of lambda expression
      ) } -> std::same_as<void>; 
};
```

https://godbolt.org/z/edhjEoGGP

---

```c++
template<typename T> concept has_set = requires(T t) {
    { t.set([](){ struct {
                operator std::string_view();
                operator std::string();
        } s; return s; }()
      ) } -> std::same_as<void>;
};

struct Bar {
    void `set(std::string_view)`;
    void `set(std::string)`;
};
// `ambiguous` - which conversion operator to choose? 
static_assert(`!`has_set<Bar>); 
```

https://godbolt.org/z/zsq5h8e4h

---

# How to accept any set function?  

> Slideware! Does not compile. 

```c++
template <typename T>
concept has_set = requires(T t) {
    { t.set(
        [](){ // set should take `any argument`
            struct { 
                // `Compiler error`: Templates cannot be 
                // declared inside of a local class!
                `template <typename U> operator U();`
            } s;
            return s; }() 
      ) } -> std::same_as<void>;
};
```

---

# How to accept any set function?  

> Back to external struct. Note: .primary[**The ambiguity remains**].

```c++
namespace { namespace internal { 
[[ maybe_unused ]] struct {
    template <typename U> operator U();
} s;
} /* namespace private */ } // namespace

template<typename T>
concept has_set = requires(T t) {
    { t.set(::internal::s) } -> std::same_as<void>;
};
```

https://godbolt.org/z/8z54jj8qW

---

# Using Predefined `std::`Concepts?

```c++
template<typename T>
concept has_set = 
   std::invocable<decltype(&T::set), T &, std::string>
|| std::invocable<decltype(&T::set), T &, 
                  std::string_view const &>
|| std::invocable<decltype(&T::set), T &, char const *>;

struct Bar { void set(std::string_view); };
static_assert(has_set<Bar>);

struct Baz { void set(std::string); };
static_assert(has_set<Baz>);

struct Bazz { void set(char const *); };
static_assert(has_set<Bazz>);
``` 

---

# Using Predefined `std::`Concepts?  

```c++
template<typename T>
concept has_set = 
   std::invocable<decltype(&T::set), T &, std::string>
|| std::invocable<decltype(&T::set), T &, 
                  std::string_view const &>
|| std::invocable<decltype(&T::set), T &, char const *>;

// Reminder: decltype(&T::set) may still be ambiguous  
struct Bar {
    void `set`(std::string_view);
    void `set`(std::string); 
};
static_assert(`!`has_set<Bar>); 
``` 

---

# Splitting Concepts Allows Overloads

```c++
template<typename T>
concept has_set = requires(T t) {
{ t.set([](){ struct { operator `std::string_view`(); } s; 
    return s; }()) } -> std::same_as<void>; }
  `||` // order matters, short circuit kicks in here
                  requires(T t) {
{ t.set([](){ struct { operator `std::string`(); } s; 
    return s; }()) } -> std::same_as<void>; };

struct Baz { void set(std::string); 
             void set(std::string_view); };
static_assert(/* hooray! */ `has_set<Baz>`);
```
https://godbolt.org/z/GMrdjqoE7

---

# Pushing the Limits

## "I want it all and I want it now"

```c++
template<typename T>
concept has_set = requires(T t) {
    { t.set(
        `any_of<std::string_view, std::string, char*>`() 
    ) } -> std::same_as<void>;
};
```

https://godbolt.org/z/ehxhxbscd

---

```c++
template <typename T> struct can_convert_to { 
    using type = T;
    operator T(); 
};

template<class`...` Ts> struct overloaded : `can_convert_to`<Ts>`...` { 
    using can_convert_to<Ts>::operator 
        typename can_convert_to<Ts>::type`...`; 
    };

template<typename... Ts> auto any_of() 
    { return overloaded<Ts...>{}; };

template<typename T> concept has_set = requires(T t) {
    { t.set(
        any_of<std::string_view, std::string, char*>() 
    ) } -> std::same_as<void>;
};
```

---

```c++
template <typename T> struct can_convert_to { operator T(); };

template<class`...` Ts> struct overloaded 
    : `can_convert_to`<Ts>`...` { 
    using can_convert_to<Ts>::operator Ts`...`; };

template<typename... Ts> 
auto any_of() { return overloaded<Ts...>{}; };

template<typename T>
concept has_set = requires(T t) {
    { t.set(
        any_of<std::string_view, std::string, char*>() 
    ) } -> std::same_as<void>;
};
```

https://godbolt.org/z/Gdc3E6veT

---

# Problem With Overloading Remains

```c++
struct Bar {
    void set(std::string_view);
    void set(std::string);
};

static_assert(`!`has_set<Bar>);
```

Let us look at ... 

... **fold expressions** for concepts, proposed by Corentin Jabot in 
[p2963r3.pdf](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p2963r3.pdf).

---

```c++
template <typename T, typename Arg> 
concept has_set_helper = requires(T t, Arg arg) { 
    { t.set(arg) } -> std::same_as<void>; };

template<typename T, typename`...` Args>
concept has_set_with_any_of = (has_set_helper<T, Args> `|| ...`);

template<typename T>
concept has_set = has_set_with_`any_of`<T, 
    `std::string_view, std::string, char const *`>; 

struct Bar {
    void set(std::string_view);
    void set(std::string);
};
static_assert(/* hooray again! */ `has_set<Bar>`);
```

---

# [Kirisame Igna](https://stackoverflow.com/users/22401258/kirisame-igna) Is [Smarter](https://stackoverflow.com/a/79240822/1528210)

```c++
template <typename T, typename... Args>
concept has_set_args = 
    ((requires(T t, Args args) {
        { t.set(args) } -> std::same_as<void>;
        }) || ... || false);

template <typename T> concept has_set = 
    has_set_args<T, std::string_view, std::string, const char *>;

struct Bar {
    void set(std::string_view);
    void set(std::string);
};
static_assert(has_set<Bar>);
```

---

# Homework

Find a way to express 

```c++
template <typename T, typename Arg> 
concept has_set = requires(T t) { 
    { t.set(/* `anything, not a limited list of types` */) } 
    -> std::same_as<void>; };
```

You must not use reflection 😜.

---

# Homework 2

Fix the code of the title page to also describe a suspend and resume with `coroutines`<br>**and** .highlight[give a talk about it!] 

```c++
using namespace boost::ut;
using namespace std::chrono;

auto t_0 = high_resolution_clock::now();
give_talk(); // a pause is missing here.
auto t_1 = high_resolution_clock::now();
auto duration = duration_cast<minutes>(t_1 - t_0);
constexpr auto max_nminutes = minutes{90};
expect(duration < max_nminutes) 
    << std::format("Talk went longer than {} minutes", 
                   max_nminutes);
```

---

background-color: #222222

# Homework 3

## Give Feedback, please.

<img width=100% src=images/2015-07-28_Dartmoor_1.jpg>

---

template: roentgen
class: middle
background-image: url(images/DSC_0229.JPG)

# .center[.white[Thank You for your attention!]]
