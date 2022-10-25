Нужно имплементировать зависимость

```Gradle 
implementation 'com.journeyapps:zxing-android-embedded:4.1.0'
```

``` Kotlin

val myText: String = SettingsSaver.getSite()  
val mWriter = MultiFormatWriter()  
try {  
    //BitMatrix class для кодирования текста и размера изображения
    val mMatrix = mWriter.encode(myText, BarcodeFormat.QR_CODE, 220, 220)  
    val mEncoder = BarcodeEncoder()  
    val mBitmap: Bitmap = mEncoder.createBitmap(mMatrix) //создаем изображение  
    binding.qrCode.setImageBitmap(mBitmap)   
} catch (e: WriterException) {  
    e.printStackTrace()  
}
```


Изменив BarcodeFormat, можно создать не только Qr код, но и Штрих коды разных форматов