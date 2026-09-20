# 🤖 Conversational Agent (Google Cloud) — شرح شامل بالعربي والإنجليزي

> **المصدر الأصلي / Original Source:**
> [Conversational Agent: How Google Is Redefining Chatbot Creation with Generative AI?](https://medium.com/google-cloud/conversational-agent-how-google-is-redefining-chatbot-creation-with-generative-ai-6893ed877ffe) — بقلم Nathan Brami (Google Cloud Community, أكتوبر 2025)

---

## 📌 نظرة عامة سريعة (TL;DR)

<table>
<tr>
<th>🇪🇬 بالعربي</th>
<th>🇬🇧 English</th>
</tr>
<tr>
<td>

جوجل أطلقت خدمة اسمها **Conversational Agent**، بتبني على خدمة قديمة اسمها **Dialogflow CX**، وبتضيف عليها قوة الذكاء الاصطناعي التوليدي (Generative AI) عشان تسهّل بناء شات بوتس ذكية ومرنة، من غير ما تكتب كل سيناريو بنفسك يدويًا.

</td>
<td>

Google launched a service called **Conversational Agent**, built on top of the existing **Dialogflow CX**, adding the power of Generative AI to make it easier to build smart, flexible chatbots — without manually coding every possible scenario.

</td>
</tr>
</table>

---

## 1️⃣ إيه هو الشات بوت (Conversational Agent)؟

### 🇪🇬 بالعربي
الشات بوت (أو "الوكيل المحادثي") هو تطبيق مصمم يتفاعل مع المستخدمين بالصوت أو الكتابة. بيقدر يجاوب على أسئلة، يقدّم معلومات، أو حتى ينفذ أفعال حقيقية عن طريق أنظمة خارجية (زي حجز موعد أو إنشاء تذكرة دعم).

من 2020، جوجل قدّمت **Dialogflow CX** كمنصة لبناء الشات بوتس دي. وفي 2025، تطورت الفكرة مع إطلاق **Conversational Agent** اللي بتدمج نماذج اللغة الكبيرة (LLMs) زي Gemini.

### 🇬🇧 English
A chatbot (or conversational agent) is an application designed to interact with users through speech or text. It can answer questions, provide information, or even perform actions via third-party systems.

Since 2020, Google has offered **Dialogflow CX** for building such chatbots. In 2025, this evolved with **Conversational Agent**, enhanced by Large Language Model (LLM) integration.

---

## 2️⃣ الفرق بين Dialogflow و Conversational Agent

### 🇪🇬 بالعربي
الخدمتين بيشتركوا في نفس الهدف (بناء شات بوت)، لكن بيختلفوا في التكنولوجيا والفلسفة:

| النوع | طريقة العمل |
|---|---|
| **الوكيل الحتمي (Deterministic Agent)** — Dialogflow CX | بيتبع سيناريوهات محددة مسبقًا، كل رد مكتوب يدويًا بالكود |
| **الوكيل التوليدي (Generative Agent)** — Conversational Agent | بيعتمد على LLM يقدر يجاوب بمرونة حتى في مواقف غير متوقعة |

**Conversational Agent بيسمحلك تدمج الاتنين مع بعض** في نموذج هجين (Hybrid).

### 🇬🇧 English
Both services share the same goal but differ in the underlying technology:

| Type | How it works |
|---|---|
| **Deterministic Agent** (Dialogflow CX) | Follows predefined scenarios; every response is explicitly coded |
| **Generative Agent** (Conversational Agent) | Relies on an LLM to answer flexibly, even in unexpected situations |

**Conversational Agent lets you orchestrate both approaches in a hybrid model.**

---

## 3️⃣ الـ Playbook — قلب النظام الجديد

### 🇪🇬 بالعربي
**الـ Playbook** هو العنصر المركزي في الجيل الجديد من الوكلاء الهجينة. فكّر فيه كـ**"دفتر تعليمات" أو "طبقة ذكية"** بتحط فوق نموذج LLM (زي Gemini) وبتتحكم في:
- سلوكه (إزاي يتصرف)
- أهدافه (إيه اللي المفروض يوصله)
- طريقة تفاعله مع المستخدم

بمعنى تاني: الـ Playbook مش بيغيّر النموذج نفسه، لكنه بيوجهه ويأطّر ردوده ويغنّيها بمصادر خارجية.

### 🇬🇧 English
The **Playbook** is the central element of this new generation of hybrid conversational agents. Think of it as an **"instruction layer"** placed over an LLM (like Gemini) that precisely configures:
- Its behavior
- Its goals
- How it interacts with the user

It acts as an intelligent overlay — orchestrating the model's reasoning, framing its responses, and enriching them with external resources.

---

## 4️⃣ مكونات الـ Playbook الأربعة

### 🇪🇬 بالعربي

#### أ) الهدف (Goal)
بيحدد الغرض من المحادثة — إيه اللي المفروض الوكيل يوصّله.
> مثال: حجز موعد طبي، أو تلخيص عقد قانوني بلغة بسيطة.

#### ب) التعليمات (Instructions)
بتحدد الخطوات اللي المفروض الوكيل يتبعها عشان يوصل للهدف.
> مثال:
> 1. اسأل المستخدم لو عنده تاريخ مفضل.
> 2. لو أيوه، اقترح موعد متاح.
> 3. لو لأ، اقترح 3 خيارات عشوائية.

#### ج) التوجيه بالأمثلة (Guide with Examples)
الأمثلة أساسية جدًا — بتوضح للـ LLM إزاي يتصرف في مواقف حقيقية:
- ✅ **نجاح**: الرد المثالي المتوقع
- ❌ **فشل**: إيه المفروض يقوله لو مش عارف الإجابة
- 🚫 **خارج النطاق**: إزاي يرفض بلباقة سؤال مش له علاقة بموضوعه

#### د) الأدوات (Tools)
ممكن تضيف أدوات توسّع من قدرات الوكيل:
- **Datastore**: قاعدة بيانات متجهة (vector database) تدار تلقائيًا، بتسمح بإضافة معرفة عن الشركة والبحث فيها باستخدام تقنية **RAG** (توليد معزز بالاسترجاع).
- **OpenAPI Tools**: بتربط الوكيل بـ APIs خارجية عشان ينفذ أفعال أو يجيب بيانات (زي إنشاء تذكرة، أو فحص المخزون، أو حجز موعد).

### 🇬🇧 English

#### a) Goal
Defines the purpose of the conversation — the objective the agent must achieve.
> Example: Booking a medical appointment, or summarizing a legal contract in plain language.

#### b) Instructions
Define the steps the agent must follow to reach the goal.
> Example:
> 1. Ask the user if they have a preferred date.
> 2. If yes, suggest an available time slot.
> 3. If not, propose three random options.

#### c) Guide with Examples
Examples are essential — they show the LLM how to react in concrete situations:
- ✅ **Success**: the expected ideal answer
- ❌ **Failure**: what to say when the AI doesn't know the answer
- 🚫 **Out of scope**: how to politely decline an off-topic question

#### d) Tools
Several tools can extend the agent's capabilities:
- **Datastore**: an automatically managed vector database that lets you add business knowledge and perform contextual searches using **RAG** (Retrieval-Augmented Generation).
- **OpenAPI Tools**: connect the agent to third-party APIs to perform actions or fetch external data (e.g., create a ticket, check inventory, book an appointment).

---

## 5️⃣ "فرّق تسد" — استخدام أكتر من Playbook

### 🇪🇬 بالعربي
واحدة من أهم مميزات الوكلاء الحديثة إنها تقدر **تنظّم أكتر من نموذج متخصص**، كل واحد ليه مهمة محددة، بدل ما تحط كل حاجة في Playbook واحد.

**مثال: مساعد محادثي متخصص في الأفلام**

| Playbook | الوظيفة | مصدر البيانات |
|---|---|---|
| **موسوعة الأفلام** | يجاوب على أسئلة معلوماتية زي "مين بطل فيلم كذا؟" أو "مين مخرج The Godfather؟" | قاعدة بيانات ثابتة |
| **أفلام السينما الحالية** | يسأل عن تفضيلات المستخدم (مزاج، نوع، مكان، وقت) ويقترح عروض قريبة | API بيانات لحظية (real-time) |

**🎯 ليه الفصل مهم؟**
لأنه بيمنع الالتباس، زي:
- الوكيل يرد على سؤال "أفلام Brad Pitt؟" بقائمة عروض سينما حالية بالغلط
- أو يقترح فيلم من 1985 لحد بيدور على عرض سينمائي الليلة

بالإضافة لكده، الفصل بيسهّل الصيانة ويقلل احتمالية الردود الخارجة عن الموضوع.

**ملاحظة مهمة:** لو المحادثة محتاجة تسلسل دقيق وواضح (زي عملية دفع)، لسه ممكن تستخدم النهج الحتمي بتاع Dialogflow CX عن طريق **Flow**، وبعدين ترجع لـ Playbook مرن تاني بعد ما العملية تخلص.

### 🇬🇧 English
One of the major strengths of modern conversational agents is their ability to **orchestrate several specialized models**, each dedicated to a specific task, instead of cramming everything into one Playbook.

**Example: A movie-focused conversational assistant**

| Playbook | Function | Data source |
|---|---|---|
| **Movie encyclopedia** | Answers factual questions like "Who directed The Godfather?" | Static database |
| **Movies currently in theaters** | Asks about user preferences (mood, genre, location, time) and suggests nearby screenings | Real-time API data |

**🎯 Why separate them?**
It avoids confusion, such as:
- The agent answering "Which movies with Brad Pitt?" with a list of current screenings
- Or suggesting a 1985 film to someone looking for tonight's showtime

This separation also simplifies maintenance and reduces the risk of off-topic answers.

**Important note:** When interactions require a precise, unambiguous sequence (e.g., a payment process), it's still possible to switch to Dialogflow CX's deterministic **Flow**, then return to a flexible Playbook once the process is complete.

---

## 6️⃣ Dialogflow CX — إزاي بيشتغل تقنيًا؟

### 🇪🇬 بالعربي
الوكيل المبني بـ **Dialogflow CX** بيعتمد على **بنية تدفقية (flow architecture)** ممثّلة كـ**رسم بياني (graph)**، كل عقدة (node) فيه اسمها **Driver**.

**خطوات تفاعل نموذجية:**

1. **رسالة المستخدم** — مثال: "عايز أشتري منتج"
2. **اكتشاف النية (Intent Detection)** — النظام بيستخدم تقنية **NLP** (معالجة اللغة الطبيعية) لتحديد النية (هنا: نية شراء)، عن طريق حساب التشابه مع مجموعة "جمل تدريب" (training phrases) محددة مسبقًا لهذه النية.
3. **رد مُعد مسبقًا** — يتفعّل رد مناسب تلقائيًا، مثلاً: "تمام، عايز تشتري إيه؟"
4. **الانتقال (Transition)** — حسب النية المكتشفة، ممكن المستخدم ينتقل لـ Driver تاني أو صفحة مخصصة لعملية الشراء.

**التحدي:** لكل نوع طلب (زي "إيه مكونات المنتج؟" أو "السعر كام؟")، **لازم تحدد سيناريو منفصل يدويًا**. ورغم إن جوجل أضافت دعم الذكاء الاصطناعي التوليدي لـ Dialogflow CX من 2023، إلا إن الأداة لسه مصممة أساسًا لبناء وكلاء حتميين ببنية منطقية صريحة ومحكومة — وده بيختلف جوهريًا عن Conversational Agent اللي بيعتمد على Playbooks تغطي سيناريوهات أوسع بكتير من غير ما تعرّف كل واحد بنفسك.

### 🇬🇧 English
An agent built with **Dialogflow CX** is based on a **flow architecture**, represented as a graph. Each node is called a **Driver**.

**Typical interaction steps:**

1. **User message** — e.g., "I want to buy a product"
2. **Intent detection** — the system uses **NLP** to identify the intent (here: purchase intent), based on similarity calculation with a predefined set of *training phrases*.
3. **Configured response** — a corresponding response is automatically triggered, e.g., "Great, which products would you like to buy?"
4. **Transition** — depending on the detected intent, the user can be redirected to another Driver or a page dedicated to the purchase.

**The challenge:** For each type of request (e.g., "What are the allergens?" or "What's the price?"), **a separate scenario must be manually defined**. Although generative AI has been integrable into Dialogflow CX since 2023, the tool is still primarily designed for modeling deterministic agents with explicit, controlled logic — which clearly differs from Conversational Agent's Playbooks, which cover much broader scenarios without defining each one individually.

---

## 7️⃣ الخلاصة (Conclusion)

### 🇪🇬 بالعربي
**Conversational Agent** بيستفيد من مرونة الـ LLMs عشان يبني وكلاء "بدون كود" (no-code) قادرين على توليد ردود أكتر انفتاحًا وسياقية. الأداة بتقدم نهج هجين قوي بيجمع بين مرونة الـ Playbooks ودقة الـ Flows، وده بيخليها تتأقلم مع مجموعة واسعة من الاستخدامات — من الإجراءات الصارمة لحد المحادثات الحرة.

لكن في نفس الوقت، فيه أدوات تانية زي **LangChain** و **OpenAI Agents SDK** و **Google Agent Development Kit (ADK)** بتدي للمهندسين تحكم كامل في بناء وكلاء مخصصة بالكود. الاختيار بين المنصات الجاهزة (زي Conversational Agent) والأطر البرمجية بيعتمد على التوازن المطلوب بين سرعة التنفيذ وعمق التخصيص.

### 🇬🇧 English
**Conversational Agent** leverages the flexibility of LLMs to design no-code agents capable of generating more open and contextual responses. It provides a powerful hybrid approach combining the flexibility of Playbooks with the rigor of Flows, adapting to a wide range of use cases — from rigid procedures to free-flowing conversations.

At the same time, frameworks like **LangChain**, the **OpenAI Agents SDK**, and Google's **Agent Development Kit (ADK)** give engineers full control to build tailor-made agents with code. The choice between integrated platforms and code-based frameworks depends on the balance sought between speed of implementation and depth of customization.

---

## 📖 قاموس المصطلحات / Glossary

| المصطلح / Term | الشرح بالعربي | English Definition |
|---|---|---|
| **Generative AI** | ذكاء اصطناعي توليدي — نوع من الذكاء الاصطناعي قادر على إنشاء محتوى جديد (نص، كود، صورة) بناءً على بيانات تدريب | A type of AI capable of creating new content (text, code, image) based on training data |
| **LLM (Large Language Model)** | نموذج لغوي ضخم مُدرّب على مليارات النصوص، زي ChatGPT و Gemini | A large-scale generative AI model trained on billions of texts (e.g., ChatGPT, Gemini) |
| **NLP (Natural Language Processing)** | معالجة اللغة الطبيعية — مجموعة تقنيات لتحليل وفهم وتوليد النص أو الكلام | A set of techniques used to analyze, understand, and generate text or speech |
| **RAG (Retrieval-Augmented Generation)** | التوليد المعزز بالاسترجاع — طريقة بتجمع بين استرجاع معلومات من قاعدة بيانات وإثراء الطلب المرسل للـ LLM بيها | A method combining retrieval of relevant info from a database with enriching the LLM query with that info |
| **Playbook** | "دفتر تعليمات" بيوجّه سلوك الـ LLM (هدف + تعليمات + أمثلة + أدوات) | An instruction layer configuring an LLM's behavior (goal + instructions + examples + tools) |
| **Flow** | تدفق حتمي في Dialogflow CX ممثل كرسم بياني من الـ Drivers | A deterministic flow in Dialogflow CX represented as a graph of Drivers |
| **Driver** | عقدة (node) في رسم Dialogflow CX البياني | A node in the Dialogflow CX flow graph |
| **Datastore** | قاعدة بيانات متجهة تدار تلقائيًا لدعم البحث السياقي (RAG) | An automatically managed vector database supporting contextual (RAG) search |

---

## 🔗 مصادر إضافية / Additional Resources

- [المقال الأصلي بالإنجليزي / Original Medium article](https://medium.com/google-cloud/conversational-agent-how-google-is-redefining-chatbot-creation-with-generative-ai-6893ed877ffe)
- [النسخة الفرنسية الأصلية / Original French version (sfeir.dev)](https://www.sfeir.dev/ia/conversational-agent-comment-google-redefinit-la-creation-de-chatbots-avec-lia-generative/)

## 🔗 مصادر إضافية / Additional Resources repo code projects example
(https://github.com/Somesh-6711/Grounded-Support-Agent-Dialogflow-CX)

https://github.com/hayo03/Dialogflow-CX-Start-Tutorial

https://github.com/topics/dialogflow-cx

https://github.com/Yash-Kavaiya/awesome-cx-agent-studio

---

