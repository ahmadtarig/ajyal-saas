# DEC-002 — Users, Roles & Permissions Foundation

**Project:** Ajyal / School Operating System SaaS  
**Status:** Approved / Baseline  
**Date:** 2026-09-18  
**Purpose:** تثبيت نموذج الهوية والصلاحيات والنطاقات والاعتمادات والتدقيق كأساس مؤسسي للمنصة.

---

## 1. Core Access Model

النموذج المعتمد:

```text
Identity
  ↓
Role Assignment
  ↓
Scope
  ↓
Permission
  ↓
Context / Policy Conditions
  ↓
Approval / Separation of Duties
  ↓
Access Decision
  ↓
Audit
```

المبدأ الأساسي:

> الدور وحده لا يكفي لتحديد الوصول.

كل قرار وصول يجب أن يراعي هوية المستخدم، الدور المعيّن، نطاق البيانات، نوع الإجراء، شروط السياق، ومتطلبات الاعتماد.

---

## 2. Identity ≠ Role

المستخدم يملك هوية واحدة، ويمكن أن يحمل أكثر من Role في نفس الوقت.

مثال:

```text
Ahmed
  ├─ Teacher
  └─ Homeroom Advisor
```

أو:

```text
Sara
  ├─ Principal
  └─ Academic Supervisor
```

لا يتم إنشاء حسابات منفصلة لنفس الشخص فقط بسبب اختلاف أدواره.

---

## 3. Role Template vs Role Assignment

يتم الفصل بين:

- **Role Template**: تعريف قياسي قابل لإعادة الاستخدام للصلاحيات المتوقعة.
- **Role Assignment**: تعيين فعلي لدور لمستخدم داخل Scope محدد.

مثال:

```text
Role Template:
Teacher

Role Assignment:
Ahmed
→ Teacher
→ School A
→ Grade 6
→ Section A
→ Mathematics
```

---

## 4. Permission Scope

الصلاحيات تعمل داخل نطاق محدد.

مستويات النطاق المحتملة:

```text
Tenant
School Group
School / Campus
Academic Stage
Gender Division
Grade
Section / Class
Subject
Individual Student
```

لا يلزم إدخال كل هذه المستويات يدويًا دائمًا؛ يمكن استنتاج Scope من التكليفات الأكاديمية والتنظيمية.

---

## 5. Functional Permission + Data Scope

قرار الوصول يعتمد على دمج:

```text
Functional Permission
+
Data Scope
```

مثال:

```text
Permission:
grade.enter

Scope:
School A / Grade 6 / Section A / Mathematics
```

---

## 6. Multiple Role Assignments

يمكن للمستخدم امتلاك عدة Role Assignments في سياقات مختلفة.

مثال:

```text
Ahmed

Assignment 1:
Teacher
School A
Grade 6
Section A
Math

Assignment 2:
Homeroom Advisor
School A
Grade 6
Section A
```

---

## 7. User Categories

تُفصل فئات المستخدمين إلى ثلاث طبقات:

### 7.1 SaaS Platform Users

مثل:

- SaaS Super Admin
- Customer Support
- Billing Admin
- Tenant Administrator

### 7.2 School Staff Users

مثل:

- Principal
- Academic Admin
- Admissions
- Registrar
- Teacher
- Homeroom Advisor
- Counselor
- Nurse
- Finance
- HR
- Operations
- Transport
- Quality
- IT Admin

### 7.3 External / Portal Users

مثل:

- Parent
- Student

---


---

## 7A. Tenant-Customizable Roles

المدرسة / الـTenant يجب أن تستطيع تعديل نموذج الأدوار بما يناسب هيكلها التشغيلي، بدل أن تكون ملزمة فقط بالأدوار القياسية التي تأتي مع المنصة.

يتم التفريق بين:

- **System Role Templates**: أدوار قياسية جاهزة من Ajyal لتسريع الإعداد.
- **Tenant Custom Roles**: أدوار تنشئها أو تعدلها المدرسة داخل نطاقها.
- **Protected Platform Roles**: أدوار خاصة بإدارة منصة SaaS نفسها ولا يمكن للمدرسة تعديلها.

أمثلة على System Role Templates:

- Teacher
- Principal
- Academic Supervisor
- Admissions Officer
- Finance Officer
- HR Officer
- Nurse
- Transport Officer

يمكن للمدرسة:

- Clone role template.
- Rename role for its own terminology.
- Add or remove allowed permissions within the tenant policy boundary.
- Adjust default scope rules.
- Create a completely new school role.
- Disable unused school roles.

مثال:

```text
Ajyal Template:
Academic Supervisor

School Custom Role:
Head of Secondary Academics

Permissions:
- academic.view_stage
- grade.review
- grade.approve
- timetable.view
- attendance.review

Scope:
Secondary Stage
```

### Guardrails

تخصيص الأدوار لا يعني أن المدرسة تستطيع تجاوز حدود أمان المنصة.

لا يجوز لـTenant Custom Role أن:

- يمنح صلاحيات SaaS Platform Administration.
- يتجاوز Tenant isolation.
- يمنح الوصول إلى Tenant آخر.
- يتجاوز Protected Security Controls.
- يلغي Audit requirements للعمليات الحساسة.
- يتجاوز Separation of Duties أو Explicit Deny عندما تكون مفروضة كسياسة حماية أساسية.

المبدأ:

```text
Platform Security Boundary
        ↓
Tenant Permission Catalogue
        ↓
School Role Templates / Custom Roles
        ↓
User Role Assignments
        ↓
Scope
```

وبذلك تكون المدرسة مرنة في تصميم Roles الخاصة بها، بينما تظل حدود الأمان الأساسية تحت سيطرة المنصة.


## 8. Parent Access Model

لا يحصل ولي الأمر على صلاحيات عامة على الطلاب.

الوصول ينتج من:

```text
Parent
  ↓
Parent-Student Relationship
  ↓
Authorized Child
```

يمكن أن تتضمن العلاقة أنواعًا مثل:

- Father
- Mother
- Guardian
- Authorized Pickup
- Financial Guardian
- Academic Viewer
- Restricted Contact

يجب دعم قيود الحضانة والتواصل وعدم افتراض أن كل ولي أمر يرى كل شيء.

---

## 9. Deny by Default

المبدأ الأمني المعتمد:

> **Deny by Default**

المستخدم لا يرى أو يعدل شيئًا إلا إذا كان لديه Role/Assignment/Permission يسمح بذلك.

يجب فرض هذا في الـBackend، وليس فقط بإخفاء عناصر الواجهة.

---

## 10. Delegation / Temporary Assignment

يدعم النظام التفويض والتكليف المؤقت.

كل Delegation أو Temporary Assignment يمكن أن يحتوي على:

- Start Date
- End Date
- Delegator
- Delegatee
- Scope
- Permissions / Role
- Reason

قاعدة أساسية:

```text
Delegated Permission
⊆
Delegator Permission
```

أي لا يجوز أن يمنح المفوِّض صلاحية أعلى من صلاحياته الأصلية.

---

## 11. Access Lifecycle

دورة حياة الوصول:

```text
Requested
→ Approved
→ Active
→ Suspended
→ Expired / Revoked
```

يجب دعم Start/End Date للتكليفات المؤقتة.

---

## 12. Automatic Deprovisioning

عند انتهاء علاقة الموظف أو نقله أو تغيير وظيفته:

```text
Employment / Assignment Change
  ↓
Re-evaluate Access
  ↓
Revoke / Adjust Role Assignments
  ↓
Invalidate Sessions when required
  ↓
Preserve Audit History
```

لا يعتمد النظام فقط على الإزالة اليدوية للصلاحيات.

---

## 13. Periodic Access Review / Recertification

يجب دعم مراجعة دورية للصلاحيات، مثل نهاية Term أو Academic Year.

المراجع يرى:

- Who has access?
- To what?
- Why?
- Since when?
- Who approved it?

والقرارات:

- Keep
- Modify
- Revoke

---

## 14. Approval Authority vs Operational Permission

يتم الفصل بين تنفيذ العملية واعتمادها.

مثال:

```text
Teacher
→ Enter Grade

Academic Supervisor
→ Review / Approve

Authorized Publisher
→ Publish
```

ينطبق المبدأ على:

- Grades
- Admissions
- Discounts
- Transfers
- Official Notices
- Sensitive Corrections
- Other critical workflows

---

## 15. No Self-Approval

لا يجوز للمستخدم اعتماد عملياته الحساسة بنفسه عندما تطلب السياسة فصل المهام.

مبدأ عام:

```text
Maker ≠ Checker
```

---

## 16. Maker–Checker / Four-Eyes Principle

يدعم النظام نمط:

```text
Maker
  ↓
Checker
  ↓
Approver (Optional)
```

يتم تفعيله فقط للعمليات التي تتطلبه سياسة المدرسة أو المنصة.

---

## 17. Sensitive Data Zones

البيانات الحساسة لا تعتمد فقط على Role عام.

مستويات وصول مقترحة:

```text
Normal Access
Restricted Access
Highly Sensitive Access
Emergency Break-Glass
```

أمثلة للبيانات الحساسة:

- Health
- Behavior
- Safeguarding
- Custody Restrictions
- Financial Details

---

## 18. Break-Glass Access

في الحالات الطارئة يمكن منح وصول استثنائي للبيانات الحساسة مع تسجيل إلزامي:

- Who
- When
- Why
- What data was accessed

هذا الوصول يجب أن يكون واضحًا في Audit Log.

---

## 19. Access Context

نفس المستخدم قد يعمل في أكثر من School / Campus أو بأدوار مختلفة.

مثال:

```text
User
  ├─ School A → Teacher
  └─ School B → Homeroom Advisor
```

لا يتم إنشاء حساب منفصل لكل مدرسة إذا كان نفس الشخص.

---

## 20. Permission Bundles

لتسهيل الإدارة، تجمع الصلاحيات التفصيلية في Bundles مفهومة مثل:

- Student Academic Access
- Attendance Management
- Grade Entry
- Grade Approval
- Parent Communication
- Financial Operations
- Health Access
- Transport Operations

