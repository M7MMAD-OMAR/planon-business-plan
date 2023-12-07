# موصل الحدث (Event Connector)

هو أداة تقنية تستخدم لربط المعلومات والأحداث بين التطبيقات،
ويستخدم لتبادل البيانات والإشعارات بشكل آلي.
وعادةً ما يحتاج إلى استلام الرسائل الواردة وتحويلها ومعالجتها،
ويعتبر جزءًا من وحدات **نظام تبادل المعلومات**.

    يفضل فصل موصل الحدث عن المشروع الأساسي لعدة أسباب منها:
        - فصل المسؤوليات الخاصة بالمشروع
        - قابلية التوسعة وإعادة الإستخدام
        - تبسيط عملية الإختبار والصيانة

### عمل موصل الحدث

#### الرسائل الواردة:

```mermaid
graph LR
    A[Inbound]
    A --> B[Queue]
    B --> C[Transformation]
    C --> D[Queue]
    D --> E[Processing]

 ```

1. **الرسالة الواردة:** تستقبل الرسالة الواردة من مصدر خارجي.
2. **قائمة الانتظار:** توضع الرسالة في قائمة الانتظار للمعالجة.
3. **التحويل:** يتم تحويل الرسالة من تنسيقها الحالي إلى تنسيق يفهمه نظام "بلانون".
4. **قائمة الانتظار:** توضع الرسالة المحولة في قائمة الانتظار للمعالجة.
5. **المعالجة:** يتم معالجة الرسالة المحولة وفقًا **للمنطق** و**نموذجات القرار** المحددة.
6. **اتخاذ الإجراء:** يتم اتخاذ الإجراء المناسب وفقًا لنتائج المعالجة لـ "بلانون".

#### الرسائل الصادرة:

```mermaid
graph RL
    A[SX Context]
    A --> B[Queue]
    B --> C[Transformation]
    C --> D[Queue]
    D --> E[Transmission]
    E --> F[Target]

 ```

1. **السياق:** يتم إنشاء الرسالة الصادرة باستخدام سياق "بلانون".
2. **قائمة الانتظار:** تتم إضافة الرسالة الصادرة إلى قائمة الانتظار.
3. **التحويل:** يتم تحويل الرسالة من تنسيق "بلانون" إلى تنسيق يفهمه النظام الخارجي.
4. **قائمة الانتظار:** توضع الرسالة المحولة في قائمة الانتظار للإرسال.
5. **الإرسال:** يتم إرسال الرسالة المحولة إلى النظام الخارجي.
6. **الهدف:** يتم تسليم الرسالة إلى الهدف المحدد.

### هيكلية موصل الحدث

- `src`
    - `com.example`
        - `inbound`
            - `Processor`
            - `Transformer`
        - `outbound`
            - `Transformer`
            - `Transmitter`
- `config.json`

### شرح الملفات

1. `Processor`

يقوم هذا الصف البرمجي بالتحقيق من IInboundMessageProcessor

    public class Processor implements IInboundMessageProcessor { }  

وهو المسؤول عن معالجة الرسائل الواردة

يجب إنشاء هذه الدالة عند التحقيق

    @Override
    public void handleMessage(IInboundMessage inboundMessage, IInboundMessageProcessorContext processorContext) { } 

`@param processorContext سياق المعالج المستخدم للوصول إلى خدمة البيانات.`

2. `Transformer`

        public class Transformer implements IInboundMessageTransformer { }

يوفر طريقة لتحويل الرسالة الواردة إلى كائن يتضمن التحويل تحليل رسالة واستخراج قيم محددة لإضافتها كحقول في "بلانون"
استخدام
استدعاء طريقة التحويل من خلال توفير نص الرسالة وبيانات تعريف الرسالة وسياق المحول. تقوم هذه الطريقة بتحليل رسالة
واستخراج قيم محددة وإضافتها كحقول في الرسالة الواردة.

يجب استخدام هذه الدالة عند التحقيق

    @Override
    public IInboundMessage transform(String messageBody, IInboundMessageMetadata messageMetadata,
    IInboundMessageTransformerContext messageTransformerContext) throws MessageTransformationException { }

3. `Transformer`

        public class Transformer implements IOutboundMessageTransformer { }

, يحول الرسائل الصادرة إلى رسائل خام صادرة (outbound raw messages). تتم هذه العملية بتطبيق تنفيذ الدالة transform
المحققة من الواجهة IOutboundMessageTransformer.

4. `Transmitter`

        public class Transmitter implements IOutboundMessageTransmitter { }

يتم استخدام هذا الصف البرمجي، المعروفة باسم **Transmitter**، لإعداد وإرسال الرسائل الصادرة إلى نظام خارجي. تحقق قائمة
IOutboundMessageTransmitter أن الفئة تنفذ الطريقة send, التي مقدمة للعمل بوصفها نقطة البداية لإرسال الرسالة الصادرة.






