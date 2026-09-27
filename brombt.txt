أنت تعمل على مشروع JARVIS الموجود حاليًا.

نفّذ هذه المرحلة كمرحلة كبيرة ومتكاملة، وليس كمجموعة تغييرات صغيرة منفصلة.

الهدف الرئيسي:

نريد أن ينتقل JARVIS من:

1. امتلاك Knowledge Memory عن Procedural Level Design.


2. القدرة على توليد Room-and-Corridor Dungeon باستخدام مولد BSP مفتوح المصدر.



إلى:

3. فحص الـ Dungeon الذي ولّده.


4. قياس خصائصه بشكل deterministic.


5. تنفيذ تجارب حقيقية بتغيير parameters.


6. مقارنة النتائج.


7. معرفة أي تجربة أفضل وفق معيار واضح.


8. حفظ التجربة والنتيجة في Experience Memory مستقلة عن Knowledge Memory.


9. استخدام التجارب السابقة لاختيار التجربة التالية.


10. تنفيذ أول حلقة Improvement حقيقية قابلة للقياس.



هذه ليست مرحلة "LLM Agent". هذه ليست مرحلة "Self-modifying code". هذه ليست مرحلة "تعديل الخوارزمية نفسها". هذه المرحلة يجب أن تجعل JARVIS يتعلم من تجاربه قبل أن نسمح له لاحقًا بتطوير أو دمج خوارزميات جديدة.

==================================================

1. IMPORTANT CURRENT PROJECT STATE ==================================================



اعتبر أن المشروع يحتوي بالفعل على الأنظمة التالية وتعمل بشكل صحيح:

JARVIS Core

Deterministic Command Router

Mastra

SearXNG

Web/Page Reader

Circle State

Self Tests

Domain Knowledge Memory

Knowledge Store

Knowledge Search / List

Dungeon Generator

Dungeon Generate Workflow

Dungeon API

Browser self-test

Open-source BSP dungeon generator vendored locally


المولد الحالي هو:

halftheopposite/bsp-dungeon-generator

وهو موجود داخل:

src/mastra/dungeon/vendor/

المولد لا يجب تغييره في هذه المرحلة.

الخدمة الحالية هي تقريبًا:

src/mastra/dungeon/dungeon-service.ts

والأنواع الحالية:

src/mastra/dungeon/types.ts

والـ room templates:

src/mastra/dungeon/room-templates.ts

والـ workflow الحالي:

src/mastra/workflows/jarvis-dungeon.ts

كما توجد Knowledge Memory مستقلة بالفعل.

مهم جدًا:

لا تعيد بناء الأنظمة الحالية. لا تستبدل Mastra. لا تستبدل SearXNG. لا تستبدل Knowledge Store. لا تنقل generator إلى مكان آخر. لا تدخل LLM أو Agent Framework جديد. لا تضف Vector DB. لا تضف Embeddings. لا تضف RAG. لا تضف Python runtime للمشروع الأساسي. لا تضف Godot أو 3D renderer. لا تضف واجهة معقدة.

نريد إضافة طبقة Dungeon Lab فوق الموجود.

================================================== 2. ARCHITECTURAL PRINCIPLE

يجب الحفاظ على الفرق الواضح بين:

Knowledge = معلومات حصل عليها JARVIS من مصادر.

Experience = شيء جرّبه JARVIS بنفسه ونتيجة التجربة.

Technique = قاعدة عملية بدأت تظهر بعد تكرار التجارب بنتائج متشابهة.

لا تخلط Experience مع Knowledge.

ولا تحوّل نتيجة تجربة واحدة إلى "حقيقة".

مثال:

Knowledge: "BSP يقسم المساحة إلى containers ثم ينشئ rooms وcorridors."

Experience: "على seed X، رفع corridorWidth من 2 إلى 3 خفّض عدد الاختناقات ورفع metric محددًا."

Technique candidate: "رفع corridorWidth إلى 3 قد يكون مفيدًا لهذه الفئة من الخرائط."

لكن لا نسجل Technique مثبتة إلا عندما توجد تجارب متعددة تؤيدها.

================================================== 2.5. RESEARCH & DISCOVERY LOOP

البحث جزء من دورة التعلم، لكنه ليس بديلًا عن التجربة.

يجب أن يستخدم JARVIS الأنظمة الموجودة بالفعل:

SearXNG

Web/Page Reader

Knowledge Store


كوسيلة لاكتشاف أفكار وتقنيات ومشاريع وأساليب جديدة يمكن بعد ذلك اختبارها بنفسه.

المبدأ:

PROBLEM → CHECK KNOWLEDGE → IF KNOWLEDGE IS NOT ENOUGH → RESEARCH → EXTRACT CANDIDATE IDEA → STORE SOURCE-BACKED KNOWLEDGE → TURN IDEA INTO EXPERIMENT HYPOTHESIS → EXPERIMENT → EVALUATE → REMEMBER EXPERIENCE

لا تجعل البحث يعمل باستمرار بدون سبب.

لا تجعل JARVIS يبحث عن الإنترنت عند كل تجربة.

يجب تشغيل Research عندما يكون هناك سبب واضح، مثل:

لا توجد معرفة كافية لحل المشكلة.

لا توجد تقنية جديدة متاحة للتجربة.

Improvement Search توقف عن التحسن.

النتائج أصبحت متكررة أو stagnant.

يوجد metric ضعيف لا يعرف JARVIS كيف يحسنه.

JARVIS يحتاج إلى خوارزمية أو technique غير موجودة في Knowledge Memory.

المستخدم يطلب منه البحث عن طريقة أو algorithm.

JARVIS يحتاج إلى توسيع مساحة الحلول قبل بدء تجربة جديدة.



---

RESEARCH ROLE

البحث يجب أن يجيب عن أسئلة مثل:

"هل توجد طرق أخرى لتوليد Room-and-Corridor Dungeons؟"

