Модификатор доступа `protected` похож на `private`, но он позволяет доступ из субкласса

```kotlin
open class Foo{
	protected fun something(){
		printl("hello")	
	}

}

class Bar:Foo(){
	fun somethingElse(){
		something()
	}
}

fun main(){
	val notFoo = Bar()
	notFoo.somethingElse()//output "hello"
}
```

