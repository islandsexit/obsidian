[[Android]]
## DownloadManager - это класс для загрузки файлов из интернета 
---
Инициализация объекта загрузчика
``` kotlin
            val downloadManager = context.getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager  
```

Инициализируем ссылку, откуда будем скачивать
```
            val downloadUri = Uri.parse(url)  
            val request = DownloadManager.Request(downloadUri)  
```

Устанавливаем ограничения и декораторы 
```
	        request.setMimeType(MIME_TYPE)  
            request.setTitle("Загрузка")  
            request.setDescription("Загрузка обновления")  
            request.setVisibleInDownloadsUi(true)  
            request.setDestinationUri(uri) 
```

Начинаем загрузку
``` 
	        downloadManager.enqueue(request)
```

Также DownloadManager возвращает id загрузки, который можно отследить см Пример 





### Пример использования
#### 1. VigPark:
```kotlin
class DownloadController(private val context: Context, private val url: String) {  
  
    companion object {  
        private const val FILE_NAME = "VigPark.apk"  
        private const val FILE_BASE_PATH = "file://"  
        private const val MIME_TYPE = "application/vnd.android.package-archive"  
        private const val PROVIDER_PATH = ".provider"  
        private const val APP_INSTALL_PATH = "\"application/vnd.android.package-archive\""  
        private var downloadStarted = false  
    }  
  
    @SuppressLint("Range", "HandlerLeak")  
    fun enqueueDownload() {  
        if(!downloadStarted){  
            downloadStarted = true  
            var destination =  
                context.getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + "/"  
            destination += FILE_NAME  
  
            val uri = Uri.parse("$FILE_BASE_PATH$destination")  
  
            val file = File(destination)  
            if (file.exists()) file.delete()  
  
            val downloadManager = context.getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager  
            val downloadUri = Uri.parse(url)  
            val request = DownloadManager.Request(downloadUri)  
	        request.setMimeType(MIME_TYPE)  
            request.setTitle("Загрузка")  
            request.setDescription("Загрузка обновления VigPark")  
            request.setVisibleInDownloadsUi(true)  
  
            request.setDestinationUri(uri)  
  
            val handler: Handler = object : Handler() {  
  
                override fun handleMessage(msg: Message) {  
                    if (msg.what == 1) {  
                        dialogInstallSuccess(destination, uri)  
                    }  
                    else{  
                        dialogInstallError("Ошибка сети")  
                    }  
                    super.handleMessage(msg)  
                }  
            }  
  
            val downloadId = downloadManager.enqueue(request)  
  
  
            Thread {  
                var downloading = true  
                while (downloading) {  
  
                    val q = DownloadManager.Query()  
                    q.setFilterById(downloadId)  
                    val cursor: Cursor = downloadManager.query(q)  
                    cursor.moveToFirst()  
                    val bytes_downloaded: Int = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR)  
                    )  
                    val bytes_total: Int =  
                        cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_TOTAL_SIZE_BYTES))  
  
                    val status = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_STATUS))  
  
                    when(status){  
                        DownloadManager.STATUS_SUCCESSFUL -> {  
                            val msg = handler.obtainMessage()  
                            msg.what = 1  
                            handler.sendMessage(msg)  
                            showInstallOption(destination, uri)  
                            downloading = false  
  
                        }  
                        DownloadManager.STATUS_FAILED -> {  
                            downloading = false  
                            downloadStarted = false  
                            val msg = handler.obtainMessage()  
                            msg.what = 2  
                            handler.sendMessage(msg)  
}  
  
                        DownloadManager.STATUS_PAUSED -> {  
                            downloading = false  
                            downloadStarted=false  
                            val msg = handler.obtainMessage()  
                            msg.what = 2  
                            handler.sendMessage(msg)  
                                }  
                    }  
  
                    val dl_progress =  
                        if (bytes_total > 0) (bytes_downloaded * 100L / bytes_total).toInt() else 0  
                    Log.e("Downloading", dl_progress.toString())  
                    val summary = cursor.getString(  
                        cursor.getColumnIndexOrThrow(DownloadManager.COLUMN_URI)  
                    )  
                    val mimeType = cursor.getString(  
                        cursor.getColumnIndexOrThrow(DownloadManager.COLUMN_MEDIA_TYPE)  
                    )  
  
  
  
                    statusMessage(cursor)?.let { Log.d("APP_TAG", "$it, $summary, $mimeType, $bytes_total, $bytes_downloaded") }  
                    cursor.close()  
  
                }  
                downloadStarted = false  
            }.start()  
        }  
  
  
    }  
  
    @SuppressLint("Range")  
    private fun statusMessage(c: Cursor): String? {  
        var msg = "???"  
        msg =  
            when (c.getInt(c.getColumnIndex(DownloadManager.COLUMN_STATUS))) {  
                DownloadManager.STATUS_FAILED -> "Download failed!"  
                DownloadManager.STATUS_PAUSED -> "Download paused!"  
                DownloadManager.STATUS_PENDING -> "Download pending!"  
                DownloadManager.STATUS_RUNNING -> "Download in progress!"  
                DownloadManager.STATUS_SUCCESSFUL -> "Download complete!"  
                else -> "Download is nowhere in sight"  
            }  
        return msg  
    }  
  
    private fun showInstallOption(  
        destination: String,  
        uri: Uri  
    ) {  
  
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {  
                        val contentUri = FileProvider.getUriForFile(  
                            context,  
                            BuildConfig.APPLICATION_ID + PROVIDER_PATH,  
                            File(destination)  
                        )  
                        val install = Intent(Intent.ACTION_VIEW)  
                        install.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)  
                        install.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)  
                        install.putExtra(Intent.EXTRA_NOT_UNKNOWN_SOURCE, true)  
                        install.data = contentUri  
                        context.startActivity(install)  
  
  
                    } else {  
                        val install = Intent(Intent.ACTION_VIEW)  
                        install.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP  
                        install.setDataAndType(  
                            uri,  
                            APP_INSTALL_PATH  
                        )  
                        context.startActivity(install)  
  
                        // finish()  
                    }  
  
    }  
  
    private fun dialogInstallError(msg: String){  
        val dialog = AlertDialog.Builder(context)  
        dialog.setTitle("Доступно обновление")  
            .setMessage("Произошла ошибка во время загрузки.\nПодключитесь к сети.\n$msg")  
            .setCancelable(false)  
            .setPositiveButton("Я подключен к сети") { dialog, whichButton ->  
                this.enqueueDownload()  
            }  
        dialog.show()  
    }  
  
    private fun dialogInstallSuccess(destination: String, uri: Uri){  
  
        val dialog = AlertDialog.Builder(context)  
        dialog.setTitle("Доступно обновление")  
            .setMessage("Установите обновление\nНе выключайте устройство\n")  
            .setCancelable(false)  
            .setPositiveButton("Установить") { dialog, whichButton ->  
                dialogInstallSuccess(destination, uri)  
                showInstallOption(destination, uri)  
            }  
        dialog.show()  
    }  
  
  
    }
```



