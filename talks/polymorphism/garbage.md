# How to Relax an Argument Constraint  

```c++
template<typename T>
concept has_set = 
    std::invocable<decltype(&T::set), 
                      T &, std::string>
    || std::invocable<decltype(&T::set), 
                      T &, std::string_view const &>
    || std::invocable<decltype(&T::set), 
                      T &, char const *> 
    ;

struct Foo {};
static_assert(!has_set<Foo>);

struct Bar {
    void set(std::string_view);
    void set(std::string);
};
static_assert(has_set<Bar>);

struct Baz {
    void set(std::string);
};
static_assert(has_set<Baz>);

struct Bazz {
    void set(char const *);
};
static_assert(has_set<Bazz>);
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
