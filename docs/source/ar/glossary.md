# قاموس المصطلحات

يحدد هذا المسرد مصطلحات التعلم الآلي العامة و 🤗 Transformers لمساعدتك على فهم الوثائق بشكل أفضل.

## A

### قناع الانتباه (Attention Mask)

قناع الانتباه هو مُدخل اختياري يستخدم عند تجميع التسلسلات معًا

<Youtube id="M6adb1j2jPI"/>

يشير هذا المُدخل إلى النموذج أى الرموز المميزة (tokens) التي يجب الانتباه إليها، وأيها لا ينبغي ذلك.

على سبيل المثال، تأمّل هذين التسلسُلين :

```python
>>> from transformers import BertTokenizer

>>> tokenizer = BertTokenizer.from_pretrained("google-bert/bert-base-cased")

>>> sequence_a = "This is a short sequence."
>>> sequence_b = "This is a rather long sequence. It is at least longer than sequence A."

>>> encoded_sequence_a = tokenizer(sequence_a)["input_ids"]
>>> encoded_sequence_b = tokenizer(sequence_b)["input_ids"]
```

لدى الإصدارات المشفرة أطوال مختلفة:

```python
>>> len(encoded_sequence_a), len(encoded_sequence_b)
(8, 19)
```

لذلك، لا يمكننا وضعها معًا في نفس المصفوفة كما هي. يجب إضافة حشو إلى التسلسل الأول حتى يصل إلى طول التسلسل الثاني، أو يجب تقليص الثاني إلى طول الأول.

في الحالة الأولى، يتم تمديد قائمة المعرفات بواسطة مؤشرات الحشو. يمكننا تمرير قائمة إلى المحلل اللغوي وطلب منه إضافة الحشو بهذه الطريقة:

```python
>>> padded_sequences = tokenizer([sequence_a, sequence_b], padding=True)
```

يمكننا أن نرى أنه تمت إضافة اصفار على يمين الجملة الأولى لجعلها بنفس طول الجملة الثانية:

```python
>>> padded_sequences["input_ids"]
[[101, 1188, 1110, 170, 1603, 4954, 119, 102, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], [101, 1188, 1110, 170, 1897, 1263, 4954, 119, 1135, 1110, 1120, 1655, 2039, 1190, 1103, 4954, 138, 119, 102]]
```

يمكن بعد ذلك تحويل هذا إلى مصفوفة في PyTorch أو TensorFlow. قناع الانتباه هو مصفوفة ثنائية تشير إلى
موضع  المؤشرات المحشوه بحيث لا ينتبه إليها النموذج. بالنسبة إلى [`BertTokenizer`]`1` يشير إلى
قيمة يجب الانتباه إليها، في حين يشير `0` إلى قيمة مبطنة. يُمكن إيجاد قناع الانتباه في القاموس الذي يُعيده مُجزِّئ النصوص (tokenizer) تحت المفتاح "attention_mask".
```python
>>> padded_sequences["attention_mask"]
[[1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]]
```

### نماذج الترميز التلقائي (autoencoding models)

