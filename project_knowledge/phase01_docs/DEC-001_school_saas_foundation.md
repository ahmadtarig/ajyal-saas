# DEC-001 — School SaaS Foundation Decisions

**Project:** Ajyal / School Operating System SaaS  
**Status:** Approved / Baseline  
**Date:** 2026-09-18  
**Purpose:** تثبيت القرارات المعتمدة حتى الآن كأساس للتصميم اللاحق وعدم تغييرها إلا بقرار صريح جديد.

---

## 1. Product Definition

المنتج عبارة عن **B2B Multi-Tenant School SaaS Platform / School Operating System** متكامل، وليس مجرد Student Information System تقليدي.

يدعم المنتج:

- مدرسة واحدة.
- مجموعة مدارس.
- عدة فروع / Campuses تحت نفس المجموعة.
- عزل بيانات كل Tenant ومدرسة وفق الصلاحيات.
- إدارة مركزية على مستوى المجموعة مع إمكانية الإدارة المستقلة لكل مدرسة أو فرع.

---

## 2. Approved Organizational & Academic Hierarchy

الهيكل الأساسي المعتمد هو:

```text
Tenant
  ↓
School Group
  ↓
School / Campus
  ↓
Academic Stage
  ↓
Gender Division (Optional)
  ↓
Grade
  ↓
Section / Class
```

### Terminology

- **Grade** = الصف الدراسي.
- **Section / Class** = الفصل / الشعبة.

---

## 3. Gender Model

يأتي **Academic Stage** قبل **Gender Division** في الهيكل.

`Gender Division` اختياري وقابل للتهيئة لكل مرحلة، وليس إلزاميًا على مستوى المدرسة بالكامل.

النماذج المدعومة:

- `Boys Only`
- `Girls Only`
- `Separate Boys / Girls`
- `Mixed / Co-ed`

### Example

```text
Primary
  └── Mixed

Intermediate
  ├── Boys
  └── Girls

Secondary
  ├── Boys
  └── Girls
```

الهدف هو السماح لنفس المدرسة بتطبيق نموذج مختلف لكل مرحلة.

---

## 4. Academic Core Scope

النواة الأكاديمية الأساسية يجب أن تبقى بسيطة وعملية، بدون فرض إدارة منهج تفصيلية على المستخدم.

الهيكل التشغيلي الأساسي:

```text
Academic Stage
  ↓
Gender Division (Optional)
  ↓
Grade
  ↓
Section / Class
  ↓
Students
  ↓
Subjects
  ↓
Subject Teachers
  ↓
Homeroom / Class Advisor
  ↓
Attendance
  ↓
Assessment Series
  ↓
Assessment Records
  ↓
Assessment Policy
  ↓
Final Result
  ↓
Published Grade
  ↓
Notes / Messages / Official Notices
  ↓
Student Academic Timeline
```

---

## 5. Curriculum / LMS Boundary

لا يُفرض في النواة الحالية نموذج تفصيلي مثل:

```text
Curriculum → Unit → Lesson → Learning Outcome → Learning Content
```

بدلًا من ذلك، يكفي في النواة:

```text
Subject → Teacher → Grade / Section → Assessments → Grades → Notes
```

### Future Roadmap

يمكن تطوير Module اختياري مستقبلًا مثل:

`Curriculum & Learning Management / LMS`

بحيث يدعم لاحقًا:

- Curriculum
- Units
- Lessons
- Learning Outcomes
- Learning Content
- Assignments / Digital Learning

يجب أن يظل التصميم الحالي قابلًا للتوسع نحو هذا المستقبل، لكن دون فرض خطوات إضافية على المدرسة الآن.

---

## 6. Subject Metadata

الحد الأدنى المطلوب للمادة:

- Subject Name
- Grade
- Section / Class
- Subject Teacher(s)

بيانات إضافية مثل التالية تكون **Optional** وغير ملزمة:

- Subject Code
- Description
- Notes
- Assessment Weight
- General Study Plan

---

## 7. Homeroom / Class Advisor

يتم دعم دور مستقل لـ **Homeroom / Class Advisor** أو مرشد الصف، منفصل عن Subject Teacher.

يستخدم في:

- متابعة الطلاب على مستوى الفصل.
- إضافة ملاحظات تربوية أو أكاديمية.
- التواصل مع ولي الأمر وفق الصلاحيات.
- متابعة الصورة الشاملة للطالب وليس مادة واحدة فقط.

---

## 8. Communication Types

تُعتمد الأنواع التالية:

### 8.1 Internal Note
ملاحظة داخلية لا تظهر لولي الأمر، ومقيدة حسب الصلاحية.

### 8.2 Parent Message
رسالة مباشرة لولي الأمر.

### 8.3 Official Notice
إشعار رسمي من المدرسة مع إمكانية التتبع مثل:

```text
Sent → Delivered → Viewed
```

---

## 9. Assessment Model

النظام لا يفرض طريقة تقييم واحدة على كل المدارس.

يجب الفصل بين:

1. **Assessment Records** — جميع الدرجات والتقييمات المسجلة خلال العام.
2. **Final Result / Pass Calculation Policy** — القاعدة التي تحدد ما يدخل فعليًا في حساب النتيجة النهائية أو الاجتياز.

### Typical Model Discussed

قد تكون لدى المدرسة سلسلة تقييمات مثل:

```text
Exam 1
Exam 2
Assignment
Exam 3
First Period Exam
Final Exam
```

كل النتائج تظل محفوظة وقابلة للعرض والمتابعة، لكن سياسة المدرسة قد تحدد أن اجتياز المرحلة يعتمد فقط على:

```text
First Period Exam + Final Exam
```

ولا تدخل بقية الامتحانات أو الـAssignments في قرار الاجتياز، رغم بقائها ضمن سجل الطالب.

---

## 10. Flexible Assessment Policy

يجب أن تكون سياسة التقييم **Configurable** بحيث تستطيع كل مدرسة تحديد الطريقة المناسبة لها، مثل:

- `First Period Exam + Final Exam`
- `Final Exam Only`
- `Selected Exams`
- `Average of Selected Exams`
- `Weighted Exams`
- `Coursework + Final Exam`
- `Assignments + Projects + Exams`
- `Best N Assessments`
- `Latest Assessment`
- `Manual Final Result`

ويُسمح باختلاف السياسة حسب:

- School / Campus
- Academic Stage
- Grade
- Subject

حسب حاجة المدرسة وسياساتها.

---

## 11. Assessment Status Semantics

لكل تقييم يجب الفصل بين الحالات التالية:

- `Recorded` — الدرجة موجودة في النظام.
- `Published` — مسموح للطالب / ولي الأمر برؤيتها.
- `Counts Toward Final Result` — تدخل في حساب النتيجة النهائية / الاجتياز أم لا.

وجود درجة لا يعني تلقائيًا أنها تدخل في حساب النجاح.

---

## 12. Raw vs Published Grade

يتم الفصل بين:

- `Raw / Recorded Grade`
- `Approved / Published Grade`

المسار المبدئي:

```text
Teacher enters grade
  ↓
Draft / Recorded
  ↓
Review / Approval (when required by school policy)
  ↓
Published
  ↓
Parent / Student can view
```

لا تظهر الدرجة لولي الأمر أو الطالب قبل نشرها وفق سياسة المدرسة.

---

## 13. Grade Change Audit Trail

أي تعديل في الدرجات يجب أن يحتفظ بسجل واضح يتضمن على الأقل:

- Old Value
- New Value
- Changed By
- Changed At
- Reason / Note when required

لا تُستبدل الدرجة القديمة دون أثر تدقيقي.

---

## 14. Student Academic Timeline

يُعتمد مفهوم **Student Academic Timeline** لتجميع التطور التاريخي للطالب عبر الزمن، مثل:

- Attendance
- Assessment Results
- Published Grades
- Notes
- Important Messages / Notices
- Academic Progress Indicators

الهدف هو تقديم صورة زمنية متكاملة بدل الاعتماد على نتيجة لحظية واحدة.

---

## 15. Promotion Policy

الـ **Promotion** أو انتقال الطالب للصف التالي لا يُفرض من النظام بسياسة عالمية واحدة.

المبدأ المعتمد:

> **Promotion Policy belongs to the School.**

أي أن كل مدرسة تحدد سياستها وفق لوائحها.

يمكن للنظام حساب أو اقتراح Final Result وفق Assessment Policy، لكن قرار الانتقال الفعلي يخضع لسياسة المدرسة وإجراءات الاعتماد لديها.

حالات قرار نهاية العام قد تشمل:

- Promote
- Repeat
- Transfer
- Graduate
- Other

---

## 16. Design Principle

المبدأ العام المعتمد لهذه المرحلة:

> **Simple for daily school operation, flexible in policy, auditable in sensitive actions, and extensible for future LMS capabilities.**

أي:

- لا نفرض تعقيدًا لا تستخدمه أغلب المدارس.
- نحافظ على مرونة كل مدرسة في سياساتها.
- نحفظ التاريخ والتدقيق في الدرجات والقرارات الحساسة.
- نترك التصميم قابلًا للتوسع دون إعادة بناء النواة.

---

## 17. Change Control

هذه القرارات تعتبر **Approved Baseline** للمشروع.

أي تغيير جوهري لاحق في:

- Organizational Hierarchy
- Gender Model
- Academic Structure
- Assessment Semantics
- Promotion Policy
- Curriculum / LMS Boundary

يجب تسجيله كقرار جديد وتوضيح أثره قبل تعديل هذا الـBaseline.
