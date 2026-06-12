# من هنا نبدأ — توثيق المشروع

## نظرة عامة

**من هنا نبدأ** هو تطبيق ويب تفاعلي بالكامل (ملف HTML واحد مستقل) مصمم للمبدعين والشباب المسلم المعاصر. يجمع بين التأهيل الفكري والروحي وتقييم المهارات الإبداعية وأدوات التخطيط للعام الجديد. اتجاه النص من اليمين لليسار (RTL) وجميع المحتويات باللغة العربية.

---

## هيكل الملف

```
min_huna_nabda.html          ← الملف الوحيد، يعمل standalone بدون خادم
```

الملف يضم ثلاثة أقسام رئيسية:

1. **`<style>`** — كل قواعد CSS (متغيرات، تخطيط، مكونات، responsive)
2. **`<body>`** — هيكل HTML لجميع الصفحات الست
3. **`<script>`** — منطق JavaScript الكامل (بيانات + وظائف + حالة)

---

## الصفحات الست

التنقل بين الصفحات عبر شريط nav علوي. الصفحة النشطة تحمل class `active`، والباقي `display:none`.

### 1. `page-home` — الصفحة الرئيسية
- **Hero section**: حقل إدخال اسم المبدع — يُطلق تجربته عبر `setCreatorName()`
- **خريطة الرحلة**: ٥ بطاقات قابلة للنقر تنقل للصفحات الأخرى
- الانتقال للمحاضرة تلقائياً بعد إدخال الاسم

### 2. `page-lecture` — المحاضرة
- **مشغّل YouTube**: `iframe` مباشر لمحاضرة الشيخ إبراهيم الطويل (`Qpy6IoYOyZI`)
- **تبويبان**:
  - `tab-poison`: اختبار التسمم بمفاهيم الغرب (٥ أسئلة — لا توجد إجابة خاطئة، المقياس يقيس درجة الوعي)
  - `tab-comprehension`: اختبار فهم محتوى المحاضرة (٥ أسئلة بإجابات موضوعية + تغذية راجعة فورية)

### 3. `page-skills` — بطاقات المهارات الإبداعية
مسار من ٣ خطوات:

**الخطوة ١ — `skills-selector`**: اختيار المهارات  
→ شبكة checkboxes لـ١٧ تخصصاً، مع عداد ديناميكي

**الخطوة ٢ — `skills-quiz-container`**: الاختبارات  
→ يفتح اختباراً مخصصاً (١٠ أسئلة) لكل تخصص مختار  
→ تبويبات علوية تتتبع التقدم وتتحول لـ ✓ بعد إنهاء كل اختبار  
→ أنواع الأسئلة: MCQ، مقياس ١-٥، سيناريو عملي

**الخطوة ٣ — `player-cards-section`**: بطاقات النتائج  
→ بطاقة احترافية لكل مهارة: درجة/١٠٠، مستوى، رادار chart، أشرطة الأبعاد  
→ درجة إجمالية عبر كل المهارات  
→ زر إعادة اختبار لكل مهارة منفردة

### 4. `page-routine` — الروتين اليومي
- **استبيان** (٦ أسئلة): وقت الاستيقاظ، النوم، مجال التركيز، ساعات الإنتاج، الفجر، السوشيال ميديا
- **نتيجة مزدوجة**:
  - `routine-daily`: روتين يومي بأوقات فعلية محسوبة ديناميكياً
  - `routine-quarterly`: خطة ربع سنوية مخصصة حسب مجال التركيز

### 5. `page-tasks` — قائمة المهام
- ١٥ مهمة للبداية مع تصنيف لكل مهمة
- شريط تقدم ديناميكي + عداد المهام المنجزة
- النقر على المهمة يبدّل حالتها (منجزة/غير منجزة)

### 6. `page-planning` — دليل الدفتر والقلم
- ٦ خطوات تفاعلية للتخطيط المثالي للعام على الورق:
  1. مراجعة العام المنصرم
  2. عجلة الحياة (٨ مجالات)
  3. تحديد الأهداف الكبرى
  4. الخطة الشهرية
  5. الروتين اليومي الملوّن
  6. الالتزام والمساءلة

---

## شريط التقدم العلوي

```
[اسم المبدع]  [████░░░░░░░░]  [N / 6 محطات]  [XX%]
```

