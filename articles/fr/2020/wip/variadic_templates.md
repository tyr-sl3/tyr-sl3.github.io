## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) C++ - Amusons-nous avec les variadic templates

---

### Sommaire

---

### Introduction
En C++, il existe la notion de template, qui permet *-notamment-* de créer des fonctions génériques :
```cpp
template<class T>
void foo(T const& value) {
    std::cout << value;
}

// et à l'usage :
foo(42);     // int
foo(3.14);   // double
foo("foo");  // const char*
```
Ici, la fonction `foo` accepte à peu près tous les types de paramètres, à condition que ceux-ci respectent les types supportés par `std::cout`.

---

### Variadic templates, kézako ?
Avec la norme C++11, arrivée en 2011, ont été ajoutés les variadic templates, permettant à une fonction ou classe/structure de prendre un nombre indéfini de paramètres.
Ils viennent remplacer de façon moderne la syntaxe C très lourde à base de `...`, `va_list`, `va_start`, `va_arg` et `va_end`.
Un bon exemple de l'utilisation des variadic template est la classe [`std::tuple`](https://en.cppreference.com/w/cpp/utility/tuple).

Pour utiliser les variadic templates, la syntaxe est la suivante :
```cpp
template<class ...TArgs>
void bar(TArgs&& ...args) {
    // ...
}
```
Voici un exemple concret d'utilisation des variadic templates, basé sur la fonction `foo` créée plus haut.
```cpp
template<class T>
void foo(T const& value) {
    // version ne prenant qu'un paramètre
    std::cout << value;
}

template<class T, class ...TArgs>
void foo(T const& value, TArgs&& ...args) {
    // version récursive
    foo(value);    // on appelle foo<T>
    foo(args...);  // on rappelle foo<T, TArgs...>
}

// et à l'usage :
foo(3, ".", 14);
```

---

### Utilisations variées des variadic templates
```cpp
template<class T, unsigned N, class ...TArgs>
struct matches_any_of {
    constexpr static auto has_next_type = N < sizeof...(TArgs) - 1;
    using conditional_type = typename std::conditional<
        has_next_type,
        matches<T, N+1, TArgs...>,
        std::false_type
    >::type;
    using TArg1 = typename std::tuple_element<N, std::tuple<TArgs...> >::type;
    
public:
    constexpr static auto value = std::is_same<T, TArg1>::value || conditional_type::value;
};

template<class T, class ...Args>
struct is_any_of: matches_any_of<T, 0u, Args...> {};
```