"كيف يعالج مطورون آخرون dead ends؟"

"ما الطرق البديلة لـ BSP؟"

"هل توجد Graph-based dungeon generation techniques؟"

"كيف يتم تقييم procedural dungeons؟"

"هل توجد مشاريع مفتوحة المصدر يمكن تجربتها لاحقًا؟"

البحث ليس الهدف النهائي.

الهدف من البحث هو:

DISCOVER → UNDERSTAND → STORE → TEST


---

RESEARCH SOURCES

عند البحث عن تقنيات Procedural Level Design، أعطِ أولوية للمصادر المفيدة فعليًا للتجربة:

GitHub repositories

Official documentation

Research papers

Technical articles

Open-source implementations

Benchmark projects


يجب الاحتفاظ بمصدر كل معلومة تم حفظها.

استخدم source structure الموجودة في Knowledge Memory.

مثال:

{ title: "...", url: "...", author: "..." }

ولا تحفظ نتيجة بحث على أنها معرفة داخلية بدون مصدر.


---

RESEARCH RESULT MODEL

أنشئ type مناسب لنتيجة البحث أو استخدم type موجودًا إذا كان architecture الحالي يسمح.

يجب أن تحتوي نتيجة البحث على الأقل على:

query

sources

discoveredIdeas

relevantProjects

relatedTopics

searchedAt

researchVersion


كل discoveredIdea يجب أن تعرف مصدرها.

مثال منطقي:

{ title: "Graph-based Dungeon Generation", sourceId: "...", sourceTitle: "...", sourceUrl: "...", description: "...", relevance: "..." }

لا تستخدم score غامضًا لتحديد أن الفكرة "أفضل".

يمكن استخدام relevance لترتيب النتائج فقط.


---

RESEARCH TO KNOWLEDGE

إذا اكتشف JARVIS فكرة مفيدة من البحث، يجب أن يستطيع تحويلها إلى Knowledge Record مع:

type

title

content

domain

topic

source

sourceType

confidence

tags


لكن confidence يجب أن يعبّر عن مصدر المعلومة، وليس نجاحها العملي.

مثال:

فكرة موجودة في ورقة أو مشروع مفتوح المصدر قد تدخل Knowledge بثقة مناسبة كمعلومة.

لكن هذا لا يعني أنها Technique ناجحة في JARVIS.

نجاحها العملي يجب أن يأتي لاحقًا من Experience.


---

RESEARCH TO EXPERIMENT

هذه أهم نقطة في Research Loop.

يجب أن يستطيع JARVIS أخذ فكرة مكتشفة وتحويلها إلى Hypothesis قابلة للتجربة.

مثال:

Research: "Graph-based generation can construct room connectivity before geometry."

JARVIS لا يقول:

"هذه الطريقة أفضل."

بل يقول:

"هذه فرضية يمكن اختبارها."

ثم:

Research Idea → Experiment Hypothesis → Experiment Plan

مثال:

{ hypothesis: "A graph-first layout may improve connectivity compared with the current BSP baseline.", source: "...", experimentType: "COMPARATIVE_TECHNIQUE_TEST" }

لا يعتبر البحث دليل نجاح.


---

RESEARCH VS EXPERIENCE

يجب أن يبقى الفرق واضحًا:

Research: "مصدر خارجي يقول إن Graph-based generation يمكن أن يحسن connectivity."

Experience: "JARVIS طبّق Graph-based generation على 10 seeds وقاس connectivity."

Technique: "نتائج JARVIS المتكررة تشير إلى أن الطريقة مفيدة لهذا النوع من الخرائط."

لا تسمح بانتقال:

Research → Technique

مباشرة.

لا بد من Experience.


---

RESEARCH WHEN IMPROVEMENT STAGNATES

أضف منطقًا بسيطًا:

إذا نفذ Improvement Search عددًا معينًا من الجولات بدون تحسن meaningful improvement،

يمكن أن يقرر Planner:

RESEARCH_REQUIRED

ثم يبدأ Research Loop.

مثال:

Round 1 → improvement Round 2 → improvement Round 3 → no improvement Round 4 → no unexplored valid mutations

بدل أن يستمر في تغيير نفس parameters بشكل عشوائي:

JARVIS → Research → discover new technique → create candidate experiment → test new idea

هذا مهم جدًا لأنه يمنع JARVIS من البقاء محصورًا داخل مساحة parameter الحالية إلى الأبد.


---

RESEARCH BOUNDARIES

البحث لا يسمح لـ JARVIS تلقائيًا بتشغيل كود غير موثوق من الإنترنت.

لا تنفذ repositories خارجية مباشرة.

لا تنزل dependency عشوائية وتربطها تلقائيًا بالنظام.

لا تغير vendor generator بسبب نتيجة بحث.

أي مشروع مفتوح المصدر يتم اكتشافه يجب أن يمر أولًا بمرحلة:

DISCOVERY → INSPECTION → COMPATIBILITY CHECK → LICENSE CHECK → EXPERIMENT PLAN

ثم يمكن في مرحلة لاحقة دمجه أو vendoring إذا كان ذلك مناسبًا.


---

RESEARCH MEMORY

لا تنشئ نظام ذاكرة رابع غير ضروري.

استخدم Knowledge Memory للمعلومة الخارجية.

واستخدم Experience Memory لنتائج التجارب.

إذا احتجت metadata إضافية عن البحث، خزّنها بطريقة منفصلة وواضحة أو اربطها بـ Knowledge Records.

يجب أن يكون بالإمكان معرفة:

هذا الشيء:

عرفه JARVIS من مصدر خارجي

أم اكتشفه من تجربة ذاتية



---

RESEARCH COMMANDS

أضف دعمًا للأوامر التالية دون كسر SEARCH الموجود:

"ابحث عن طرق جديدة لتوليد Dungeons"

"research dungeon generation techniques"

"ابحث عن بدائل BSP"