يتتبع ٦ نقاط تقدم في المتغير `progress`:

| المفتاح | يتفعّل عند |
|---|---|
| `nameSet` | إدخال الاسم |
| `lectureWatched` | زيارة صفحة المحاضرة |
| `poisonDone` | إكمال اختبار التسمم |
| `compDone` | إكمال اختبار الفهم |
| `skillsDone` | إظهار نتائج المهارات |
| `routineDone` | توليد الروتين |

---

## بنية البيانات

### `SKILL_FIELDS` — بيانات اختبارات المهارات
```js
SKILL_FIELDS = {
  [skillId]: {
    label: string,      // اسم المهارة بالعربية
    icon: string,       // إيموجي
    role: string,       // المسمى الوظيفي (عربي/إنجليزي)
    dims: string[],     // الأبعاد التي يقيسها الاختبار (٥ أبعاد)
    qs: Question[]      // ١٠ أسئلة
  }
}
```

**أنواع الأسئلة:**
```js
// اختيار من متعدد / سيناريو
{ type: 'mcq'|'scenario', dim: number, text: string, hint: string,
  choices: string[], scores: number[] }  // scores: 0-3 لكل خيار

// مقياس
{ type: 'scale', dim: number, text: string, hint: string,
  min: string, max: string }  // القيمة 1-5 → تحوّل لنسبة مئوية
```

### التخصصات الـ١٧
```
writing       الكتابة الإبداعية
content       كتابة المحتوى
animation     الأنيميشن
illustration  الرسم الطامس (الرقمي)
drawing       الرسم العام
motion        الموشن جرافيك
editing       المونتاج
voice         التمثيل الصوتي
directing     الإخراج
threed        الثري دي
marketing     التسويق
design        التصميم
coding        البرمجة والتقنية
management    الإدارة والإشراف
research      البحث والتدقيق
storyboard    الستوري بورد
pm            إدارة المشاريع
```

### `SKILLS` — قائمة التخصصات
```js
[{ id: string, label: string, icon: string }]
// ملاحظة: id في SKILLS يجب أن يطابق مفاتيح SKILL_FIELDS
```

### `TASKS` — المهام
```js
[{ id: string, text: string, cat: string }]  // 15 مهمة
```

---

## متغيرات الحالة (State)

```js
creatorName          // اسم المبدع (افتراضي: 'المبدع')
completedTasks       // Set() — معرّفات المهام المنجزة
poisonSelected       // { qIdx: score } — إجابات اختبار التسمم
compSelected         // { qIdx: 'a'|'b'|'c'|'d' } — إجابات اختبار الفهم
selectedSkills       // Set() — معرّفات المهارات المختارة
progress             // { nameSet, lectureWatched, ... }

// نظام اختبارات المهارات
sqSkillList          // string[] — المهارات المختارة بالترتيب
sqCurrentSkillIndex  // number — فهرس المهارة الحالية
sqCurrentQ           // number — فهرس السؤال الحالي
sqAnswers            // { skillId: { qIdx: answer } }
sqResults            // { skillId: { overall, finals, level } }
```

---

## الدوال الرئيسية

### التنقل والبنية
| الدالة | الوصف |
|---|---|
| `showPage(id)` | التنقل بين الصفحات الست + تحديث nav |
| `switchTab(tabId, btn)` | التبديل بين التبويبات داخل الصفحة |
| `updateProgress()` | تحديث شريط التقدم العلوي |

### المحاضرة والاختبارات
| الدالة | الوصف |
|---|---|
| `selectPoison(el, qIdx, score)` | اختيار إجابة اختبار التسمم |
| `submitPoisonQuiz()` | حساب وعرض نتيجة التسمم |
| `selectComp(el, qIdx, ans)` | اختيار + تقييم فوري لاختبار الفهم |
| `submitCompQuiz()` | عرض نتيجة اختبار الفهم |

