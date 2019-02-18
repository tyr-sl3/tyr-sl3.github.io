## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Le RAII, qu'est-ce que c'est ?

---

### Sommaire


---

### Définition

Le RAII (pour **R**esource **A**cquisition **I**s **I**nitialisation) est un principe clé de l'orienté objet. Malgré son nom trompeur, le RAII garantit que losqu'un object acquiert une ressource (pointeur, connexion, ...), ladite ressource sera bel et bien libérée lors de la destruction de l'objet. Il associe la durée de vie d'une ressource à celle de son propriétaire.

Par exemple, en C++, `std::istream` et `std::ostream` sont, au même titre que `std::unique_ptr` et `std::shared_ptr`, des capsules RAII-conform. Les ressources allouées sont libérées à la destruction des instances.

---

### Exemples
Exemple de code non-conforme au RAII car le fichier doit être libéré à la main :
```c
FILE *file = fopen("foo.txt", "r");
if (! file) {
  // gestion de l'erreur
} else {
  // lecture du fichier...
  fclose(file);
}
```
Dans un exemple aussi simple, le fichier sera libéré comme attendu, mais dans un exemple plus complexe *-ou pire, dans un code mêlant C et C++-*, il peut ne **jamais** l'être. Il y a alors une fuite mémoire. Le RAII est le meilleur moyen de se prémunir des fuites mémoire, en garantissant que les capsules ayant l'ownership s'occupent de libérer les ressources.

Exemple de code conforme au RAII :
```py
with open("foo.txt", "r") as f:
    # lecture du fichier
# ici, le fichier est fermé
```
Cet exemple de code est tout à fait RAII-conform, car le fichier est libéré à la sortie du bloc `with`/`as` (à l'appel de `__exit__()`).

Les langages modernes sont à peu près full RAII-conform ; les objets sont généralement supprimés par un garbage collector, et les ressources sous-jacentes le sont également.

---

### Cas d'utilisation

D'une manière générale, il convient de *toujours* passer par des capsules RAII lorsque l'on veut manipuler des ressources qui doivent être allouées et libérées à la main. Usez et abusez d'objets RAII en C++ (`std::string`, `std::vector`, ...) plutôt que d'employer des pointeurs bruts. Toujours en C++, créez un wrapper RAII lorsque vous devez utiliser des pointeurs (comme l'utilisation d'une bibliothèque C) de manière à s'affranchir de multiples vérifications après chaque allocation.