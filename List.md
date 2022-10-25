### List

`List<T>` хранит элементы в определённом порядке и обеспечивает к ним доступ по индексу. Индексы начинаются с нуля (0 - индекс первого элемента) и идут до `lastIndex`, который равен `(list.size - 1)`.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    
    println("Number of elements: ${numbers.size}") // 4
    println("Third element: ${numbers.get(2)}") // three
    println("Fourth element: ${numbers[3]}") // four
    println("Index of element \"two\" ${numbers.indexOf("two")}") // 1
}
```



Элементы списка (в том числе `null`) могут дублироваться: список может содержать любое количество одинаковых объектов. Два списка считаются равными, если они имеют одинаковый размер и их элементы в одних и тех позициях **структурно равны*

```kotlin
data class Person(var name: String, var age: Int)

fun main() {
    val bob = Person("Bob", 31)
    val people = listOf(Person("Adam", 20), bob, bob)
    val people2 = listOf(Person("Adam", 20), Person("Bob", 31), bob)
    println(people == people2) // true
    bob.age = 32
    println(people == people2) // false
}
```




## MutableList

`MutableList<T>` - это `List` с операциями записи, специфичными для списка, например, для добавления или удаления элемента в определённой позиции.

```kotlin
fun main() {
    val numbers = mutableListOf(1, 2, 3, 4)
    numbers.add(5)
    numbers.removeAt(1)
    numbers[0] = 0
    numbers.shuffle()
    println(numbers) // [4, 0, 3, 5]
}
```

Как видите, в некоторых аспектах списки очень похожи на массивы. Однако есть одно важное отличие: размер массива определяется при инициализации и никогда не изменяется; в свою очередь список не имеет предопределённого размера; размер списка может быть изменён в результате операций записи: добавления, обновления или удаления элементов.

По умолчанию в Kotlin реализацией `List` является `ArrayList`, который можно рассматривать как массив с изменяемым размером.