### نظام اختبارات المهارات
| الدالة | الوصف |
|---|---|
| `initSkills()` | بناء شبكة اختيار المهارات |
| `startSkillsQuiz()` | بدء تسلسل الاختبارات |
| `buildSkillTabs()` | بناء تبويبات المهارات العلوية |
| `sqRenderQ()` | عرض السؤال الحالي |
| `sqSelectChoice(i)` | اختيار إجابة MCQ/scenario |
| `sqUpdateScale(v)` | تحديث قيمة مقياس |
| `sqNext()` | التالي (ينتقل للمهارة التالية أو النتائج) |
| `sqPrev()` | السابق |
| `computeSkillResult(sid)` | حساب النتيجة لمهارة واحدة |
| `showAllResults()` | عرض بطاقات كل المهارات |
| `renderPlayerCard(sid, grid)` | بناء بطاقة نتيجة مهارة واحدة |
| `retakeSkill(sid)` | إعادة اختبار مهارة بعينها |
| `retakeAllQuizzes()` | إعادة جميع الاختبارات |
| `resetSkills()` | العودة لخطوة اختيار المهارات |

### الروتين والمهام
| الدالة | الوصف |
|---|---|
| `selectRadio(groupId, el)` | تحديد خيار في مجموعة radio |
| `generateRoutine()` | توليد الروتين اليومي والخطة الربعية |
| `initTasks()` | بناء قائمة المهام |
| `initPlanning()` | بناء خطوات التخطيط |
| `goToStep(idx)` | الانتقال لخطوة تخطيط محددة |

---

## حساب نتيجة المهارة

```
لكل سؤال MCQ/scenario:
  raw = (scores[answer] / 3) × 100       // scores: 0, 1, 2, 3

لكل سؤال scale:
  raw = ((answer - 1) / 4) × 100         // answer: 1-5

لكل بُعد (dim):
  dimScore = متوسط rawScores للأسئلة التي تنتمي لهذا البعد

النتيجة الكلية = متوسط dimScores

المستويات:
  ≥ 85 → محترف  (أخضر)
  ≥ 70 → متقدم  (أزرق)
  ≥ 55 → متوسط  (بنفسجي)
  < 55 → مبتدئ  (برتقالي/أحمر)
```

---

## نظام الخطوط الحالي

```css
font-family: 'Tajawal', sans-serif;   /* الجسم الأساسي */
font-family: 'Cairo', sans-serif;     /* العناوين والأرقام الكبيرة */
/* مستورد من Google Fonts */
```

---

## نظام الألوان الحالي

```css
:root {
  --gold: #C9A84C;
  --gold-light: #E8C97A;
  --gold-dark: #8B6914;
  --ink: #0F0E0B;
  --ink2: #1C1A14;
  --parchment: #FAF6EE;
  --parchment2: #F2EBD9;
  --green-deep: #1A3A2A;
  --green-mid: #2D6A4F;
  --green-light: #52B788;
  --text-main: #1C1A14;
  --text-muted: #6B5E3A;
  --border: rgba(201,168,76,0.25);
  --shadow: rgba(0,0,0,0.12);
}
```

---

## التوافق والبنية التقنية

- **صفحة واحدة (SPA)**: كل شيء في ملف HTML واحد، لا يحتاج خادماً
- **JavaScript خالص**: لا مكتبات خارجية، لا frameworks
- **RTL كامل**: `<html dir="rtl">` + خطوط عربية
- **Responsive**: grid مرن، `clamp()` للخطوط، media queries لـ ≤600px
- **Google Fonts**: Tajawal + Cairo عبر CDN

---

---

## 🎨 متطلبات البراند وإعادة التصميم

### الخط المطلوب — Rubik

استبدال خطي Tajawal وCairo بخط **Rubik** كخط أساسي وحيد لكامل الواجهة.

```html
<!-- استبدل رابط Google Fonts الحالي بهذا -->
<link href="https://fonts.googleapis.com/css2?family=Rubik:ital,wght@0,300;0,400;0,500;0,700;0,900;1,400&display=swap" rel="stylesheet">
```

```css
/* استبدل كل مراجع الخطوط في CSS */
body            { font-family: 'Rubik', sans-serif; }
h1, h2, h3, h4 { font-family: 'Rubik', sans-serif; }

/* احذف كل مراجع 'Cairo' و'Tajawal' من CSS وJS */
/* مثال: font-family: 'Cairo', sans-serif  →  font-family: 'Rubik', sans-serif */
```

خط Rubik يدعم العربية بشكل ممتاز ويعمل مع RTL، وهو متاح على Google Fonts.

---

### ألوان البراند الأساسية