مع إمكانية فتح التفاصيل للمسؤول المتقدم.

---

## 21. Permission Inheritance and Override

يسمح النظام بوراثة الصلاحيات مع Override عند الحاجة.

```text
Inherited Access
+
Explicit Allow
+
Explicit Deny
```

قاعدة التعارض:

> **Explicit Deny wins.**

---

## 22. Field-Level Security

قد يسمح للمستخدم بفتح سجل طالب، لكن ليس كل الحقول.

مثال المعلم قد يرى:

- Student Name
- Class
- Attendance
- Academic Progress

ولا يرى بالضرورة:

- Medical Details
- Custody Restrictions
- Financial Balance
- Sensitive Behavior Notes

---

## 23. Action-Level Permissions

يتم الفصل بين أنواع الإجراءات:

```text
View
Create
Edit
Delete
Approve
Publish
Export
Print
Share
Override
```

الوصول للبيانات لا يعني تلقائيًا حق تعديلها أو تصديرها.

---

## 24. Bulk Action Permissions

العمليات الجماعية لها صلاحيات مستقلة عن العمليات الفردية.

مثال:

```text
grade.edit_single
grade.edit_bulk

student.message_single
student.message_bulk

attendance.correct_single
attendance.correct_bulk
```

---

## 25. SaaS Admin Isolation

إدارة منصة SaaS لا تعني تلقائيًا الوصول إلى Customer Data.

```text
Platform Administration
≠
Customer Data Access
```

يمكن لمسؤول المنصة إدارة:

- Tenant
- Subscription
- Licenses
- Usage
- Configuration
- Support

أما الوصول إلى بيانات العميل فيكون مسارًا منفصلًا ومقيدًا ومدققًا.

---

## 26. Support Impersonation

دعم Ajyal يمكن أن يستخدم جلسة دعم مؤقتة ومراقبة:

```text
Support Agent
→ Authorized Support Session
→ Select Tenant
→ Impersonate / View Context
→ Limited Duration
→ Full Audit
→ Session Ends
```

يجب أن يظهر بوضوح:

```text
You are viewing as: <User>
```

ولا يسمح بتنفيذ عمليات حساسة إلا بصلاحيات خاصة.

---

## 27. Session & Device Security

يدعم النظام:

- Active Sessions
- Known Devices
- Last Login
- IP / Device Metadata
- Force Logout
- Revoke All Sessions

مع قابلية إضافة MFA للأدوار الحساسة مثل:

- SaaS Admin
- Principal
- Finance Manager
- IT Admin

---

## 28. High-Risk Action Re-authentication

يمكن أن تتطلب بعض العمليات إعادة تأكيد الهوية أو MFA مستقبلًا، مثل:

- Mass Student Deletion
- Financial Adjustment
- Custody Restriction Change
- System Administrator Change
- Sensitive Data Export
- Publishing Final Results

---

## 29. Permission Conditions / Policy Rules

بعض قرارات الوصول تعتمد على السياق وليس فقط Role + Scope.

مثال:

```text
Teacher can edit grades
ONLY IF:
- Assessment is open
- Grade is not published
- User teaches that subject
```

الصيغة المعتمدة:

```text
Identity
+
Role
+
Scope
+
Permission
+
Context / Condition
=
Access Decision
```

---

## 30. Audit Log

يجب تسجيل العمليات الحساسة، مثل:

- Login
- Permission Change
- Role Assignment
- Grade Change
- Approval
- Sensitive Data Access
- Export
- Impersonation
- Break-Glass Access

مستخدم Audit Viewer يستطيع القراءة، لكن لا يستطيع تعديل سجل التدقيق.

---

## 31. Reason Codes

بعض العمليات الحساسة يجب أن تتطلب سببًا أو Reason Code.

مثال تغيير درجة منشورة:

- Data Entry Correction
- Approved Appeal
- Administrative Correction
- Other

مع Notes عند الحاجة.

---

## 32. Permission Explainability

النظام يجب أن يفسر سبب السماح أو المنع.

مثال:

```text
Access denied

You have:
Teacher / Mathematics / Grade 6A

But:
Assessment status = Published

Required:
grade.modify_published
```

كما يستطيع المسؤول استخدام:

`Why does this user have access?`

ويظهر مسار الاستحقاق، مثل:

```text
Ahmed
→ Teacher Role
→ School A
→ Grade 6A
→ Mathematics Assignment
→ grade.enter
```

---

## 33. Final Security Principle

كل عملية حساسة تمر عبر:

```text
Authentication
→ Role
→ Scope
→ Permission
→ Policy Rule
→ Approval / SoD
→ Audit
```

---

## 34. Change Control

هذه الوثيقة تعتبر **Approved Baseline** لنموذج Users, Roles & Permissions.

أي تغيير جوهري لاحق في:

- Identity Model
- Role Assignment Model
- Scope Hierarchy
- Permission Semantics
- Delegation
- Approval Separation
- Sensitive Access
- SaaS Admin Isolation
- Impersonation
- Audit Model

يجب تسجيله كقرار جديد وتوضيح أثره قبل تعديل هذا الـBaseline.
