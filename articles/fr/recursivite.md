## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [FR] Amusons-nous avec la récursivité (F#)

##### Introduction
> En tant que développeur C++, je ne m'étais jusqu'alors jamais vraiment intéressé à des langages fonctionnels. Je me suis récemment (re)mis au F#, qui est un dérivé d'OCaml adapté à la plateforme .NET, tout comme le C#. Il s'agit d'un langage très intéressant, qui diffère de la plupart des langages que l'on a l'habitude de rencontrer. En F#, la récursivité est une chose très utilisée, qui permet notamment, selon moi, de plus facilement comprendre et débugger du code, en plus d'être très élégante.

---

En C++, beaucoup de problématiques peuvent être résolues par des boucles. Itérer sur une liste, effectuer une action `N` fois... Dans cet article, je vais présenter certaines de ces problématiques avec leur résolution via une boucle, ainsi qu'un équivalent en utilisant la récursivité.

##### a. La somme d'une liste
Pour faire la somme d'une liste, une solution simple consiste à itérer sur ladite liste, et d'additionner la valeur de chaque élément à une valeur de base.
```cpp
int sum(std::vector<int> const& vec){
  auto result = 0;
  for(auto it = vec.begin(); it != vec.end(); ++it){
    result += *it;
  }
  return result;
}
```

###### Possible implémentation :
```fs
let rec sum list =
  match list with
  | []    -> 0
  | x::xs -> x + (sum xs)
```
***Note*** - *Lorsque l'on déclare une fonction récursive en F#, il est impératif d'utiliser le mot-clé `rec`.* <br />
***Note*** - *L'expression `x::xs` correspond à la liste décomposée en `x` (son premier élément) et `xs` (le reste de la liste).*

Ou plus simplement :
```fs
let rec sum = function
  | []    -> 0
  | x::xs -> x + (sum xs)
```
***Note*** - *Le compilateur "comprend" qu'un argument est attendu, bien qu'il ne soit pas spécifié explicitement.*

Soit une fonction qui attend un argument (ici une liste), et teste deux cas :
* Si la liste est vide, on renvoie 0 ;
* Sinon, on prend son premier élément (ici `x`) et on l'additionne au résultat de `sum` sur le reste de la liste (ici `xs`). Si `xs` est vide, on tombe alors sur le premier cas : fin de la récursion.

Il s'agit d'un cas très simple.

##### b. Le cas de la fonction puissance  (volontairement simpliste)
```cpp
using uint = unsigned;

// Via une boucle :
int pow(int n, uint p) noexcept{
  if(p == 0){
    return 1;
  }
  auto res = 1;
  for(auto i = 0u; i < p; ++i){
    res *= n;
  }
  return res;
}

// Version récursive :
int pow(int n, uint p) noexcept{
  return (p != 0) ? n * pow(n, p-1) : 1;
}
```
La seconde version est bien plus concise que la première. L'équivalent en F# serait le suivant :
```fs
let rec pow n p =
  match p with
  | 0 -> 1
  | _ -> n * (pow n (p-1))
```

##### c. Recodons `List.map` !
En OCaml / F#, il existe la fonction `List.map`, qui prend en entrée une fonction et une liste, et applique ladite fonction à chaque élément de la liste. Exemple :
```fs
let ls = [1; 2; 3; 4; 5]
let ls2 = List.map (fun x -> x*2) ls
printfn "%A" ls2 // [2; 4; 6; 8; 10]
```

Si l'on voulait la recoder, on pourrait écrire une version proche de :
```fs
let rec map fn ls =
  match ls with
  | []    -> []
  | x::xs -> (fn x) :: (map fn xs)
```