"find open source dungeon generators"

"research how to reduce dead ends"

"ابحث عن طرق لتحسين connectivity"

هذه الأوامر يمكن أن تنتج Research Result.

أما:

"ابحث عن أسعار الذهب"

فيجب أن يبقى SEARCH كما هو.

ولا تجعل كلمة "Dungeon" وحدها Research command.


---

RESEARCH INTENT

إذا احتاج Router إلى intent جديدة، استخدم اسمًا واضحًا مثل:

DUNGEON_RESEARCH

لكن لا تجعل كل SEARCH متعلقًا بـ Dungeon يتحول إلى DUNGEON_RESEARCH.

يجب أن يكون هناك target واضح أو طلب بحث واضح.

مثال:

"search for dungeon generation algorithms" → DUNGEON_RESEARCH

بينما:

"search the web for today's weather" → SEARCH


---

RESEARCH WORKFLOW

أضف workflow مناسبًا مثل:

src/mastra/workflows/jarvis-dungeon-research.ts

ويكون المسؤول عن:

validate-research-request → search → collect-results → read-relevant-pages → normalize-sources → store-knowledge → prepare-discovery-result → respond

لا تجعل workflow يحتوي business logic ضخمًا.

استخدم services منفصلة إذا كان ذلك مناسبًا.


---

RESEARCH SERVICE

أنشئ abstraction مناسبًا، مثل:

DungeonResearchService

ويجب أن يوفر تقريبًا:

research()

getResearchResult()

saveResearchKnowledge()

findCandidateTechniques()

prepareExperimentHypothesis()

لكن أعد استخدام خدمات البحث والـ Knowledge الحالية بدل تكرارها.


---

RESEARCH + EXPERIMENT PLANNER

ExperimentPlanner يجب أن يستطيع معرفة:

ما التقنيات المعروفة؟

ما التقنيات التي تم اختبارها؟

ما التقنيات التي فشلت؟

ما الذي لم يُختبر؟

هل توجد فكرة جديدة من Research يمكن تجربتها؟

هل الـ current search space أصبح stagnant؟


ثم يستطيع إعطاء أولوية مناسبة.

مثال:

Known: BSP

Tested: corridorWidth 1-4

Tested: iterations 4-8

No improvement.

Research found: Graph-based layout.

Planner: "هناك Technique Candidate جديدة لم تُختبر."

في هذه الحالة لا يكرر:

corridorWidth = 5 corridorWidth = 6 corridorWidth = 7

إذا كانت هذه التغييرات خارج الحدود أو غير مفيدة.

بل يحفظ الفكرة ويخطط لتجربتها بالطريقة المناسبة في مرحلة تدعم عدة generators.

مهم:

في هذه المرحلة لا يجب إدخال Graph Generator كامل إذا لم يكن موجودًا.

يمكن أن تكون النتيجة:

NEW_TECHNIQUE_DISCOVERED

وتحفظ كمرشح للتكامل والتجربة المستقبلية.

لا تدّعي نجاح التقنية قبل تنفيذها.


---

RESEARCH FAILURE HANDLING

إذا فشل البحث:

SearXNG unavailable

Page Reader failed

page unavailable

source inaccessible

no relevant result


لا تعتبر البحث ناجحًا.

احفظ الفشل فقط إذا كان مفيدًا للتشخيص.

لا تنشئ Knowledge من نتائج غير موثوقة أو snippets غير كافية.


---

RESEARCH DETERMINISM

البحث نفسه ليس deterministic بطبيعته لأن الإنترنت يتغير.

لذلك لا تطلب أن:

same query → same web results

لكن يجب أن يكون:

نفس Research Result المحفوظ → نفس knowledge references → نفس experiment planning context

أي أن التجربة يجب أن تعتمد على snapshot محفوظة من البحث، وليس إعادة بحث غير متحكم فيها أثناء تنفيذ experiment.


---

RESEARCH PROVENANCE

أي فكرة دخلت إلى Experiment Planner من البحث يجب أن تعرف:

source

sourceType

url

discoveredAt

researchId


حتى نستطيع لاحقًا معرفة:

"لماذا قرر JARVIS تجربة هذا؟"

والجواب يجب أن يكون قابلًا للتتبع.

مثال:

Experiment → hypothesis → researchId → source → URL


---

IMPORTANT RESEARCH PRINCIPLE

لا تجعل الإنترنت عقل JARVIS.

الإنترنت يوسّع مساحة الأفكار.

JARVIS نفسه يجب أن:

يبحث → يختار فكرة قابلة للاختبار → يجربها → يقيسها → يحفظ النتيجة → يبني خبرة

الهدف النهائي هو:

RESEARCH → HYPOTHESIS → EXPERIMENT → EXPERIENCE → TECHNIQUE

وليس:

RESEARCH → COPY → CLAIM SUCCESS

================================================== 3. CREATE DUNGEON LAB MODULE

أنشئ module مستقل:

src/mastra/dungeon-lab/

ويفضل أن تكون البنية تقريبًا:

src/mastra/dungeon-lab/ types.ts evaluator.ts evaluation-profile.ts experiment-planner.ts experiment-runner.ts comparator.ts improvement-service.ts experience-store.ts technique-detector.ts lab-service.ts

الأسماء قابلة للتعديل إذا كان architecture الحالي يفرض أسماء أفضل، لكن يجب الحفاظ على الفصل الواضح بين المسؤوليات.

لا تجعل DungeonService نفسه مسؤولًا عن evaluation أو experiments.

DungeonService: "generate"

DungeonLab: "inspect / evaluate / experiment / compare / improve / learn"

================================================== 4. DOMAIN TYPES

أنشئ types واضحة.

يجب أن يكون لدينا على الأقل:

DungeonEvaluation

ويحتوي على:

dungeonId أو generationId

generatedSeed

config

structuralChecks

metrics

score

scoreProfile

warnings

evaluatedAt

