## WeakReference
---
Слабая ссылка защищает объект от ближайшей сборки мусора 

```Java
//создание объекта 
Cat Cat cat = new Cat(); 

//создание слабой ссылки на объект Cat 
WeakReference<Cat> catRef = new WeakReference<Cat>(cat);

//теперь на объект ссылается только слабая ссылка catRef. 
cat = null; 

//теперь на объект ссылается еще и обычная переменная 
cat cat = catRef.get();

//очищаем слабую ссылку 
catRef.clear();

```

