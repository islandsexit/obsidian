## Map - это как словарь "Ключ-значение"

### Неизменяемые словари

**Инициализация словаря**:
```Kotlin
val dict = mapOf("key" to "value", "other-key" to "other-value")
```

**Важно**
Ключи не могут повторяться, а вот значения могут

Две `Map`-ы, содержащие равные пары, равны независимо от порядка пар.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)    
    val anotherMap = mapOf("key2" to 2, "key1" to 1, "key4" to 1, "key3" to 3)

    println("The maps are equal: ${numbersMap == anotherMap}") //Maps равны друг другу
}
```

## Изменяемые словари (Mutable Maps)
`MutableMap` - это `Map` с операциями записи, например, можно добавить новую пару "ключ-значение" или обновить значение, связанное с указанным ключом.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap.put("three", 3)
    numbersMap["one"] = 11

    println(numbersMap) // {one=11, two=2, three=3}
}
```

По умолчанию реализацией `Map` является `LinkedHashMap`) - сохраняет порядок элементов. Альтернативная реализация - `HashMap` - не сохраняет порядок элементов.