#### 2. VigFace
```Kotlin
  
class DownloadController(private var context: Context, var url:String, var callbackDownloading:CallbackDownloading) {  
  
    companion object{  
         const val FILE_NAME = "app-release.apk"  
         const val FILE_BASE_PATH = "file://"  
         const val MIME_TYPE = "application/vnd.android.package-archive"  
         const val PROVIDER_PATH = ".provider"  
         const val APP_INSTALL_PATH = "\"application/vnd.android.package-archive\""  
         var downloadStarted = false  
  
        const val SUCCESS_DOWNLOADING = 1  
        const val ERROR_DOWNLOADING = 2  
        const val DOWNLOAD_RUNNING = 3  
  
        const val DESTINATION = "destination"  
        const val URI = "uri"  
    }  
  
    interface CallbackDownloading{  
        fun onSuccess(msg:Message)  
        fun onError()  
        fun downloadRunning(progress:Int)  
    }  
  
    private lateinit var downloadManager: DownloadManager  
    private var downloadId: Long = 0  
  
    lateinit var downloadThread: Thread  
  
    private val handler by lazy{ object : Handler(Looper.getMainLooper()) {  
  
            override fun handleMessage(msg: Message) {  
                when(msg.what){  
                    SUCCESS_DOWNLOADING -> callbackDownloading.onSuccess(msg)  
                    ERROR_DOWNLOADING -> callbackDownloading.onError()  
                    DOWNLOAD_RUNNING -> callbackDownloading.downloadRunning(msg.arg1)  
                }  
                super.handleMessage(msg)  
            }  
        }  
    }  
  
  
  
    fun stopDownloading(){  
        Log.i("Downloading", "stopDownloading")  
        downloadStarted = false  
        downloadThread.interrupt()  
        downloadManager.remove(downloadId)  
    }  
  
    @SuppressLint("ServiceCast", "Range")  
    fun enqueueDownload() {  
  
        if(!downloadStarted){  
            downloadStarted = true  
            var destination =  
                context.getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + "/"  
            destination += FILE_NAME  
  
            val uri = Uri.parse("$FILE_BASE_PATH$destination")  
  
            val file = File(destination)  
            if (file.exists()) file.delete()  
  
            downloadManager = context.getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager  
            val downloadUri = Uri.parse(url)  
            val request = DownloadManager.Request(downloadUri)  
            request.setMimeType(MIME_TYPE)  
            request.setTitle("Загрузка")  
            request.setDescription("Загрузка обновления VigFace")  
            request.setDestinationUri(uri)  
  
  
  
            downloadId = downloadManager.enqueue(request)  
  
        downloadThread = Thread {  
            while (downloadStarted) {  
//todo error if stopped  
                val q = DownloadManager.Query()  
                q.setFilterById(downloadId)  
                val cursor: Cursor = downloadManager.query(q)  
                cursor.moveToFirst()  
                val bytes_downloaded: Int = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR)  
                )  
                val bytes_total: Int =  
                    cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_TOTAL_SIZE_BYTES))  
  
                val status = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_STATUS))  
  
                val dl_progress =  
                    if (bytes_total > 0) (bytes_downloaded * 100L / bytes_total).toInt() else 0  
                Log.e("Downloading", dl_progress.toString())  
                val summary = cursor.getString(  
                    cursor.getColumnIndexOrThrow(DownloadManager.COLUMN_URI)  
                )  
                val mimeType = cursor.getString(  
                    cursor.getColumnIndexOrThrow(DownloadManager.COLUMN_MEDIA_TYPE)  
                )  
  
                when(status){  
                    DownloadManager.STATUS_SUCCESSFUL -> {  
                        val msg = handler.obtainMessage()  
                        msg.what = SUCCESS_DOWNLOADING  
                        val bundle = Bundle()  
                        bundle.putString(DESTINATION, destination)  
                        bundle.putString(URI, uri.path)  
                        msg.data = bundle  
                        handler.sendMessage(msg)  
                        downloadStarted = false  
  
  
                    }  
                    DownloadManager.STATUS_FAILED -> {  
                        downloadStarted = false  
                        val msg = handler.obtainMessage()  
                        msg.what = ERROR_DOWNLOADING  
                        handler.sendMessage(msg)  
                    }  
  
                    DownloadManager.STATUS_PAUSED -> {  
                        downloadStarted=false  
                        val msg = handler.obtainMessage()  
                        msg.what = ERROR_DOWNLOADING  
                        handler.sendMessage(msg)  
                    }  
                    DownloadManager.STATUS_RUNNING, DownloadManager.STATUS_PENDING ->{  
                        val msg = handler.obtainMessage()  
                        msg.what = DOWNLOAD_RUNNING  
                        msg.arg1 = dl_progress  
                        handler.sendMessage(msg)  
                    }  
  
  
                }  
  
                statusMessage(cursor).let { Log.d("APP_TAG", "$it, $summary, $mimeType, $bytes_total, $bytes_downloaded") }  
                cursor.close()  
  
            }  
  
        }  
  
            downloadThread.start()  
  
        }  
  
  
    }  
  
    @SuppressLint("Range")  
    private fun statusMessage(c: Cursor): String {  
        var msg = "???"  
        msg =  
            when (c.getInt(c.getColumnIndex(DownloadManager.COLUMN_STATUS))) {  
                DownloadManager.STATUS_FAILED -> "Download failed!"  
                DownloadManager.STATUS_PAUSED -> "Download paused!"  
                DownloadManager.STATUS_PENDING -> "Download pending!"  
                DownloadManager.STATUS_RUNNING -> "Download in progress!"  
                DownloadManager.STATUS_SUCCESSFUL -> "Download complete!"  
                else -> "Download is nowhere in sight"  
            }  
        return msg  
    }  
  
  
  
  
  
  
  
  
}
```