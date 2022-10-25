## forEach() - Это функция прохода коллекции и применения лямбда выражения к каждому из элементов

```Kotlin 
val collection = listOf("1", "2", "3")

collection.forEach{ elementInCollection -> print(elementInCollection) }
```

```Kotlin
inline fun <T> Iterable<T>.forEach(action: (T) -> Unit)
```

Из-за того, что функция является единственным параметром, скобки могут быть опущены и за фигурными скобками может быть написана лямбда