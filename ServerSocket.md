## ServerSocket  [[Android]]
---
ServerSocket -  это основа разработки сетевых (см [[Cеть]]) приложений 


Среди понятий и терминов, связанных с работой в сети, если одно очень важное – Сокет. Оно обозначает точку, через которую происходит соединение. Проще говоря, сокет соединяет в сети две программы.

![[1080.webp]]

1. Инициализация Серверного сокета
```Kotlin
val serverSocket = ServerSocket(port:Int, max_clients:Int)

```
2. Слушаем сокет
```Kotlin
clientSocket = server.accept(); // accept() будет ждать пока //кто-нибудь не захочет подключиться
```
3. Читаем из сокета поток данных 
```Kotlin 
val input = BufferedReader(InputStreamReader(clientSocket.getInputStream()))
```
4.  Читаем по строчке из сокета
```Kotlin
while(true){
	val read = input.readLine()  
	    val len = read.length?:0  
	    if (len==0) { //При достижении 0 размера перестаем читать и выходим из цикла  
	        break  
	    }

}
```
5.  Пишем ответ в сокет
```Kotlin
val Dataout = arrayOf<DataOutputStream?>(null)

out[0] = DataOutputStream(clientSocket.getOutputStream())  
out[0]!!.writeBytes(OUTPUT_HEADERS + ("<img style='display:block; width:1000px;height:1000px;' id='base64image' src='data:image/png;base64, "+viewFinder?.base64Photo+"'/>").length + OUTPUT_END_OF_HEADERS)  

out[0]!!.writeBytes("<img style='display:block; width:1000px;height:1000px;' id='base64image' src='data:image/png;base64, "+viewFinder?.base64Photo+"\'/>") 

out[0]!!.close()

```

Как мы знаем в андроид для связи  с интернетом нужно все делать в отдельном потоке [[Thread (Поток)]] 








---
### Реализации
#### 1. VigFace
```Kotlin
  
inner class ServerThread() : Runnable {  
  
    override fun run() {  
        var socket: Socket? = null  
        try {  
            serverSocket = ServerSocket(PORT, 2)  
            serverSocket.reuseAddress = true  
            } catch (e: IOException) {  
            e.printStackTrace()  
        }  
        while (!Thread.currentThread().isInterrupted) {  
            try {  
                Log.i(TAG, "run: ff")  
                socket = serverSocket.accept()  
                val commThread = CommunicationThread(socket)  
                Thread(commThread).start()  
            }catch (e:SocketException){  
                Log.e(TAG, "run: ${e.printStackTrace()}", )  
            } catch (e: IOException) {  
                e.printStackTrace()  
            }  
        }  
    }  
  
  
}  
  
inner class CommunicationThread(private val clientSocket: Socket) : Runnable {  
  
    private var input: BufferedReader? = null  
    override fun run() {  
  
        while (!Thread.currentThread().isInterrupted) {  
            try {  
                val read = input?.readLine()  
                    val len = read?.length?:0  
                    if (len==0) {  
                        break  
                    }  
  
  
            } catch (e: IOException) {  
                e.printStackTrace()  
            }  
        }  
        viewFinder?.takePicture()  
        val out = arrayOf<DataOutputStream?>(null)  
        val thread = Thread {  
            while (!Thread.currentThread().isInterrupted){  
                if(!viewFinder?.base64Photo.isNullOrEmpty()&&!clientSocket.isClosed){  
                    try {  
                        out[0] = DataOutputStream(clientSocket.getOutputStream())  
                        out[0]!!.writeBytes(OUTPUT_HEADERS + ("<img style='display:block; width:1000px;height:1000px;' id='base64image' src='data:image/png;base64, "+viewFinder?.base64Photo+"'/>").length + OUTPUT_END_OF_HEADERS)  
                        out[0]!!.writeBytes("<img style='display:block; width:1000px;height:1000px;' id='base64image' src='data:image/png;base64, "+viewFinder?.base64Photo+"\'/>")  
                        out[0]!!.close()  
                        viewFinder?.base64Photo = null  
                        Thread.currentThread().interrupt()  
  
                    }catch (ex:java.net.SocketException){  
                        Thread.currentThread().interrupt()  
                        viewFinder?.base64Photo = null  
                    }  
                    catch (e: Exception) {  
                        viewFinder?.base64Photo = null  
                        Thread.currentThread().interrupt()  
                        e.printStackTrace()  
  
                    }  
                }  
  
            }  
  
        }  
  
  
        thread.start()  
  
    }  
  
    init {  
  
        try {  
            input = BufferedReader(InputStreamReader(clientSocket.getInputStream()))  
        } catch (e: IOException) {  
            e.printStackTrace()  
        }  
    }  
}
```