راجع [نماذج الترميز](#encoder-models) و [نمذجة اللغة المقنعة](#masked-language-modeling-mlm)

### النماذج ذاتية الانحدار (Autoregressive Models)

راجع [نمذجة اللغة السببية](#causal-language-modeling) و [نماذج فك التشفير](#decoder-models)

## B

### العمود الفقري (backbone)

يُمثل العمود الفقري الشبكة العصبونية (الترميزات والطبقات) المسؤولة عن إخراج الحالات الخفية أو المُميزات الأولية. عادة ما يكون متصلاً بـ [رأس](#head) يستقبل المُميزات كمدخلات لإجراء تنبؤ. على سبيل المثال، يُعد النموذج [`ViTModel`] عمودًا فقريًا دون رأس مُحدد مُرفق به. يمكن أيضًا استخدام `ViTModel` كعمود فقري في نماذج أخرى, مثل [DPT](model_doc/dpt).

## C

### نمذجة اللغة السببية (أو التنبؤية) causal language modeling

مهمة ما قبل التدريب يقوم فيها النموذج بقراءة النصوص بالترتيب ويتنبأ بالكلمة التالية. يتم ذلك عادةً من خلال قراءة الجملة كاملةً، ولكن مع استخدام قناع داخل النموذج لإخفاء الرموز المميزة اللاحقة في خطوة زمنية معينة.



### قناة(channel)

تتكون الصور الملونة من مزيج من القيم في ثلاث قنوات لونية: الأحمر والأخضر والأزرق (RGB) بينما تحتوي صور ذات التدرج رمادي على قناة واحدة فقط. في مكتبة 🤗 Transformers، يمكن أن تكون القناة اللونية البُعد الأول أو الأخير في مُصفوفة الصورة: [`n_channels`، `height`، `width`] أو [`height`، `width`، `n_channels`].

### التصنيف الزمني التوصيلي connectionist temporal classification (CTC)

خوارزمية تسمح للنموذج بالتعلم دون معرفة كيفية محاذاة المدخلات مع المخرجات بدقة؛ يحسب CTC توزيع جميع المخرجات المحتملة لمدخلات مُحددة ويختار المخرج الأكثر احتمالًا. تُستخدم CTC بشكل شائع في مهام التعرف على الكلام نظرًا لأن الكلام المنطوق لا يتوافق دائمًا بشكل مُباشر مع النص المكتوب، لأسباب مختلفة مثل معدلات الكلام المختلفة للمتكلم.

### الالتفاف (Convolution)

نوع من الطبقات في شبكة عصبية، حيث تُضرب مصفوفة الإدخال عُنصرًا بُعنصر بمصفوفة أصغر تُسمى (النواة أو المرشح) ويتم جمع القيم في مصفوفة جديدة. يُعرف هذا باسم عملية الالتفاف التي يتم تكرارها عبر مصفوفة الإدخال بأكملها. تُطبق كل عملية التفاف على جزء مُختلف من مصفوفة الإدخال. تُستخدم الشبكات العصبية الالتفافية (CNNs) بشكل شائع في رؤية الحاسوب.

## D

### التوازي على مستوى البيانات (DataParallel - DP)

هي تقنية تُستخدم لتدريب النماذج على عدة وحدات معالجة رسومات (GPUs)، حيث يتم نسخ نفس إعداد التدريب عدة مرات، بحيث تتلقى كل نسخة شريحة مختلفة من البيانات يتم تنفيذ المعالجة بالتوازي ويتم مزامنة جميع الإعدادات في نهاية كل خطوة تدريب.

تعرف على المزيد حول كيفية عمل DataParallel [هنا](perf_train_gpu_many#dataparallel-vs-distributeddataparallel).

### معرفات مدخلات وحدة فك التشفير (decoder input IDs)

هذا المدخل خاص بنماذج الترميز وفك التشفير، ويحتوي على معرفات الإدخال التي سيتم تغذيتها إلى وحدة فك التشفير.
يجب استخدام هذه المدخلات لمهام التسلسل إلى التسلسل، مثل الترجمة أو التلخيص، وعادة ما يتم بناؤها بطريقة محددة لكل نموذج.

تقوم معظم نماذج الترميز وفك التشفير (BART، T5) بإنشاء معرفات `decoder_input_ids` الخاصة بها من `labels`. في مثل هذه النماذج،
يعد تمرير `labels` هو الطريقة المفضلة للتعامل مع التدريب.

يرجى التحقق من وثائق كل نموذج لمعرفة كيفية تعاملها مع معرفات الإدخال هذه للتدريب على التسلسل إلى التسلسل.

### نماذج فك التشفير (decoder models)

يُشار إليها أيضًا باسم نماذج التنبؤية الذاتية، وتنطوي نماذج فك التشفير على مهمة ما قبل التدريب (تسمى نمذجة اللغة السببية) حيث يقرأ النموذج النصوص بالترتيب ويتعين عليه التنبؤ بالكلمة التالية. يتم ذلك عادةً عن طريق
قراءة الجملة بأكملها مع قناع لإخفاء الرموز المميزة المستقبلية في خطوة زمنية معينة.

<Youtube id="d_ixlCubqQw"/>
### التعلم العميق deep learning (DL)
خوارزميات التعلم الآلي التي تستخدم الشبكات العصبية متعددة الطبقات.

## E

### نماذج الترميز (encoder models)

تُعرف أيضًا باسم نماذج الترميز التلقائي، وتأخذ نماذج الترميز إدخالًا (مثل النص أو الصور) وتحويلها إلى تمثيل رقمي مكثف يُطلق عليه الترميز. غالبًا ما يتم تدريب نماذج الترميز مسبقًا باستخدام تقنيات مثل [نمذجة اللغة المقنعة](#masked-language-modeling-mlm)، والتي تقوم بإخفاء أجزاء من تسلسل الإدخال وإجبار النموذج على إنشاء تمثيلات أكثر دلالة (فائدة ووضوحاً).

<Youtube id="H39Z_720T5s"/>

## F
### استخراج الميزات (feature extraction)

عملية اختيار وتحويل البيانات الأولية إلى مجموعة من الميزات الأكثر إفادة وفائدة لخوارزميات التعلم الآلي. بعض الأمثلة على استخراج الميزات تشمل تحويل النص الأولي/الخام إلى ترميزات الكلمات واستخراج ميزات مهمة مثل الحواف أو الأشكال من بيانات الصور/الفيديو.

### تجزئة التغذية الأمامية (feed forward chunking)

في كل وحدة الانتباه الباقية في المحولات، تلي طبقة الاهتمام الانتباه عادة طبقتان للتغذية الأمامية.
حجم تضمين الطبقة الأمامية الوسيطة أكبر عادة من حجم المخفي للنموذج (على سبيل المثال، لـ
`google-bert/bert-base-uncased`).
بالنسبة لإدخال بحجم `[batch_size, sequence_length]`، يمكن أن تمثل الذاكرة المطلوبة لتخزين التضمينات الأمامية الوسيطة `[batch_size، sequence_length, config.intermediate_size]` جزءًا كبيرًا من استخدام الذاكرة. لاحظ مؤلفو (https://arxiv.org/abs/2001.04451)[Reformer: The Efficient Transformer] أنه نظرًا لأن الحساب مستقل عن بعد `sequence_length`، فإنه من المكافئ رياضيًا حساب تضمينات الإخراج الأمامية `[batch_size، config.hidden_size]_0, ..., [batch_size، `config_size]_n
فردياً والتوصيل بها لاحقًا إلى `[batch_size, sequence_length, config.hidden_size]` مع `n = sequence_length`، والذي يتداول زيادة وقت الحساب مقابل تقليل استخدام الذاكرة، ولكنه ينتج عنه نتيجة مكافئة رياضيا.

بالنسبة للنماذج التي تستخدم الدالة `[apply_chunking_to_forward]`، يحدد `chunk_size` عدد التضمينات يتم حساب الإخراج بالتوازي وبالتالي يحدد المقايضة بين حجم الذاكرة والتعقيد الوقت. إذا تم تعيين `chunk_size` إلى `0`، فلن يتم إجراء تجزئة التغذية الأمامية.


### النماذج المضبوطة (finetuned models)

الضبط الدقيق هو شكل من أشكال نقل التعلم، يتضمن أخذ نموذج مُدرّب مسبقًا، وتجميد أوزانه، واستبدال طبقة الإخراج برأس نموذج مُضاف حديثًا. يتم تدريب رأس النموذج على مجموعة البيانات المستهدفة.

راجع البرنامج التعليمي [Fine-tune a pretrained model](https://huggingface.co/docs/transformers/training) لمزيد من التفاصيل، وتعرف على كيفية ضبط النماذج باستخدام 🤗 Transformers.

## H

### رأس النموذج (head)

يشير رأس النموذج إلى الطبقة الأخيرة من الشبكة العصبية التي تقبل الحالات المخفية الخام/الأولية وتُسقطها على بُعد مختلف. يوجد رأس نموذج مختلف لكل مهمة.

  * [`GPT2ForSequenceClassification`] هو رأس تصنيف تسلسل - طبقة خطية - أعلى نموذج [`GPT2Model`] الأساسي.
  * [`ViTForImageClassification`] هو رأس تصنيف صورة - طبقة خطية أعلى حالة مخفية نهائية للرمز `CLS` - أعلى نموذج [`ViTModel`] الأساسي.
  * [`Wav2Vec2ForCTC`] هو رأس نمذجة اللغة مع [CTC](#connectionist-temporal-classification-ctc) أعلى نموذج [`Wav2Vec2Model`] الأساسي.

## I

### رقعة الصور (image patch)

"رقعة الصورة" في نماذج المحولات البصرية، تُقسم الصورة إلى أجزاء أصغر تسمى "رقعات". يتم تمثيل كل رقعة بشكل رقمي (تحويلها إلى مجموعة من الأرقام) ثم تُعالج كسلسلة من البيانات. يمكنك العثور على حجم الرُقعة patch_size - أو دقتها - في إعدادات النموذج.

### الاستدلال (Inference)

الاستدلال هو عملية تقييم نموذج على بيانات جديدة بعد اكتمال التدريب. راجع البرنامج التعليمي [Pipeline for inference](https://huggingface.co/docs/transformers/pipeline_tutorial) لمعرفة كيفية إجراء الاستدلال باستخدام 🤗 Transformers.

### معرفات الإدخال (input IDs)

معرفات الإدخال هي غالبًا المعلمات المطلوبة الوحيدة التي يجب تمريرها إلى النموذج كإدخال. هذه المعرفات عبارة عن أرقام تمثل كل كلمة أو رمز في الجملة التي نريد أن يفهمها النموذج. بمعنى آخر، هي طريقة لترجمة الكلمات إلى أرقام يتم استخدامها كإدخال بواسطة النموذج.

<Youtube id="VFp38yj8h3A"/>

يعمل كل محلل لغوي بشكل مختلف ولكن الآلية الأساسية تبقى كما هي. إليك مثال باستخدام محلل BERT اللغوي، والذي يعد محلل لغوي [WordPiece](https://arxiv.org/pdf/1609.08144.pdf):

```python
>>> from transformers import BertTokenizer

>>> tokenizer = BertTokenizer.from_pretrained("google-bert/bert-base-cased")

>>> sequence = "A Titan RTX has 24GB of VRAM"
```

يتولى المحلل اللغوي مهمة تقسيم التسلسل إلى رموز مميزة متوفرة في قاموس المحلل اللغوي.

```python
>>> tokenized_sequence = tokenizer.tokenize(sequence)
```

االرموز إما كلمات أو أجزاء كلمات. هنا على سبيل المثال، لم تكن كلمة "VRAM" موجودة في مفردات النموذج، لذلك تم تقسيمها إلى "V" و "RA" و "M". للإشارة إلى أن هذه الرموز ليست كلمات منفصلة ولكنها أجزاء من نفس الكلمة، تمت إضافة بادئة مزدوجة (#) إلى "RA" و "M":
```python
>>> print(tokenized_sequence)
['A', 'Titan', 'R', '##T', '##X', 'has', '24', '##GB', 'of', 'V', '##RA', '##M']
```
```python
>>> print(tokenized_sequence)
['A'، 'Titan'، 'R'، '##T'، '##X'، 'has'، '24'، '##GB'، 'of'، 'V'، '##RA'، '##M']
```

يمكن بعد ذلك تحويل هذه الرموز إلى مُعرفات يفهمها النموذج. يمكن القيام بذلك عن طريق تغذية الجملة مباشرةً إلى مُجزّئ الرموز، والذي يستفيد من تنفيذ 🤗 Tokenizers بلغة Rust للحصول على أعلى أداء.

```python
>>> inputs = tokenizer(sequence)
```

يقوم المحلل اللغوي بإرجاع قاموس يحتوي على جميع المعلومات التي يحتاجها النموذج للعمل بشكل صحيح. وتوجد مؤشرات الرموز المميزة تحت مفتاح `input_ids`:

```python
>>> encoded_sequence = inputs["input_ids"]
>>> print(encoded_sequence)
[101، 138، 18696، 155، 1942، 3190، 1144، 1572، 13745، 1104، 159، 9664، 2107، 102]
```

لاحظ أن المحلل اللغوي يضيف تلقائيًا "رموزًا خاصة" (إذا كان النموذج المرتبط يعتمد عليها) وهي معرفات خاصة
يستخدمها النموذج في بعض الأحيان.

إذا قمنا بفك تشفير التسلسل السابق،

```python
>>> decoded_sequence = tokenizer.decode(encoded_sequence)
```

سنرى

```python
>>> print(decoded_sequence)
[CLS] A Titan RTX has 24GB of VRAM [SEP]
```

لأن هذه هي الطريقة التي يتوقع بها نموذج [`BertModel`] إدخالاته.

## L

### االملصقات (Labels)

هي معامل اختياري يمكن إدخاله في النموذج لحساب الخسارة بنفسه.
نماذج تصنيف التسلسل: ([BertForSequenceClassification]) يتوقع النموذج مصفوفة ذات بعد (batch_size) حيث تتوافق كل قيمة من المجموعة مع الملصق المتوقع للتسلسل بأكمله.
نماذج تصنيف الرمز: ([BertForTokenClassification]) يتوقع النموذج مصفوفة ذات بعد (batch_size, seq_length) حيث تتوافق كل قيمة مع الملصق المتوقع لكل رمز فردي.
نماذج النمذجة اللغوية المقنعة:([BertForMaskedLM]) يتوقع النموذج مصفوفة ذات بعد (batch_size, seq_length) حيث تتوافق كل قيمة مع الملصق المتوقع لكل رمز فردي: تكون الملصقات هي معرف رمز الكلمة المقنعة، والقيم الأخرى يتم تجاهلها (عادةً -100).
مهام التسلسل إلى التسلسل: ([BartForConditionalGeneration], [MBartForConditionalGeneration]) يتوقع النموذج مصفوفة ذات بعد (batch_size, tgt_seq_length) حيث تتوافق كل قيمة مع التسلسل الهدف المرتبط بكل تسلسل مدخل. أثناء التدريب، سيقوم كل من BART و T5 بإنشاء decoder_input_ids و decoder attention masks داخليًا. عادةً لا يلزم توفيرها. هذا لا ينطبق على النماذج التي تستخدم إطار العمل Encoder-Decoder.
نماذج تصنيف الصور: ([ViTForImageClassification]) يتوقع النموذج مصفوفة ذات بعد (batch_size) حيث تتوافق كل قيمة من المجموعة مع الملصق المتوقع لكل صورة فردية.
نماذج التقسيم الدلالي: ([SegformerForSemanticSegmentation]) يتوقع النموذج مصفوفة ذات بعد (batch_size, height, width) حيث تتوافق كل قيمة من المجموعة مع الملصق المتوقع لكل بكسل فردي.
نماذج اكتشاف الأجسام: ([DetrForObjectDetection]) يتوقع النموذج قائمة من القواميس تحتوي على مفتاح class_labels و boxes حيث تتوافق كل قيمة من المجموعة مع الملصق المتوقع وعدد المربعات المحيطة بكل صورة فردية.
نماذج التعرف التلقائي على الكلام: ([Wav2Vec2ForCTC]) يتوقع النموذج مصفوفة ذات بعد (batch_size, target_length) حيث تتوافق كل قيمة مع الملصق المتوقع لكل رمز فردي.

<Tip>

قد تختلف تسميات كل نموذج، لذا تأكد دائمًا من مراجعة وثائق كل نموذج للحصول على معلومات حول التسميات الخاصة به.

</Tip>
لا تقبل النماذج الأساسية ([`BertModel`]) الملصقات ، لأنها نماذج المحول الأساسية، والتي تقوم ببساطة بإخراج الميزات.

### نماذج اللغة الكبيرة large language models (LLM)

مصطلح عام يشير إلى نماذج اللغة المحولة (GPT-3 و BLOOM و OPT) التي تم تدريبها على كمية كبيرة من البيانات. تميل هذه النماذج أيضًا إلى وجود عدد كبير من المعلمات القابلة للتعلم (على سبيل المثال، 175 مليار لمعلمة GPT-3).

## M

### نمذجة اللغة المقنعة masked language modeling (MLM)

مهمة تدريب مسبق حيث يرى النموذج نسخة تالفة من النصوص، وعادة ما يتم ذلك عن طريق حجب بعض الرموز بشكل عشوائي، ويتعين على النموذج التنبؤ بالنص الأصلي.

### متعدد الوسائط (multimodal)

مهمة تجمع بين النصوص مع نوع آخر من المدخلات (على سبيل المثال، الصور).

## N

### توليد اللغة الطبيعية Natural language generation (NLG)

جميع المهام المتعلقة بتوليد النص (على سبيل المثال، [اكتب باستخدام المحولات](https://transformer.huggingface.co/)، والترجمة).

### معالجة اللغة الطبيعية Natural language processing (NLP)

طريقة عامة للقول "التعامل مع النصوص".

### فهم اللغة الطبيعية Natural language understanding (NLU)

جميع المهام المتعلقة بفهم ما هو موجود في نص (على سبيل المثال تصنيف النص بأكمله، أو الكلمات الفردية).

## P

### خط الأنابيب (pipeline)

في مكتبة Transformers، يُشير مصطلح "خط الأنابيب" إلى سلسلة من الخطوات التي يتم تنفيذها بترتيب محدد لمعالجة البيانات وتحويلها وإرجاع تنبؤ من نموذج. بعض المراحل الشائعة في خط الأنابيب قد تشمل معالجة البيانات الأولية، واستخراج الميزات، والتوحيد.

للحصول على مزيد من التفاصيل، راجع [خطوط الأنابيب للاستدلال](https://huggingface.co/docs/transformers/pipeline_tutorial).

### التوازي على مستوى خط الأنابيب (PipelineParallel)

تقنية توازي يتم فيها تقسيم النموذج رأسياً (على مستوى الطبقة) عبر وحدات معالجة الرسومات (GPU) متعددة، بحيث توجد طبقة واحدة أو عدة طبقات من النموذج على وحدة معالجة الرسومات (GPU) واحدة فقط. تقوم كل وحدة معالجة رسومات (GPU) بمعالجة مراحل مختلفة من خط الأنابيب بالتوازي والعمل على جزء صغير من الدفعة. تعرف على المزيد حول كيفية عمل PipelineParallel [هنا](perf_train_gpu_many#from-naive-model-parallelism-to-pipeline-parallelism).

### قيم البكسل (pixel values)

مصفوفة من التمثيلات الرقمية لصورة يتم تمريرها إلى نموذج. تأخذ قيم البكسل شكل [`batch_size`، `num_channels`، `height`، `width`]، ويتم إنشاؤها من معالج الصور.

### التجميع (Pooling)

هي عملية تقوم بتقليص مصفوفة إلى مصفوفة أصغر، إما عن طريق أخذ القيمة القصوى أو المتوسط الحسابي للأبعاد التي يتم تجميعها. توجد طبقات التجميع بشكل شائع بين الطبقات التلافيفية convolutional layers لتقليل حجم تمثيل الميزات.

### معرفات الموضع (position IDs)

على عكس الشبكات العصبية المتكررة (RNNs) التي تتضمن موضع كل رمز (token) ضمن بنيتها، لا تدرك المحولات موضع كل رمز. لذلك، تستخدم معرفات الموضع (`position_ids`) من قبل النموذج لتحديد موضع كل رمز في قائمة الرموز.

إنها معلمة اختيارية. إذا لم يتم تمرير أي `position_ids` إلى النموذج، يتم إنشاء المعرفات تلقائيًا كترميزات موضعية مطلقة.

يتم اختيار الترميزات الموضعية المطلقة في النطاق `[0، config.max_position_embeddings - 1]`. تستخدم بعض النماذج أنواعًا أخرى من الترميزات الموضعية، مثل الترميزات الموضعية الجيبية أو الترميزات الموضعية النسبية.

### ما قبل المعالجة (preprocessing) 

مهمة إعداد البيانات الخام بتنسيق يمكن أن تستهلكه نماذج التعلم الآلي بسهولة. على سبيل المثال، عادةً ما تتم معالجة النص مسبقًا عن طريق التمييز. للحصول على فكرة أفضل عن كيفية ظهور المعالجة المسبقة لأنواع الإدخال الأخرى، راجع البرنامج التعليمي [Preprocess](https://huggingface.co/docs/transformers/preprocessing).

### النموذج المسبق التدريب (pretrained model)

نموذج تم تدريبه مسبقًا على بعض البيانات (على سبيل المثال، كل Wikipedia). تنطوي طرق التدريب المسبق على هدف ذاتي الإشراف، والذي يمكن أن يكون قراءة النص ومحاولة التنبؤ بالكلمة التالية ( راجع (causal-language-modeling#)[نمذجة اللغة السببية] ) أو قناع بعض الكلمات ومحاولة التنبؤ بها ( راجع (masked-language#)[نمذجة اللغة المقنعة]- عرض MLM).

لدى نماذج الكلام والرؤية أهدافها التدريبية المسبقة الخاصة. على سبيل المثال، Wav2Vec2 هو نموذج كلام تم تدريبه مسبقًا على مهمة تباينية تتطلب من النموذج تحديد تمثيل الكلام "الحقيقي" من مجموعة من تمثيلات الكلام "الخاطئة". من ناحية أخرى، BEiT هو نموذج رؤية تم تدريبه مسبقًا على مهمة نمذجة صورة مقنعة تقوم بقناع بعض رقع الصورة وتتطلب من النموذج التنبؤ بالرقع المقنعة (مشابهة لهدف نمذجة اللغة المقيدة).

## R

### شبكة عصبية متكررة (RNN)

هي نوع من النماذج التي تستخدم حلقة متكررة فوق طبقة معينة لمعالجة النصوص.

### التعلم التمثيلي (representation learning)

هو فرع من فروع تعلم الآلة يركز على تعلم تمثيلات ذات معنى للبيانات الخام. بعض الأمثلة على تقنيات التعلم التمثيلي تشمل تضمين الكلمات، والمشفرات ذاتية، وشبكات التنافس التوليدية(GANs).

## S

### معدل العينات (sampling rate)

قياس، بالهرتز، لعدد العينات (إشارة الصوت) المأخوذة في الثانية. ينتج معدل العينات عن تمييز إشارة مستمرة مثل الكلام.

### الانتباه الذاتي (Self-Attention)

هو آلية تتيح لكل عنصر في المدخل أن يحدد أي العناصر الأخرى في نفس المدخل يجب أن ينتبه إليها.

### التعلم الذاتي الخاضع للإشراف (supervised learning)

فئة من تقنيات التعلم الآلي التي يقوم فيها النموذج بإنشاء هدفه التعليمي الخاص من البيانات غير الموسومة. يختلف عن [التعلم غير الخاضع للإشراف](#unsupervised-learning) و [التعلم الخاضع للإشراف](#supervised-learning) في أن عملية التعلم خاضعة للإشراف، ولكن ليس صراحة من المستخدم.

مثال واحد على التعلم الذاتي الخاضع للإشراف هو [نمذجة اللغة المقيدة](#masked-language- عرض MLM)، حيث يتم تمرير جمل للنموذج مع إزالة نسبة من رموزه ويتعلم التنبؤ بالرموز المفقودة.

### التعلم شبه الخاضع للإشراف (semi-supervised learning)

فئة واسعة من تقنيات تدريب التعلم الآلي التي تستفيد من كمية صغيرة من البيانات الموسومة مع كمية أكبر من البيانات غير الموسومة لتحسين دقة النموذج، على عكس [التعلم الخاضع للإشراف](#supervised-learning) و [التعلم غير الخاضع للإشراف](#unsupervised-learning).

مثال على نهج التعلم شبه الخاضع للإشراف هو "التدريب الذاتي"، حيث يتم تدريب نموذج على بيانات موسومة، ثم يستخدم لتقديم تنبؤات حول البيانات غير الموسومة. يتم إضافة الجزء من البيانات غير الموسومة التي يتنبأ بها النموذج بأكبر قدر من الثقة إلى مجموعة البيانات الموسومة ويتم استخدامها لإعادة تدريب النموذج.

### تسلسل إلى تسلسل (seq2seq)

نماذج تولد تسلسلًا جديدًا من إدخال، مثل نماذج الترجمة، أو نماذج التلخيص (مثل [Bart](model_doc/bart) أو [T5](model_doc/t5)).

### Sharded DDP

اسم آخر لمفهوم [Zero Redundancy Optimizer](#zero-redundancy-optimizer-zero) الأساسي كما هو مستخدم من قبل العديد من التطبيقات الأخرى لـ Zero.

### الخطوة (Stride)

في العمليات التلافيفية أو التجميعية، تشير الخطوة إلى المسافة التي يتحرك بها النواة (kernel) فوق المصفوفة. خطوة تساوي 1 تعني أن النواة تتحرك بكسل واحد في كل مرة.

### التعلم الخاضع للإشراف (supervised learning)

هو نوع من تدريب النماذج التي تستخدم بيانات مُعلَّمة بشكل مباشر لتصحيح أداء النموذج وتوجيهه. يتم تغذية البيانات إلى النموذج قيد التدريب، ويتم مقارنة تنبؤاته بالنتائج الصحيحة المعروفة. يقوم النموذج بتعديل أوزانه بناءً على مدى خطأ تنبؤاته، وتتكرر هذه العملية لتحسين أداء النموذج.

## T

### توازي Tensor (TP)

تقنية توازي لتدريب وحدات معالجة الرسومات (GPU) متعددة يتم فيها تقسيم المصفوفة إلى عدة أجزاء، لذا بدلاً من وجود المصفوفة بأكملها على وحدة معالجة الرسومات (GPU) واحدة، توجد كل شظية من المصفوفة على وحدة معالجة الرسومات (GPU) المخصصة لها. تتم معالجة الشظايا بشكل منفصل وبالتوازي على وحدات معالجة الرسومات (GPU) المختلفة ويتم مزامنة النتائج في نهاية خطوة المعالجة. هذا ما يُطلق عليه أحيانًا التوازي الأفقي، حيث يحدث الانقسام على المستوى الأفقي.

تعرف على المزيد حول توازي Tensor [هنا](perf_train_gpu_many#tensor-parallelism).

### الرمز اللغوي (Token)

جزء من جملة، عادة ما يكون كلمة، ولكن يمكن أن يكون أيضًا كلمة فرعية (غالبًا ما يتم تقسيم الكلمات غير الشائعة إلى كلمات فرعية) أو علامة ترقيم.

### معرفات نوع الرمز (token type ids)

الغرض من بعض النماذج هو إجراء التصنيف على أزواج من الجمل أو الإجابة على الأسئلة.

<Youtube id="0u3ioSwev3s"/>

يتطلب ذلك تسلسلين مختلفين يتم دمجهما في إدخال "input_ids" واحد، والذي يتم عادةً باستخدام رموز خاصة، مثل رموز التصنيف (`[CLS]`) والفاصل (`[SEP]`). على سبيل المثال، يقوم نموذج BERT ببناء إدخال تسلسلين على النحو التالي:

```python
>>> # [CLS] SEQUENCE_A [SEP] SEQUENCE_B [SEP]
```

يمكننا استخدام برنامجنا للتمييز لإنشاء مثل هذه الجملة تلقائيًا عن طريق تمرير التسلسلين إلى `tokenizer` كمعامليين (وليس قائمة، كما كان من قبل) مثل هذا:

```python
>>> from transformers import BertTokenizer

>>> tokenizer = BertTokenizer.from_pretrained("google-bert/bert-base-cased")
>>> sequence_a = "HuggingFace is based in NYC"
>>> sequence_b = "Where is HuggingFace based?"

>>> encoded_dict = tokenizer(sequence_a، sequence_b)
>>> decoded = tokenizer.decode(encoded_dict["input_ids"])
```

والذي سيعيد:

```python
>>> print(decoded)
[CLS] HuggingFace is based in NYC [SEP] Where is HuggingFace based؟ [SEP]
```

هذا يكفي لبعض النماذج لفهم أين ينتهي تسلسل واحد وأين يبدأ الآخر. ومع ذلك، تستخدم نماذج أخرى، مثل BERT، أيضًا معرفات نوع الرمز (يُطلق عليها أيضًا معرفات الجزء). يتم تمثيلها كماسك ثنائي لتحديد نوعي التسلسل في النموذج.

يعيد برنامج الترميز هذا القناع كإدخال "token_type_ids":

```python
>>> encoded_dict["token_type_ids"]
[0، 0، 0، 0، 0، 0، 0، 0، 0، 0، 1، 1، 1، 1، 1، 1، 1، 1، 1]
```

يتم تمثيل التسلسل الأول، "السياق" المستخدم للسؤال، بجميع رموزه بواسطة `0`، في حين يتم تمثيل التسلسل الثاني، المقابل إلى "السؤال"، بجميع رموزه بواسطة `1`.

تستخدم بعض النماذج، مثل [`XLNetModel`] رمزًا إضافيًا يمثله `2`.

### التعلم الانتقالي (Transfer Learning)

تقنية تنطوي على أخذ نموذج تم تدريبه مسبقًا وتكييفه مع مجموعة بيانات خاصة بمهمتك. بدلاً من تدريب نموذج من الصفر، يمكنك الاستفادة من المعرفة المكتسبة من نموذج موجود كنقطة بداية. يسرع هذا عملية التعلم ويقلل من كمية بيانات التدريب المطلوبة.

### المحول (Transformer)

هو بنية لنموذج تعلم عميق يعتمد على الانتباه الذاتي.

## U

### التعلم غير الخاضع للإشراف (unsupervised learning)

شكل من أشكال تدريب النماذج حيث لا يتم وضع علامات على البيانات المقدمة إلى النموذج. تستفيد تقنيات التعلم غير الخاضعة للإشراف من المعلومات الإحصائية لتوزيع البيانات للعثور على الأنماط المفيدة للمهمة المعنية.

## Z

### محسن التكرار الصفري (ZeRO)

تقنية توازي تقوم بتشظية المصفوفات بطريقة مشابهة لـ [TensorParallel](#tensor-parallelism-tp)، باستثناء إعادة بناء المصفوفة بالكامل في الوقت المناسب لحساب التقدير أو الحساب الخلفي، وبالتالي لا يلزم تعديل النموذج. تدعم هذه الطريقة أيضًا تقنيات الإخلاء المختلفة للتعويض عن ذاكرة GPU المحدودة.

تعرف على المزيد حول Zero [هنا](perf_train_gpu_many#zero-data-parallelism).