evaluatorVersion


مثال منطقي:

structuralChecks:

allWithinBounds

floorIsConnected

noInvalidRooms

noInvalidCorridors

validDimensions


لكن لا تفرض أسماء أو checks غير ممكنة.

قبل تنفيذ evaluator: افحص شكل الـ normalized dungeon الحقيقي الموجود حاليًا في المشروع والمولد vendor.

لا تفترض شكل tiles أو rooms أو corridors.

استخرج من الكود الحالي الطريقة الصحيحة لمعرفة:

floor

wall

room

corridor

room bounds

room type

corridor representation


إذا كان metric غير ممكن حسابه بشكل موثوق: لا تخترع قيمة.

إما:

لا تحسبه

أو ارجعه unavailable مع سبب واضح.


================================================== 5. EVALUATOR

أنشئ DungeonEvaluator deterministic بالكامل.

نفس Dungeon + نفس config + نفس evaluator version يجب أن يعطي نفس النتيجة.

لا تستخدم random داخله.

لا تستخدم LLM.

لا تستخدم heuristics غامضة بدون تفسير.

نريد فصل:

A) Raw Metrics B) Structural Validation C) Optional Fitness Score


---

A. RAW METRICS

ابدأ بالمقاييس التي يمكن استخراجها بثقة من الـ dungeon الحالي.

على الأقل حاول توفير:

mapWidth

mapHeight

totalTiles

walkableTiles

wallTiles

walkableRatio

roomCount

corridorCount

roomsByType

connectedComponents

largestComponentRatio

outOfBoundsRoomCount

outOfBoundsCorridorCount

overlappingRoomCount إذا كان ممكنًا حسابه

roomAreaTotal

roomAreaRatio

corridor coverage إذا كان ممكنًا حسابه

deadEnd metric إذا كان تعريفه موثوقًا

branch metric إذا كان تعريفه موثوقًا

special room distances إذا كانت room types المطلوبة موجودة

minimum/maximum/average room sizes

minimum/maximum/average corridor lengths إذا كانت البيانات تسمح بذلك


لا تضف metrics فقط لزيادة العدد.

كل metric يجب أن يكون:

deterministic

قابلًا للاختبار

واضح التعريف



---

B. STRUCTURAL CHECKS

أنشئ checks منفصلة عن score.

أمثلة:

valid dimensions all generated content inside bounds at least one room at least one walkable tile single connected walkable component no impossible geometry no malformed room no malformed corridor

إذا كان check لا ينطبق: أرجع status واضح مثل:

"not_applicable"

ولا تعتبره pass.


---

C. FITNESS / QUALITY SCORE

أنشئ:

src/mastra/dungeon-lab/evaluation-profile.ts

بحيث توجد profile قابلة للتعديل.

مثلًا:

default-room-corridor-profile

الـ score يجب أن يكون transparent.

لا تريد رقمًا سحريًا لا يعرف JARVIS كيف وصل إليه.

يجب أن يعرف:

كل metric

normalized value

weight

contribution

final score


مثال منطقي:

{ metric: "largestComponentRatio", value: 1, normalized: 1, weight: 0.35, contribution: 0.35 }

لا تستخدم كلمة "beautiful".

لا تستخدم "good dungeon" بدون تعريف.

الـ score هو fitness حسب profile محددة، وليس حكمًا مطلقًا على جودة التصميم.

مهم جدًا:

يجب أن تكون structural failures قادرة على خفض score بشدة أو جعل النتيجة invalid.

مثال: Dungeon يحتوي على عدة مكونات منفصلة لا يجب أن ينافس Dungeon متصلًا بالكامل فقط لأن لديه غرفًا أكثر.

================================================== 6. COMPARATOR

أنشئ Comparator مستقل.

مسؤوليته:

مقارنة DungeonEvaluation مع أخرى أو أكثر.

يجب أن يعيد:

baseline

candidates

metric deltas

score delta

structural differences

winner/selectedCandidate بحسب الـ fitness profile

explanation


لكن explanation يجب أن تكون deterministic.

مثال:

"Candidate B increased largestComponentRatio from 0.93 to 1.00 and reduced deadEndRatio from 0.18 to 0.11. Final score increased by 0.074."

لا تستخدم LLM لإنشاء هذا النص.

يجب أيضًا توفير حالة:

no meaningful improvement

بحيث لا يجبر النظام نفسه على اختيار candidate أسوأ.

مهم:

إذا لم يتحسن أي candidate عن baseline: الـ baseline يبقى champion.

================================================== 7. EXPERIMENT MODEL

أنشئ نموذج Experiment مستقل.

يجب أن يحتوي على الأقل:

id

type

domain

baselineConfig

baselineSeed

candidates

hypothesis

controlledVariables

changedVariables

results

comparison

selectedCandidate

conclusion

createdAt

completedAt

experimentVersion


أنواع التجارب الأولية:

1. PARAMETER_SWEEP


2. SINGLE_PARAMETER_CHANGE


3. ROBUSTNESS_TEST


4. IMPROVEMENT_SEARCH




---

SINGLE_PARAMETER_CHANGE

مثال:

baseline: corridorWidth = 2

candidate: corridorWidth = 3

نفس seed.

هذا مهم لأنه يعزل تأثير parameter قدر الإمكان.


---

PARAMETER_SWEEP

مثال:

corridorWidth:

1 2 3 4

نفس seed.

ثم evaluate لكل نتيجة.


---

ROBUSTNESS_TEST

بعد أن يظهر candidate أفضل مع seed واحد، لا تعتبره "متعلمًا" مباشرة.

اختبره على عدة seeds.

مثلًا:

seed A seed B seed C

مع نفس config.

والهدف معرفة:

هل التحسن يتكرر؟ أم كان مجرد نتيجة seed معين؟


---

IMPROVEMENT_SEARCH

هذه هي أول حلقة Improvement.

ابدأ من baseline.

