# Biochemistry Easy with Me — Android starter project

**الاسم:** Biochemistry Easy with Me  
**الناشر:** By Dr. Ahmed Al-Kisher · 2027 Edition  
**المنصة:** Android (Kotlin + Jetpack Compose)  
**الخلفية:** Firebase Authentication (custom tokens) + Cloud Firestore + Cloud Functions.

## ما تم تجهيزه في هذا المشروع
- شاشة تفعيل الطالب بكود اشتراك واسم الطالب.
- ربط تسجيل الدخول بدالة Cloud Function موثوقة تتحقق من الكود وتمنع استخدامه أكثر من مرة.
- تحميل الأسئلة المنشورة من Firestore.
- إرسال إجابة كل سؤال إلى Cloud Function للتصحيح؛ مفتاح الإجابة محفوظ في مجموعة منفصلة لا يستطيع تطبيق الطالب قراءتها.
- قواعد Firestore أولية تمنع قراءة مفاتيح الإجابات وأكواد الاشتراك من التطبيق.
- واجهة عربية الإرشادات/إنجليزية المصطلحات بتصميم Navy & Teal.

> **الحالة الحالية:** هذا مشروع مصدر أولي قابل للاستكمال والبناء في Android Studio، وليس ملف APK نهائياً. يلزم ربطه بمشروع Firebase خاص بالمدرس، وإضافة ملف `google-services.json`، وإنشاء أكواد وأسئلة، ثم بناء APK واختباره على جهاز Android فعلي. لا توجد بيانات اعتماد أو قاعدة بيانات حقيقية مرفقة.

## المتطلبات
- Android Studio حديث (يدعم Android Gradle Plugin 8.7.3).
- JDK 17.
- Android SDK Platform 35.
- Node.js 20 وFirebase CLI لتجهيز Cloud Functions.
- حساب Google لإنشاء مشروع Firebase.

## خطوات التجهيز
1. أنشئ مشروعاً جديداً في Firebase Console.
2. أضف تطبيق Android بالمعرّف `com.biochem.easy`.
3. نزّل `google-services.json` وضعه داخل مجلد `app/` بجانب `build.gradle.kts`.
4. فعّل Firestore Database.
5. من Firebase Authentication فعّل مزود Custom authentication إن ظهر ضمن إعدادات المشروع (تسجيل الدخول هنا يتم عبر Custom Token من Cloud Functions).
6. من مجلد المشروع نفّذ:
   ```bash
   npm --prefix functions install
   npm --prefix functions run build
   firebase login
   firebase use --add
   firebase deploy --only functions,firestore:rules
   ```
7. أنشئ كود اشتراك في Firestore يدوياً داخل `subscriptionCodes`، واجعل **معرّف المستند هو الكود**، وأضف الحقول:
   ```json
   { "active": true, "used": false }
   ```
   ويمكن إضافة `expiresAt` كقيمة Timestamp.
8. أضف سؤالاً في `questions` بالحقول التالية:
   ```json
   {
     "prompt": "Which molecule is the primary energy currency of the cell?",
     "options": ["ATP", "DNA", "Urea", "Cholesterol"],
     "explanation": "ATP stores and transfers usable energy in cells.",
     "published": true
   }
   ```
9. أضف مستنداً في `answerKeys` بنفس معرّف السؤال، وبالحقل:
   ```json
   { "correctIndex": 0 }
   ```
   الفهرس يبدأ من صفر: 0 = الخيار A، و1 = B، وهكذا. لا تضع `correctIndex` داخل مستند السؤال.
10. افتح المجلد في Android Studio، انتظر Gradle Sync، ثم اختر **Build > Build Bundle(s) / APK(s) > Build APK(s)**. ستجد ملف APK عادةً في `app/build/outputs/apk/debug/`.

## تنبيهات مهمة قبل النشر للطلاب
- قواعد Firestore هنا نقطة بداية ويجب مراجعتها واختبارها قبل نشر التطبيق تجارياً.
- دالة إنشاء أكواد الاشتراك/لوحة تحكم المدرس لم تُبنَ بعد؛ حالياً يمكن للمدرس إضافة الأكواد والأسئلة من Firebase Console. لا تمنح الطلاب صلاحية Firebase Console مطلقاً.
- دالة التصحيح تسجل إجابة كل سؤال منفردة، لكنها لا تنفذ بعد مؤقت امتحان موثوقاً أو تقرير نتائج مجمعاً أو سياسة محاولات. هذه ضمن مرحلة التطوير التالية.
- يجب إضافة سياسة خصوصية، وشروط استخدام، وآلية حذف حساب الطالب، واختبار الأمان قبل الإطلاق العام.
- لا تضع مفاتيح إدارية أو Service Account داخل تطبيق Android.