```css
/* ===== BRAND COLORS ===== */
:root {
  /* ألوان البراند الأساسية */
  --brand-red:     #F04D39;   /* أحمر — اللون الرئيسي، CTA، عناصر الإبراز */
  --brand-yellow:  #F4C82C;   /* أصفر — accent، تمييز، شارات التقدم */
  --brand-teal:    #3BB9AB;   /* فيروزي — نجاح، مهارات، عناصر إيجابية */
  --brand-blue:    #086FB4;   /* أزرق — روابط، معلومات، ثانوي */

  /* ألوان الخلفية والنص */
  --brand-bg:      #FAF9F7;   /* خلفية الصفحة (الأفتح) */
  --brand-dark:    #262626;   /* النص الرئيسي وخلفيات داكنة */
}
```

#### خريطة الاستخدام المقترحة

| العنصر | اللون الحالي | اللون الجديد |
|---|---|---|
| خلفية الصفحة `body` | `#FAF6EE` (parchment) | `#FAF9F7` (`--brand-bg`) |
| النص الرئيسي | `#1C1A14` | `#262626` (`--brand-dark`) |
| شريط التقدم العلوي (bg) | `#1A3A2A` (green-deep) | `#262626` (`--brand-dark`) |
| أزرار CTA الرئيسية `.btn-primary` | `#1A3A2A` | `#F04D39` (`--brand-red`) |
| أزرار الـ gold `.btn-gold` | `#C9A84C` | `#F4C82C` (`--brand-yellow`) |
| شريط التقدم fill | gradient ذهبي | `#F04D39` (`--brand-red`) |
| بطاقات النتيجة (top-bar) | gradient ذهبي | `#F04D39` (`--brand-red`) |
| مؤشر النجاح/الإنجاز | `#52B788` (green-light) | `#3BB9AB` (`--brand-teal`) |
| section-icon background | `#1A3A2A` | `#086FB4` (`--brand-blue`) |
| عناوين الأقسام `h2` | `#1A3A2A` | `#262626` (`--brand-dark`) |
| حدود البطاقات `--border` | `rgba(201,168,76,0.25)` | `rgba(38,38,38,0.12)` |
| `.active` في التبويبات | `#1A3A2A` | `#F04D39` (`--brand-red`) |
| خلفية hero | gradient أخضر-داكن | gradient `#262626` → `#1a1a1a` |
| حقل اسم المبدع (border) | `#C9A84C` | `#F04D39` (`--brand-red`) |

#### نظام الألوان المقترح الكامل (يستبدل `:root` الحالية)

```css
:root {
  /* Brand */
  --brand-red:    #F04D39;
  --brand-yellow: #F4C82C;
  --brand-teal:   #3BB9AB;
  --brand-blue:   #086FB4;
  --brand-bg:     #FAF9F7;
  --brand-dark:   #262626;

  /* Derived */
  --red-light:    #FEE9E7;    /* خلفيات حمراء خفيفة */
  --yellow-light: #FEF8E0;    /* خلفيات صفراء خفيفة */
  --teal-light:   #E0F5F3;    /* خلفيات فيروزية خفيفة */
  --blue-light:   #E0EEF9;    /* خلفيات زرقاء خفيفة */

  /* Neutrals */
  --bg:           #FAF9F7;    /* = brand-bg */
  --bg2:          #F0EFED;    /* سطح ثانوي */
  --text-main:    #262626;    /* = brand-dark */
  --text-muted:   #6B6B6B;    /* نص ثانوي */
  --border:       rgba(38,38,38,0.12);
  --shadow:       rgba(0,0,0,0.10);
}
```

---

### تحديثات CSS المطلوبة بالتفصيل