ولّد مجموعة candidates ضمن تغييرات آمنة.

مثال mutation rules:

corridorWidth: ±1

iterations: ±1 أو ±2

containerSplitRetries: ±1

containerMinimumRatio: تغيير صغير ضمن الحدود الحالية

containerMinimumSize: تغيير صغير ضمن الحدود الحالية

mapGutterWidth: ±1

لا تغيّر mapWidth/mapHeight في أول نسخة من improvement search إلا إذا كانت هناك حاجة واضحة.

لا تغيّر seed أثناء المقارنة الأساسية.

================================================== 8. EXPERIMENT PLANNER

أنشئ:

ExperimentPlanner

وظيفته:

اختيار ماذا يجرب JARVIS بعد ذلك.

لا نستخدم LLM.

نريد أول نظام قرار deterministic.

مثال:

إذا كان: corridorWidth +1 أعطى تحسنًا متكررًا

يمكنه تجربة: corridorWidth +1 مرة أخرى ضمن الحدود.

إذا: corridorWidth +1 فشل عدة مرات

يخفض أولوية هذا التغيير.

إذا parameter لم يتم اختباره: يُعطى أولوية للاستكشاف.

إذا parameter لديه نتائج متناقضة: يُعطى أولوية لـ robustness testing.

يعني planner يجب أن يوازن بين:

exploration و exploitation

لكن بدون بناء نظام Reinforcement Learning كامل.

نريد فقط أول نسخة عملية وبسيطة.

================================================== 9. IMPROVEMENT LOOP

أنشئ:

DungeonImprovementService

المدخلات:

base config

base seed

objective profile

max rounds

candidates per round


مثال:

maxRounds = 3 candidatesPerRound = 6

الخوارزمية:

1. Generate baseline.


2. Evaluate baseline.


3. Ask ExperimentPlanner for candidate configs.


4. Generate candidates.


5. Evaluate candidates.


6. Compare all against current champion.


7. Keep best candidate فقط إذا كان تحسنًا حقيقيًا.


8. Save experiment.


9. Use experience from previous round.


10. Generate next candidate set.


11. Repeat.


12. Stop when:



max rounds reached

no improvement

no unexplored valid mutations


13. Run robustness validation on final champion using several seeds.


14. Return:



original config

final config

all rounds

experiments

final metrics

robustness result

explanation


مهم جدًا:

لا ترجع "improved" فقط لأن هناك candidate جديد.

يجب أن يكون لدينا دليل رقمي على التحسن.

================================================== 10. EXPERIENCE MEMORY

أنشئ Experience Store مستقلًا.

لا تستخدم KnowledgeStore لهذا.

الملف الافتراضي:

.jarvis/experience.json

ومتغير بيئي:

JARVIS_EXPERIENCE_PATH

استخدم نفس مبدأ التخزين الحالي:

lazy load

atomic writes

deterministic ordering

persistence

seed data غير مطلوبة


إذا كان architecture الموجود لديه abstraction أفضل، استخدمه.

يجب ألا يعتمد ExperienceStore مباشرة على UI.


---

EXPERIENCE RECORD

يجب أن يحتوي على:

id

type

domain

title

hypothesis

context

baseline

candidates

selectedResult

metrics

conclusion

confidence

supportingExperimentIds

parentExperienceIds

createdAt

updatedAt


الأنواع:

EXPERIMENT OBSERVATION COMPARISON IMPROVEMENT TECHNIQUE_CANDIDATE

================================================== 11. EXPERIENCE SEARCH

أضف البحث في Experience Memory.

مثل:

"ماذا جرب JARVIS في corridorWidth؟"

"ما نتائج تجارب BSP؟"

"ما الذي نجح مع corridor width 3؟"

"اعرض آخر التجارب"

"list experiments"

"search experience for corridorWidth"

في البداية يمكن أن يكون البحث deterministic مثل Knowledge Search.

لا تضف embeddings.

استخدم:

exact field matches

parameter names

tags

title

conclusion

hypothesis


ويجب أن يكون الترتيب deterministic.

================================================== 12. TECHNIQUE DETECTOR

لا تجعل تجربة واحدة Technique.

أنشئ TechniqueDetector بسيطًا.

مثال:

إذا تكرر:

corridorWidth +1

وكان أثره إيجابيًا في عدة تجارب مستقلة وعلى أكثر من seed،

يمكن إنشاء:

TECHNIQUE_CANDIDATE

لكن يجب أن يحتوي على evidence.

مثال:

{ technique: "Increase corridorWidth from 2 to 3 for this dungeon profile", supportCount: 4, seedCount: 3, experimentIds: [...], confidence: "MEDIUM" }

ممنوع تحويلها تلقائيًا إلى KnowledgeRecord.

في هذه المرحلة تبقى داخل Experience Memory.

لاحقًا سنبني مرحلة promotion من Experience إلى Knowledge.

================================================== 13. CONFIDENCE

أنشئ confidence بسيطًا ومفهومًا.

مثلًا:

LOW: نتيجة تجربة واحدة.

MEDIUM: نفس الاتجاه ظهر في عدة تجارب مستقلة.

HIGH: النتيجة تكررت عبر عدة seeds وتجارب مستقلة بدون تعارض مهم.

لا تدّعي statistical significance.

لا تستخدم مصطلحات علمية أكبر من البيانات المتوفرة.

================================================== 14. LAB SERVICE

أنشئ:

DungeonLabService

ويكون هو facade الرئيسي.

يجب أن يوفر تقريبًا:

generateAndEvaluate()

evaluateDungeon()

runExperiment()

compareEvaluations()

runImprovement()

searchExperiences()

listExperiences()

getExperience()

getTechniqueCandidates()

لا تجعل UI يستدعي evaluator مباشرة.

المسار:

Command Router → Core / API → Mastra Workflow → DungeonLabService → DungeonService + Evaluator + ExperimentRunner + ExperienceStore

