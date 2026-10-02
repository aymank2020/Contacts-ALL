# مراجعة Contacts-ALL وخطة التطوير

<!-- review-metadata -->
تاريخ المراجعة: 2026-10-02. الفرع المحلي: `codex/review-develop-2026-10-02`.

المصدر: [aymank2020/Contacts-ALL](https://github.com/aymank2020/Contacts-ALL)؛ commit الأساس: `55c458461dd5b6b0785047a0e7a45d7013fe9d63`؛ عدد الملفات المتتبعة في الأساس: 51. Fork: true؛ مؤرشف: false.

نُفذت المرحلة المحددة أدناه بعد مراجعة الكود والاختبارات وتطبيق مراجعة التكامل والأثر؛ المراحل التالية والفجوات لا تُعد مكتملة.

تطبيق Android 29 تعليمي يعرض 13 اسمًا ثابتًا في Fragment داخل ViewPager. لا يقرأ جهات اتصال الجهاز، ولا يجوز وصف بياناته الحالية بأنها سجل الهاتف الفعلي.

## التنفيذ الحالي

تهيئة القائمة في `onCreate` مع تفريغها قبل التعبئة، واستخدام `requireContext` للـLayoutManager. فُصل adapter عن RecyclerView وأُزيلت مراجع العرض عند `onDestroyView`، وأُزيل تخزين Context المأخوذ من `onAttach`. أضيف Maven Central قبل JCenter مع حفظ نسخ dependencies.

## خطة المراحل التالية

1. اختبار التنقل بين الصفحات وإعادة إنشاء العرض للتأكد من ثبات 13 عنصرًا وعدم الاحتفاظ بعرض قديم.
2. تحديد هل المنتج قائمة تدريبية أم قارئ جهات اتصال؛ المسار الثاني يحتاج Contacts Provider وأقل أذونات وحالات رفض الإذن، مع بيانات اصطناعية للاختبار.
3. إضافة البحث وإظهار القائمة الفارغة والإتاحة، ثم تحديث أداة البناء بصورة منفصلة عن منطق البيانات.

## التكامل والتحقق

المسار: Activity/ViewPager → ContactsFragment → ContactsAdapter → RecyclerView. الأثر للمستخدم: إعادة إنشاء عرض Fragment لا تضاعف العناصر ولا تبقي ارتباطًا بالعرض السابق. نجح `assembleDebug testDebugUnitTest` باستخدام JDK8 وGradle5.6.4 وSDK المحلي؛ السجل `evidence/contacts-gradle-final.log`. اختبارات الوحدة الأصلية لا تثبت سلوك دورة الحياة، ولا جهاز ADB متصل لاختباره تفاعليًا. لم تُقرأ جهات اتصال شخصية أو تُطلب أذوناتها.

## مصادر أولية

- [Android: دورة حياة Fragment](https://developer.android.com/guide/fragments/lifecycle)
- [Android: طلب الأذونات وقت التشغيل](https://developer.android.com/training/permissions/requesting)
- [Gradle: انتهاء JCenter](https://blog.gradle.org/jcenter-shutdown)