```css
/* 1. الخط */
body { font-family: 'Rubik', sans-serif; }

/* 2. الخلفية والنص */
body { background: var(--brand-bg); color: var(--brand-dark); }

/* 3. شريط التقدم */
#progress-bar-container { background: var(--brand-dark); border-bottom-color: var(--brand-yellow); }
#user-name-display      { color: var(--brand-yellow); }
#progress-fill          { background: var(--brand-red); }
.readiness-badge        { background: var(--brand-yellow); color: var(--brand-dark); }

/* 4. nav */
#main-nav               { background: var(--brand-dark); }
.nav-btn.active         { color: var(--brand-yellow); border-bottom-color: var(--brand-red); }

/* 5. أزرار */
.btn-primary            { background: var(--brand-red); color: white; border-color: var(--brand-red); }
.btn-primary:hover      { background: #d43d2a; }
.btn-gold               { background: var(--brand-yellow); color: var(--brand-dark); }
.btn-gold:hover         { background: #e6b820; }

/* 6. Hero */
#hero                   { background: linear-gradient(180deg, var(--brand-dark) 0%, #1a1a1a 100%); }
#hero h1                { color: var(--brand-yellow); }
.name-input-row input   { border-color: var(--brand-red); }

/* 7. Section icons */
.section-icon           { background: var(--brand-blue); }
.section-header h2      { color: var(--brand-dark); }

/* 8. Quiz options */
.quiz-opt.selected      { background: var(--brand-dark); border-color: var(--brand-red); }
.sq-choice.selected     { background: var(--brand-dark); border-color: var(--brand-red); }

/* 9. Tab buttons */
.tab-btn.active         { color: var(--brand-red); }
.skill-tab-btn.active   { background: var(--brand-red); border-color: var(--brand-red); }
.ftab.active            { background: var(--brand-red); border-color: var(--brand-red); }

/* 10. Progress bars */
#task-progress-bar      { background: linear-gradient(90deg, var(--brand-red), var(--brand-teal)); }
.src-bar-fill           { /* يبقى بلون الأداء: أحمر/أصفر/أخضر */ }

/* 11. بطاقات المهارات */
.src-top-bar            { background: linear-gradient(90deg, var(--brand-red), var(--brand-yellow)); }
.src-score-circle       { border-color: var(--brand-yellow); }
.src-score-num          { color: var(--brand-yellow); }

/* 12. خطوات التخطيط */
.step-header            { background: linear-gradient(135deg, var(--brand-dark), #333); }
.step-header h3         { color: var(--brand-yellow); }
.step-tip               { border-right-color: var(--brand-red); }

/* 13. skills-quiz-intro */
.skills-quiz-intro      { background: linear-gradient(135deg, var(--brand-dark), #333); }

/* 14. score-result */
.score-result           { background: linear-gradient(135deg, var(--brand-dark), #1a1a1a); }
.score-num              { color: var(--brand-yellow); }

/* 15. routine-timeline header */
.routine-timeline h3    { color: var(--brand-red); }
.time-label             { color: var(--brand-red); }
```

---

### ملاحظات التطبيق

1. **الخط**: Rubik يدعم العربية جيداً لكنه خط لاتيني في الأصل — تأكد من اختبار العربية في المتصفح قبل الإنتاج
2. **الأحمر على الأبيض**: `#F04D39` على خلفية بيضاء يمر معيار WCAG AA بصعوبة في الأحجام الصغيرة — استخدم `#262626` للنص الصغير على خلفية بيضاء واحتفظ بالأحمر للعناوين والأزرار
3. **الأصفر**: `#F4C82C` لا يقرأ جيداً على خلفية فاتحة — استخدمه على خلفيات داكنة فقط (`#262626` أو أغمق)
4. **الفيروزي** `#3BB9AB`: مناسب للعناصر الإيجابية (نجاح، إنجاز، مهارات عالية) كبديل للأخضر الحالي
5. **الأزرق** `#086FB4`: يعمل جيداً للمعلومات والروابط والأيقونات

---

### قائمة التحديثات المطلوبة في JS

```js
// في generatePlayerCards / renderPlayerCard
// استبدل الألوان المُضمّنة:
'#52B788'  →  '#3BB9AB'   // أشرطة النجاح
'#C9A84C'  →  '#F4C82C'   // أشرطة الأداء المتوسط
'#E85D24'  →  '#F04D39'   // أشرطة الأداء الضعيف

// في result cards
'#EAF3DE', '#3B6D11'  →  '#E0F5F3', '#1a8a7e'  // مستوى محترف (teal)
'#E6F1FB', '#185FA5'  →  '#E0EEF9', '#086FB4'  // مستوى متقدم (blue)
'#EEEDFE', '#534AB7'  →  '#FEF8E0', '#a08900'  // مستوى متوسط (yellow)
'#FAECE7', '#993C1D'  →  '#FEE9E7', '#c0392b'  // مستوى مبتدئ (red-light)

// في drawRadar / radarSvg
'#534AB7'  →  '#F04D39'   // stroke الرادار
'rgba(83,74,183,0.2)'  →  'rgba(240,77,57,0.2)'  // fill الرادار
```