================================================== 15. MAStra WORKFLOWS

أضف workflow جديدًا مثل:

src/mastra/workflows/jarvis-dungeon-lab.ts

ويجب أن تكون الخطوات واضحة.

مثال:

validate-lab-request → prepare-experiment → generate → evaluate → compare → save-experience → respond

ولعمليات improvement:

validate-improvement-request → generate-baseline → evaluate-baseline → plan-candidates → run-candidates → evaluate-candidates → compare → save-experience → plan-next-round → repeat → robustness-validation → save-final-experience → respond

لا تجعل workflow يحتوي business logic ضخمًا.

المنطق الحقيقي يجب أن يكون في services.

================================================== 16. COMMAND ROUTER

أضف intents جديدة دون كسر intents الحالية.

مثال:

DUNGEON_EVALUATE DUNGEON_EXPERIMENT DUNGEON_COMPARE DUNGEON_IMPROVE EXPERIENCE_SEARCH EXPERIENCE_LIST

أمثلة يجب دعمها:

"قيّم هذا Dungeon"

"evaluate dungeon"

"جرّب corridor width 3"

"experiment with corridorWidth"

"قارن بين هذين Dungeon"

"compare dungeon results"

"حسّن هذا Dungeon"

"improve dungeon"

"جرّب عدة إعدادات وحاول إيجاد الأفضل"

"run dungeon experiments"

"ماذا تعلم JARVIS من تجاربه؟"

"show experiences"

"اعرض التجارب"

"ابحث في تجارب JARVIS عن corridorWidth"

مهم:

لا تكسر:

SEARCH KNOWLEDGE_SEARCH KNOWLEDGE_LIST DUNGEON_GENERATE

مثال:

"ابحث عن dungeon generation algorithms"

يجب أن يبقى SEARCH.

"generate a dungeon"

يجب أن يبقى DUNGEON_GENERATE.

"evaluate the generated dungeon"

يجب أن يصبح DUNGEON_EVALUATE.

================================================== 17. API

أضف endpoints واضحة، مع الحفاظ على الحالية.

مقترح:

POST /api/jarvis/dungeon/evaluate

POST /api/jarvis/dungeon/experiment

POST /api/jarvis/dungeon/compare

POST /api/jarvis/dungeon/improve

GET /api/jarvis/experience

GET /api/jarvis/experience/search

إذا كان نمط الـ API الحالي مختلفًا، اتبع النمط الحالي بدل اختراع style جديد.

لا تلمس endpoints الحالية إلا لإعادة الاستخدام عند الحاجة.

================================================== 18. CIRCLE STATE

أضف حالات مناسبة مثل:

DUNGEON_EVALUATING DUNGEON_EXPERIMENTING DUNGEON_COMPARING DUNGEON_LEARNING DUNGEON_IMPROVING

المسؤول عن تحديث Circle State هو application layer كما هو متبع حاليًا.

لا تجعل workflow يتعامل مع UI مباشرة.

================================================== 19. UI

لا تنشئ Dashboard كبيرة.

نريد فقط أن يستطيع المستخدم رؤية نتيجة العملية الحالية بوضوح.

عند evaluation:

room count

corridor count

connectivity

score

أهم المشاكل


عند experiment:

baseline

عدد candidates

metric changes

selected candidate

score delta


عند improvement:

baseline score

rounds

final score

changed parameters

robustness result

conclusion


يمكن عرض البيانات كنص منظم في الـ HUD الحالي.

لا تضف مكتبة UI جديدة.

================================================== 20. DETERMINISM

هذا جزء أساسي.

يجب أن ينجح:

same config + same seed → same dungeon

وبالتالي:

same dungeon → same evaluation

ونفس experiment inputs → نفس comparison ordering

ونفس stored experience → نفس search order

لا تستخدم:

Date.now() randomness non-deterministic iteration

في أي جزء من الحساب نفسه.

التواريخ يمكن استخدامها كmetadata فقط.

================================================== 21. EXPERIMENT ISOLATION

يجب فصل نوعين من التجارب:

A) Controlled experiment

نفس seed تغيير parameter واحد أو مجموعة محددة.

الهدف: عزل السبب.

B) Robustness experiment

نفس config عدة seeds.

الهدف: معرفة هل التحسن يتكرر.

لا تخلط الاثنين في نتيجة واحدة.

================================================== 22. IMPROVEMENT POLICY

في أول نسخة:

لا تحاول تعديل source code للمولد.

لا تعدل:

