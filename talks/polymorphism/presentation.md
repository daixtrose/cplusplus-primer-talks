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
<br>
<br>
<br>
<br>


```c++
using namespace std::chrono
auto t_0 = high_resolution_clock::now();
give_talk();
auto t_1 = high_resolution_clock::now();
auto duration = 
    duration_cast<minutes>(t_1 - t_0);
expect(duration.count() < 40, 
       "Talk went longer" 
       "than 40 minutes");
```
]]

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

> https://www.daixtrose.de/talks/crazy-generic-lambdas/crazy-generic-lambddas.html

<img id="url-slides">

]

---

background-image: url(images/2024-03-29_Amsterdam_X-T3_DSCF0382.jpg)
class: impact-large

## Fotos are mine

Published under [.white[CC BY-SA]](https://creativecommons.org/licenses/by-sa/4.0/legalcode) .image-fixed-40[![](images/CC_BY-SA_88x31.png)]

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

background-image: url(images/IMG_0321.JPEG)
class: impact

# Modern Approach

---

# Modern Approach: Concepts

```c++
template <typename T> `concept` has_super_cool_features 
  = `requires`(T t, std::string s) {
    { t.coolFeature() } -> std::`convertible_to`<std::string>;
    { t.set(s) } -> std::`same_as`<void>;
  };
```
instead of 

```c++
class ISuperCoolFeatures {
public:
    virtual std::string coolFeature() const = 0; 
    virtual void set(std::string s) = 0; 
    virtual ~ISuperCoolFeatures() = default; 
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

background-image: url(images/DSC_0197.JPG)
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
    has_cool_feature<T> `&&` has_set<T>;
```

---

# Testing 

- Showing an example with [µt](https://github.com/boost-ext/ut) as testing framework for no good reason. It looks promising and lightweight, and it was written by [Krzysztof Jusiak](https://github.com/krzysztof-jusiak) whom I adore for his outstanding C++ programming skills and his [C++ Tip of The Week](https://github.com/tip-of-the-week/cpp) collection of crazy C++ riddles.
- [µt](https://github.com/boost-ext/ut) does not provide a mocking framework.
- I haven't found a **mocking framework that works with concepts**.

---

# Testing with Mocks

```c++
struct Mock {
    std::list<std::string> collectedSetArguments;
    mutable std::atomic<std::size_t> numberOfCallsToCoolFeature { 0 };
    std::string coolFeature() const {
        `++numberOfCallsToCoolFeature`;
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
            == impl.numberOfCallsToCoolFeature);
        expect("The answer to all questions is 42"s 
            == result);
        // side effect
        expect("42"s == impl.coolFeature()); 
    };
```
---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template <typename T> 
concept has_set = requires(T t, std::string s) {
    { t.set(s) } -> std::same_as<void>;
};
```

The requirement for `s` is a little bit fuzzy here.

---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    { t.set(s) } -> std::same_as<void>;
};

struct C1
{
    void set([[maybe_unused]] std::string const & s) {}
};

static_assert(has_set<C1>);

struct C2
{
    void set([[maybe_unused]] std::string_view const & s) {}
};

static_assert(has_set<C2>);

struct C3
{
    void set([[maybe_unused]] std::string_view const & s) const noexcept {}
};

static_assert(has_set<C3>);


struct C4
{
    void set([[maybe_unused]] int i) const noexcept {}
};

static_assert(has_set<C4>);
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
concept has_set = requires(T t, `/* what do we do here ? */` s) {
    { t.set(s) } -> std::same_as<void>;
};
``` 

---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
template <typename T> 
concept has_set = requires(T t, std::string s) {
    { t.set(s) } -> std::same_as<void>;
};
```

See https://stackoverflow.com/a/79130967/1528210

```c++
template <typename T, `typename S = std::string`>
concept has_set = requires(T t, S s) {
    `requires std::convertible_to<S, std::string_view>;`
    { t.set(s) } -> std::same_as<void>;
};
``` 

---

# How to Enforce an Argument Constraint on a Method Parameter?  

```c++
// Default parameter must fulfill the constraint 
// --------------------------------VVVVVVVVVVV
template <typename T, `typename S = std::string`>
concept has_set = requires(T t, S s) {
    // additional constraint
    `requires std::convertible_to<S, std::string_view>;`
    { t.set(s) } -> std::same_as<void>;
};
``` 

```c++
void use(has_set auto & o) {
     o.set("aaa");
}
```


---

```c++
// See https://stackoverflow.com/a/79130967/1528210
#include <type_traits>
#include <string_view>
#include <string>

template <typename T, typename S = std::string>
concept has_set = requires(T t, S s) {
    requires std::convertible_to<S, std::string_view>;
    { t.set(s) } -> std::same_as<void>;
};

struct HasSetClass
{
    void set([[maybe_unused]] std::string const & s) {}
};

struct HasSetClass2
{
    void set([[maybe_unused]] std::string_view const & s) {}
};

struct HasSetClass3
{
    void set([[maybe_unused]] std::string_view const & s) const noexcept {}
};


void use(has_set auto & o)
{
     o.set("aaa");
}

int main() {
    HasSetClass2 a2;
    use(a2);

    HasSetClass3 a3;
    use(a3);

}
```

---

```c++
#include <concepts>
#include <string_view>


template<typename T>
concept has_set = requires {
    // Use the static cast to support classes that 
    // have multiple set overloads
    { static_cast<void(T::*)(std::string_view)>(&T::set) };
};
```



---

```c++
#include <tuple>
#include <string>

template<typename>
struct function_traits;

template <typename Return, typename... Args>
struct function_traits<Return (*)(Args...)>
{
   using return_type = Return;
   using arg_types = std::tuple<Args...>;

   static constexpr std::size_t arity = sizeof...(Args);
   template <std::size_t i> using arg_type = Args...[i];
};

template <typename Class, typename Return, typename... Args>
struct function_traits<Return (Class::*)(Args...)>
   : function_traits<Return(*)(Args...)>
{
    using class_type = Class;
};

// test
std::size_t func(int, const std::string &s) { return s.size(); }
struct Test {
    std::size_t func(int, const std::string &s) { return s.size(); }
};

int main() {
  // using traits = function_traits<decltype(func)>;
  using traits = function_traits<decltype(&Test::func)>;

  static_assert(traits::arity == 2);
  static_assert(std::is_same_v<traits::return_type, std::size_t>);
  static_assert(std::is_same_v<traits::arg_type<0>, int>);
  static_assert(std::is_same_v<traits::arg_type<1>, const std::string &>);
  static_assert(std::is_same_v<traits::arg_types, std::tuple<int, const std::string &>>);

  return 0;
}


// template<auto mf, typename T>
// auto make_proxy(T && obj)
// {
//     return [&obj] (auto &&... args) {
//         return (std::forward<T>(obj).*mf)(std::forward<decltype(args)>(args)...);
//     };
// }
```

---

```c++
#include <type_traits>
#include <string_view>
#include <string>
#include <print>

struct NeverUseThis {
    template <typename T>
    [[ noreturn ]] operator T() {
        static_assert("never reach me!");
        throw;
    }     
};

template <typename T>
concept has_set = 
    std::is_invocable_v<decltype(&T::set), T &, std::string>
    || std::is_invocable_v<decltype(&T::set), T &, std::string_view>
    || std::is_invocable_v<decltype(&T::set), T &, char const *> 
    ;

    // std::is_invocable<, decltype(a), int>

    // std::convertible_to<S, std::string_view> 
    // &&
    // requires(T t, S s) {
    //     { t.set(s) } -> std::same_as<void>;      
    // };

//static_cast<void(T::*)(std::string_view)>(&T::set); 

struct Foo {};
static_assert(!has_set<Foo>);

struct Bar {
    void set(std::string_view);
};
static_assert(has_set<Bar>);

struct Baz {
    void set(std::string);

};
static_assert(has_set<Baz>);

struct FooBarBaz {
    void set(std::string_view) { 
        std::print("FooBarBaz\n");
    };
    void set(int) { };
};
static_assert(has_set<FooBarBaz>);

struct HasSetClass
{
    void set([[maybe_unused]] std::string const & s) {
        std::print("HasSetClass\n");
    }
};

struct HasSetClass2
{
    void set([[maybe_unused]] std::string_view const & s) {
        std::print("HasSetClass2\n");
    }
};


void use(has_set auto & o)
{
     o.set("aaa");
}

int main() {
    FooBarBaz fbb;
    use(fbb);

    HasSetClass a1;
    use(a1);

    HasSetClass2 a2;
    use(a2);
}
```

---

```c++
struct NeverUseThis {
    template <typename T>
    [[ noreturn ]] operator T() {
        static_assert("never reach me!");
        throw;
    }
};
```

Dabei reicht

```c++
struct NeverUseThis {
    template <typename T> operator T();
};
```

---

```c++
#include <string>
#include <string_view>

template<typename T>
concept has_set = requires(T t) {
    { t.set(
        [](){ struct {
            operator std::string_view();
            operator std::string();
            operator char *();

        } s; return s; }()
      ) } -> std::same_as<void>;
};

struct Foo {};
static_assert(!has_set<Foo>);

struct Bar {
    void set(std::string_view);
};
static_assert(has_set<Bar>);

struct Baz {
    void set(std::string);
    void set(int i);
};
static_assert(has_set<Baz>);

struct Bazz {
    void set(char *);
};
static_assert(has_set<Bazz>);


int main()
{
}
```

---

```c++
template<typename T>
concept has_set = requires(T t) {
    { t.set(
        [](){ struct {
            operator std::string_view();
            
        } s; return s; }()
      ) } -> std::same_as<void>; }
      ||
      requires(T t) {
    { t.set(
        [](){ struct {
            operator std::string();
        } s; return s; }()
      ) } -> std::same_as<void>; }
       ||
      requires(T t) {
    { t.set(
        [](){ struct {
            operator char *();
        } s; return s; }()
      ) } -> std::same_as<void>; }   
      ;
```
---

```c++
template< class F, class... Args >

concept invocable =
    requires(F&& f, Args&&... args) {
        std::invoke(std::forward<F>(f), std::forward<Args>(args)...);
            /* not required to be equality-preserving */
    };
```

---

```c++
#include <string>
#include <string_view>
#include <concepts>

template<typename T, typename Return, typename Arg>
concept has_set_memfn = requires(T t) { { t.set(Arg{}) }->std::same_as<Return>; };

template<typename T>
concept has_set = has_set_memfn<T, void, std::string_view>;

struct Foo {};
static_assert(!has_set<Foo>);

struct Bar {
    void set(std::string_view){}
};
static_assert(has_set<Bar>);

int main() {
    Bar bar;

    bar.set([](){ struct {
            operator std::string_view() const { return ""; }
        } s; return s; }());
}
```

---

We then declare a **template** function that takes arguments whose type adheres to the **constraints** defined by the **concept**:

```c++
std::string consume(has_super_cool_features `auto`& s);
```
TODO

```c++
template <has_super_cool_features T> std::string consume(T & s);
```

---

template: roentgen
class: middle

# Thank You for your attention!
