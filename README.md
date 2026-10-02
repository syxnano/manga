# Manga Reader TTS

แอป Android สำหรับอ่านออกเสียงข้อความภาษาไทยจากภาพมังงะที่แสดงอยู่บนหน้าจอของแอปอื่น (ไม่ต้องแคปภาพเอง)

## คุณสมบัติหลัก

- ปุ่มลอย (SYSTEM_ALERT_WINDOW) ย้ายได้ โปร่งแสงเมื่อไม่ใช้
- โหมดเลือกข้อความ 3 แบบ: TAP / CROP / FULL_SCREEN
- จับภาพด้วย MediaProjection แบบ on-demand เท่านั้น (ไม่เปลืองเครื่อง)
- OCR ภาษาไทยแบบปลั๊กอิน (Google Cloud Vision + Tesseract offline)
- ประมวลผลข้อความไทย (รวมบรรทัด, กรอง SFX, แปลง 555 เป็นเสียงหัวเราะ)
- TTS ด้วย Android TextToSpeech เสียงไทย
- ธีมมืด ประหยัดพลังงาน

## ความต้องการระบบ

- Android 8.0 (API 26) ขึ้นไป
- แนะนำ RAM 3 GB+
- สิทธิ์: Overlay, MediaProjection, Notification, Internet

## วิธี Build

1. เปิดโปรเจกต์ด้วย **Android Studio Ladybug (2024.2+)** หรือใหม่กว่า
2. Sync Gradle
3. ใส่ Google Cloud Vision API Key ในหน้า Settings ของแอป (หรือใช้ Proxy)
4. (ถ้าต้องการ Offline) ดาวน์โหลด `tha.traineddata` จาก [tessdata](https://github.com/tesseract-ocr/tessdata) แล้ววางใน `filesDir/tessdata/`
5. Build → Build APK(s)

```bash
./gradlew assembleDebug
```

APK จะอยู่ที่ `app/build/outputs/apk/debug/app-debug.apk`

## การตั้งค่าสำคัญ

### 1. สิทธิ์ Overlay
Settings → Apps → Manga Reader TTS → Display over other apps → Allow

### 2. MediaProjection
ทุกครั้งที่เริ่มเซสชัน (โดยเฉพาะ Android 14+) ระบบจะขออนุญาตจับภาพหน้าจอใหม่

### 3. ปิด Battery Optimization
**สำคัญมาก** โดยเฉพาะเครื่องจีน:
- Xiaomi / Redmi / POCO → Battery saver = No restrictions + Autostart
- Oppo / Realme / Vivo → Don't optimize + Autostart
- Huawei → App launch = Manual
- Samsung → Unrestricted + ไม่ใส่ Sleeping apps

### 4. API Key
- สร้าง API Key ที่ [Google Cloud Console](https://console.cloud.google.com/) เปิด Cloud Vision API
- ใส่ในแอป หรือส่งผ่าน Proxy ของตัวเองเพื่อความปลอดภัย

## โครงสร้างโปรเจกต์

```
app/src/main/java/com/mangareader/tts/
├── MainActivity.kt
├── MangaReaderApp.kt
├── overlay/
│   ├── FloatingButtonService.kt
│   ├── HighlightOverlayView.kt
│   └── SelectionMode.kt
├── capture/
│   ├── ScreenCaptureManager.kt
│   └── ImagePreprocessor.kt
├── ocr/
│   ├── OcrEngine.kt
│   ├── GoogleVisionOcr.kt
│   ├── TesseractOcr.kt
│   ├── OcrCache.kt
│   └── OcrRepository.kt
├── text/
│   ├── TextProcessor.kt
│   └── ReadingOrder.kt
├── tts/
│   └── TtsManager.kt
├── ui/
│   ├── theme/Theme.kt
│   └── screens/MainScreen.kt
├── data/
│   └── AppSettings.kt
└── util/
    ├── BootReceiver.kt
    └── BatteryOptimizationHelper.kt
```

## ข้อจำกัดที่ทราบ

- โหมด TAP และ CROP ยังเป็น prototype (ใช้ full screen เป็นหลัก)
- Tesseract offline เป็น stub — ต้องเพิ่ม dependency และ implement เพิ่ม
- MediaProjection บน Android 14+ ต้องขอใหม่ทุกครั้ง
- บางเครื่องจีนอาจฆ่า overlay แม้จะตั้ง Unrestricted แล้ว
- ไม่มี correction dialog UI แบบเต็มในเวอร์ชันนี้ (ตั้งค่าได้ แต่ยังไม่ popup)

## ความเป็นส่วนตัว

- ไม่บันทึกภาพมังงะลงเครื่อง
- ส่งภาพเฉพาะส่วนที่ผู้ใช้เลือก และเฉพาะเมื่อใช้โหมด Cloud
- API Key เก็บใน EncryptedSharedPreferences
- ไม่มี analytics หรือการอัปโหลดใด ๆ นอกเหนือจาก OCR

## License

สำหรับการศึกษาและใช้งานส่วนตัวเท่านั้น
