[[Функции в котлин]]

## Vararg

Котлин поддерживает объявление функции с переменным колличеством параметров 

Достаточно поставить перед названием параметра модификатор vararg:
``` Kotlin 
fun format(format: String, vararg args: Any)
```

В Java `vararg` должен быть в конце всех параметров функции.
В Kotlin vararg может быть и в середине 

## Spread

Это spread — оператор, который распаковывает массив в список значений из этого массива. Он нужен при передаче массива в качестве параметра vararg.

При вызове функции `format` , определенной следующим образом:

```Kotlin
val params = arrayOf("param1", "param2")  
format(output, params)
```

происходит ошибка компиляции `Type mismatch: inferred type is Array<String> but String was expected`.

Для того, чтобы ее исправить, нужно использовать оператор spread для распаковки массива параметров в соответствующие значения: 

```Kotlin
val params = arrayOf("param1", "param2")   
format(output, *params)
```


#### Объединение нескольких spread

При использовании оператора spread можно передать дополнительные значения в параметр vararg или даже объединить несколько массивов spread в одном параметре:

```Kotlin
val params = arrayOf("param1", "param2")  
val opts = arrayOf("opts1", "opts2", "opts3")  
format(output, *params, "additional", *opts)
```