src/mastra/dungeon/vendor/*

ولا تحاول توليد خوارزمية BSP جديدة.

JARVIS يتعلم فقط:

أي config يعمل أفضل ضمن المجال الحالي.

هذه نقطة مهمة.

إذا كانت تجربة improvement انتهت بـ:

corridorWidth = 3

فـ JARVIS تعلم تفضيل parameter/config.

ولم يتعلم خوارزمية Dungeon جديدة.

================================================== 23. FAILURE HANDLING

كل تجربة يجب أن تسجل الفشل أيضًا.

مثال:

candidate config invalid

generation failed

evaluation unavailable

score degraded

no improvement

robustness failed

لا تخفي الفشل.

Experience Memory يجب أن تعرف أيضًا ما الذي لم ينجح.

مثال:

"corridorWidth = 4 produced lower score on 3 tested seeds."

هذه خبرة مهمة.

================================================== 24. NO FAKE LEARNING

ممنوع كتابة رسائل مثل:

"JARVIS learned this"

إلا إذا تم تنفيذ تجربة فعلية.

بدل ذلك استخدم لغة مثل:

"JARVIS observed"

"Experiment result"

"Repeated evidence"

"Candidate technique"

"Improvement validated across 3 seeds"

وهذه الكلمات يجب أن تعكس البيانات فعليًا.

================================================== 25. VERSIONING

أضف version fields إلى:

evaluator

evaluation profile

experiment format

experience format


مثال:

evaluatorVersion: "1.0.0"

السبب: إذا غيرنا evaluator لاحقًا يجب ألا تختلط النتائج القديمة بالجديدة بدون معرفة الاختلاف.

================================================== 26. TESTING

أضف test suite قوية.

يجب اختبار:

Evaluator:

valid dungeon

invalid dungeon

disconnected dungeon

deterministic metrics

bounds

room metrics

corridor metrics


Comparator:

higher score

equal score

no improvement

invalid candidate

deterministic ordering


ExperienceStore:

save

load

update

search

list

persistence

atomic write behavior


ExperimentRunner:

same seed

controlled parameter change

candidate execution

failed candidate

multiple candidates


ImprovementService:

baseline preserved if no improvement

best candidate selected

next round generated

termination

robustness test

experience saved


TechniqueDetector:

single experiment does NOT create validated technique

repeated evidence does

contradictory evidence lowers confidence or blocks promotion


Command Router:

all new commands

ambiguous commands

old commands still work


Integration: UI → client → API → Mastra → DungeonLabService → DungeonGenerator → evaluator → experience store → response

================================================== 27. REAL BROWSER SELF TEST

أضف browser self-tests حقيقية.

يجب اختبار على الأقل:

1. 

"أنشئ Dungeon"

2. 

"قيّم الـ Dungeon"

3. 

"جرّب corridorWidth 3"

4. 

"قارن النتائج"

5. 

"حسّن هذا Dungeon"

6. 

"اعرض التجارب"

7. 

"ابحث في تجارب JARVIS عن corridorWidth"

8. 

"ابحث عن dungeon generation algorithms"

ويجب التأكد أن الأخير يبقى SEARCH.

ويجب التأكد أن:

"dungeon"

وحدها لا تشغل Lab أو Generate إذا كان Router الحالي يتطلب action واضحة.

================================================== 28. IMPORTANT DATA FLOW

يجب أن يكون التدفق:

USER ↓ COMMAND ROUTER ↓ INTENT ↓ MAStra WORKFLOW ↓ DUNGEON LAB SERVICE ↓ DUNGEON SERVICE ↓ GENERATION RESULT ↓ EVALUATOR ↓ COMPARATOR ↓ EXPERIENCE STORE ↓ RESPONSE

وفي improvement:

USER ↓ IMPROVEMENT REQUEST ↓ BASELINE ↓ PLAN ↓ CANDIDATES ↓ GENERATE ↓ EVALUATE ↓ COMPARE ↓ CHOOSE CHAMPION ↓ SAVE EXPERIENCE ↓ NEXT ROUND ↓ ROBUSTNESS ↓ FINAL EXPERIENCE ↓ RESPONSE

================================================== 29. USE EXISTING OPEN-SOURCE WORK WHERE USEFUL

لا تعيد اختراع concepts معروفة إذا كان هناك implementation مناسب يمكن الاستفادة منه.

لكن لا تضف dependency ضخمة فقط من أجل metric بسيط.

يمكن استخدام أفكار من PCG benchmark projects كمراجع تصميمية، خصوصًا:

quality

diversity

controllability

connectivity

dead ends

special room distances


لكن لا تدخل Python benchmark إلى core JARVIS في هذه المرحلة.

المشروع الأساسي يجب أن يبقى:

TypeScript + Bun

ويمكن لاحقًا بناء adapter منفصل لـ external benchmark إذا أصبح ذلك ضروريًا.

================================================== 30. DIVERSITY

أضف support مبدئيًا لـ diversity metrics، لكن لا تجعلها شرطًا لإنهاء المرحلة.

الهدف الأول:

هل dungeon مختلف فعلًا عند تغيير seed؟

يمكن توفير metric بسيط وقابل للتفسير مثل:

tile difference ratio

room layout difference

room count difference


لكن لا تستخدم diversity score إذا لم يكن التعريف موثوقًا.

الأهم الآن: quality + structural validity + experiment reproducibility.

================================================== 31. NO PREMATURE COMPLEXITY

لا تضف:

reinforcement learning framework

neural network

vector database

embedding service

autonomous coding agent

self-rewriting source code

Python service

microservice architecture

Redis

Postgres

cloud dependency


كل ذلك مؤجل.

نريد نظامًا محليًا deterministic يستطيع أن:

generate → evaluate → experiment → compare → remember → improve configuration

================================================== 32. FILE DISCOVERY RULE

قبل كتابة الكود:

اقرأ الملفات الحالية المتعلقة بـ:

dungeon

router

core state

client

Mastra

API

self tests

knowledge store


لا تفترض signatures غير موجودة.

أعد استخدام abstractions الموجودة بدل إنشاء نسخ ثانية منها.

إذا وجدت أن اسمًا أو structure مختلف: اتبع structure الموجود بدل إجبار المشروع على الشكل المقترح هنا.

================================================== 33. MINIMAL BREAKAGE RULE

يجب أن تبقى كل الأنظمة القديمة تعمل:

SEARCH KNOWLEDGE_SEARCH KNOWLEDGE_LIST DUNGEON_GENERATE

يجب ألا تتغير semantics الخاصة بها.

الاختبارات القديمة يجب أن تستمر.

================================================== 34. ACCEPTANCE CRITERIA

لا تعتبر المرحلة مكتملة إلا إذا تحقق الآتي:

1. 

JARVIS يستطيع تقييم Dungeon موجود.

2. 

التقييم deterministic.

3. 

يستطيع إجراء تجربة بتغيير parameter.

4. 

يستطيع مقارنة baseline مع candidates.

5. 

يعرف أن "no improvement" حالة صحيحة.

6. 

يستطيع تشغيل robustness test على عدة seeds.

7. 

يحفظ التجارب في Experience Memory مستقلة.

8. 

يستطيع البحث في Experience Memory.

9. 

يستطيع استخدام التجارب السابقة للمساعدة في اختيار التجربة التالية.

10. 

يستطيع تنفيذ Improvement Search متعددة الجولات.

11. 

الـ improvement لا يعلن النجاح بدون تحسن measurable.

12. 

التجارب الفاشلة محفوظة.

13. 

Technique Candidate لا يظهر من تجربة واحدة.

14. 

كل النتائج deterministic.

15. 

كل tests القديمة ما زالت خضراء.

16. 

Browser self-test يثبت المسار الحقيقي كاملًا.

17. 

لا يوجد تغيير على vendor generator.

18. 

لا يوجد LLM أو Vector DB أو Embeddings أو Python dependency جديدة.

================================================== 35. REQUIRED OUTPUT FROM THE CODING AGENT

بعد التنفيذ أعطني تقريرًا منظمًا يحتوي على:

A. الملفات التي أضيفت.

B. الملفات التي عدلت.

C. شرح architecture الجديد.

D. شرح metrics التي تم تنفيذها فعلًا.

E. شرح experiment types.

F. شرح Experience Memory.

G. شرح كيف يقرر JARVIS ماذا يجرب بعد ذلك.

H. مثال تجربة حقيقية:

baseline config → candidate configs → metrics → comparison → selected candidate → saved experience

I. مثال Improvement Search حقيقي مع عدة rounds.

J. مثال robustness test على عدة seeds.

K. عدد الاختبارات ونتائجها.

L. نتيجة: bun tsc -b --noEmit tsconfig.root.json

M. نتيجة: bun run build

N. نتيجة: bun run verify

O. نتيجة browser self-test.

P. اذكر بوضوح أي شيء لم تستطع تنفيذه ولماذا.

لا تقل "تعلم JARVIS" بشكل عام. أعطني evidence من التجارب والـ metrics.

================================================== 36. FINAL DESIGN GOAL

بعد انتهاء هذه المرحلة، أريد أن يصبح JARVIS قادرًا على القيام بهذا التسلسل:

"أنشئ Dungeon."

→ يولد Dungeon.

"قيّمه."

→ يفحص Dungeon ويعطي metrics.

"جرّب corridorWidth = 3."

→ ينفذ experiment مضبوط.

"قارن."

→ يقارن النتائج.

"جرّب عدة إعدادات وحاول تحسينه."

→ يبدأ Improvement Search.

→ baseline

→ candidates

→ evaluate

→ compare

→ champion

→ next round

→ robustness

→ final result

→ save experience

ثم:

"ماذا تعلمت من هذا؟"

→ لا يجيب من Knowledge Memory.

بل يجيب من Experience Memory بنتائج التجارب الفعلية.

المرحلة المطلوبة الآن ليست جعل JARVIS "ذكيًا" بالكلام.

المرحلة المطلوبة هي جعله يملك أول حلقة حقيقية:

OBSERVE → MEASURE → EXPERIMENT → COMPARE → REMEMBER → TRY AGAIN

وكل هذا يجب أن يكون قابلًا للاختبار وإعادة الإنتاج.

================================================== 37. RESEARCH ACCEPTANCE CRITERIA

إضافة إلى كل ما سبق، لا تعتبر المرحلة مكتملة إلا إذا تم ربط البحث فعليًا بدورة التعلم.

يجب أن ينجح الآتي:

1. 

JARVIS يستطيع إجراء Research مخصص لـ Procedural Level Design وDungeon Generation باستخدام SearXNG وWeb/Page Reader الموجودين.

2. 

نتيجة البحث تحتفظ بالمصادر والـ URLs.

3. 

المعلومة الخارجية يمكن حفظها في Knowledge Memory مع source واضح.

4. 

JARVIS يستطيع التمييز بين:

Research finding

و

Experiment result

5. 

فكرة تم اكتشافها من Research يمكن تحويلها إلى Experiment Hypothesis.

6. 

Experiment Planner يستطيع معرفة أن هناك فكرة جديدة مكتشفة من Research ولم يتم اختبارها بعد.

7. 

إذا أصبح Improvement Search stagnant، يستطيع النظام طلب Research بدل الاستمرار في تغيير parameters بلا نهاية.

8. 

Research لا تعني نجاح التقنية.

يجب أن تكون الدورة:

Research → Hypothesis → Experiment → Evaluation → Experience

9. 

إذا فشل Research فلا يتم إنشاء Knowledge وهمية.

10. 

لا يتم تشغيل code أو repositories عشوائية مباشرة من الإنترنت.

11. 

كل Experiment يعتمد على snapshot محفوظة من Research وليس على إعادة بحث غير متحكم فيها أثناء التجربة.

12. 

يمكن تتبع سبب اختيار Hypothesis من خلال:

Experiment → Research Record → Source

13. 

أضف browser self-tests تثبت:

"ابحث عن طرق بديلة لتوليد Dungeon"

→ DUNGEON_RESEARCH

و:

"ابحث عن dungeon generation algorithms"

يتم التعامل معه كبحث متخصص إذا كان parser الحالي يميز ذلك بشكل واضح.

بينما:

"ابحث عن أسعار الذهب"

يبقى SEARCH.

14. 

أضف integration test يثبت:

Research → Knowledge → Hypothesis → Experiment

حتى لو كانت التقنية المكتشفة لا تزال مجرد candidate ولم يتم تنفيذ generator جديد لها.

15. 

تقرير التنفيذ النهائي يجب أن يذكر:

كم بحثًا حقيقيًا تم تنفيذه.

ما المصادر التي تم العثور عليها.

ما الأفكار الجديدة التي تم اكتشافها.

أي الأفكار تم تحويلها إلى hypotheses.

أي hypotheses تم اختبارها.

وما الذي بقي مجرد Research ولم يتحول إلى Experience.


لا تستخدم عبارة "JARVIS learned" لأي فكرة لم تمر عبر تجربة فعلية.

==================================================

END OF PROMPT

==================================================

