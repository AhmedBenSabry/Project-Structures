# Tech Stack:
- Backend: .NET 8 Web API
- Database: SQL Server + Entity Framework Core
- Authentication: JWT + Google/Facebook + FCM
- Architecture: Clean 3-Tier + DTO Project

## Folder Structure
Softic.Solution/
- Softic (Presentation Layer - Web API)
- Softic.BLL (Business Logic)
- Softic.DAL (Data Access + EF Core)
- Softic.DTO (جميع الـ DTOs)


# Softic Solution

## مشروع يحتوي على عدة طبقات بنية (Layered Architecture) وهي:

### 1. **Softic (Presentation Layer - Web API)**

- **Configurations/**: إعدادات الخدمات والميدل وير.
- **Controllers/**: جميع وحدات التحكم في API.
  - `AuthController.cs`
  - `User/`, `EmployeeControllers/`, `VendorController.cs`, ...
  - ...(30+ مجلد ميزة)
- **Helper/**: المساعدات والخدمات المشتركة.
- **Jobs/**: المهام الخلفية باستخدام Hangfire.
- **Resources/**: ملفات التوطين (Localization) مثل ملفات `.resx`.
- `Program.cs`
- `appsettings.json` و `appsettings.Development.json`
- `Softic.csproj`

### 2. **Softic.BLL (Business Logic Layer)**

- **AuthServices/**: خدمات المصادقة (بما في ذلك تسجيل الدخول عبر Google/Facebook).
- **Enums/**: جميع الثوابت (Enums) للمشروع.
- **Extensions/**: طرق التوسيع (Extension Methods).
- **Helper/**: ردود API، JWT، التصفح (Pagination)...
- **Implementation/**: جميع منطق العمل (Business Logic classes).
  - `UserBL.cs`, `EmployeeBL.cs`, `RequestBL.cs`, ...
  - `UnitOfWork.cs`
- **Interface/**: جميع الواجهات (مثل `IUserBL`, `IEmployeeBL`...).
- **Resolver/**: محولات القيم المخصصة (AutoMapper).
- **Services/**: خدمات خارجية (مثل SMS، FCM، التعرف على الوجه).
- `Softic.BLL.csproj`

### 3. **Softic.DAL (Data Access Layer - Entity Framework)**

- **Database/**:
  - `ApplicationDbContext.cs`
  - `EntityConfigurations/`
- **Entities/**: الكيانات الخاصة بـ EF Core (منظمة حسب الميزة).
  - `Users/`, `EmployeeModels/`, `Requests/`, `Transactions/`, ...
- **Enums/**: ثوابت المشروع.
- **Extends/**: `BaseEntity`, `AppUser`, `IMustHaveCompany`...
- **Migrations/**: الداتا بيز.
- **Security/**: ملفات الأمان.
- `Softic.DAL.csproj`

### 4. **Softic.DTO (Data Transfer Objects)**

- **AssetsDtos/**, `AuthDtos/`, `EmployeeDtos/`, `RequestDtos/`, ...
- `BaseDto.cs`, `BaseEditDto.cs`
- جميع DTOs الخاصة بالميزات (مثل `Add`, `Edit*`, `Return*`).
- **Lookup/**: جميع DTOs الخاصة بالقيم المرجعية (مثل الفرع، القسم، إلخ).
- `Softic.DTO.csproj`

### 5. **الملفات الأخرى:**
- `Softic.sln`: الملف الرئيسي للحل.

---
---


# ترتيب الجداول حسب الأهمية والتسلسل المنطقي

### المجموعة الأساسية (Core Tables)
| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 1      | Company                   | الشركة (الـ Tenant)                     | كل حاجة تابعة ليها              |
| 2      | AspNetUsers               | مستخدمي النظام (Identity)               | مرتبط بـ Employee               |
| 3      | Employee                  | بيانات الموظف الشخصية والوظيفية        | أهم جدول بعد Company           |
| 4      | Branch                    | الفروع                                 |                                 |
| 5      | Department                | الأقسام                                |                                 |
| 6      | Position                  | المناصب (مع PositionManagerId للمدير)   | Hierarchical                    |
| 7      | Teams                     | الفرق                                   |                                 |

### المجموعة التنظيمية والهيكلية

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 8  | OrganizationChart         | الرسم التنظيمي الديناميكي               | EntityId + Type + ParentId      |
| 9  | EmployeeLevel             | مستويات الموظفين                        |                                 |
| 10 | EmployeeLevelSetting      | إعدادات المستوى (إجازات، بدلات...)       | Many-to-Many مع EmployeeLevel   |

### الحضور والانصراف (أكتر Module بيستخدم)

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 11 | EmployeeAttendance        | سجل الدخول والخروج                      |                                 |
| 12 | EmployeeAttendanceMains   | الملخص اليومي (WorkingHours محسوبة)     | Computed Column                 |
| 13 | EmployeeAttendanceLocation| المواقع المسموحة للحضور                 |                                 |
| 14 | AttendanceType            | أنواع الحضور (دخول، خروج، إجازة...)     |                                 |
| 15 | CompanyOffDays            | عطلات الشركة الرسمية                    |                                 |

### الطلبات (Requests) – أكبر Workflow في النظام

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 16 | Request                   | الطلبات (إجازة، إذن، عهدة...)           | Duration محسوبة                |
| 17 | RequestType               | أنواع الطلبات                          |                                 |
| 18 | RequestCategory           | تصنيفات الطلبات                         |                                 |
| 19 | RequestStatus             | حالات الطلب                             |                                 |
| 20 | RequestApprovalMatrix     | مصفوفة الموافقات (من مين لمين)          | أهم جدول في الـ Workflow       |
| 21 | RequestAttachment         | مرفقات الطلب                            |                                 |

### الأصول (Assets)
| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 22 | AssetCategory             | تصنيفات الأصول (Hierarchical)           | Self-Reference                  |
| 23 | Assets                    | الأصول نفسها (سيارات، لاب توب...)       | Self-Reference + EmployeeId     |
| 24 | AssetAttachment           | مرفقات الأصل                            |                                 |

### المهام (Tasks)

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 25 | Tasks                     | المهام الرئيسية                         |                                 |
| 26 | TaskAssignments           | تعيين الموظفين للمهمة                    |                                 |
| 27 | ToDoItems                 | البنود الفرعية                          |                                 |
| 28 | TaskAttachments           | مرفقات المهمة                           |                                 |

### الماليات (Financial)

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 29 | Payables                  | المستحقات (ديون العملاء)                |                                 |
| 30 | Receivables               | التحصيلات (الدفعات)                     |                                 |
| 31 | FinancialCustodies        | العهد المالية                           |                                 |
| 32 | BankTransfareFromEmployees| تحويلات بنكية من الموظفين               |                                 |
| 33 | PaymentTypes              | طرق الدفع                              |                                 |

### المسارات والعملاء (Sales Routes)

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 34 | Paths                     | المسارات التجارية                       |                                 |
| 35 | Customers                 | العملاء (Self-Reference للتاجر الجملة)  |                                 |
| 36 | EmployeePaths             | ربط الموظف بالمسار (Many-to-Many)        |                                 |
| 37 | CustomerPath              | ربط العميل بالمسار (Many-to-Many)        |                                 |

### الموردين

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 38 | VendorTypes               | أنواع الموردين                          |                                 |
| 39 | Vendors                   | الموردين                               |                                 |

### الرواتب والمستحقات

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 40 | EmployeeSalaryArchives    | أرشيف الرواتب الشهرية                   |                                 |
| 41 | EmployeeDeduction         | الخصومات                               |                                 |
| 42 | EmployeeEntitlment        | المستحقات والبدلات                      |                                 |
| 43 | MassiveActionTransactions | العمليات الجماعية (خصم/إضافة لمجموعة)   |                                 |

### الإشعارات والدعم

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 44 | NotificationLog           | سجل الإشعارات                           |                                 |
| 45 | Support + SupportAttachment| طلبات الدعم                           |                                 |
| 46 | Help + HelpAttachment     | طلبات المساعدة                          |                                 |

### القضايا والشكاوى

| الترتيب | الجدول                    | الوصف                                   | ملاحظات مهمة                    |
|--------|---------------------------|-----------------------------------------|----------------------------------|
| 47 | Issue                     | الشكاوى والقضايا                        |                                 |
| 48 | IssueAttachment           | مرفقات القضية                           |                                 |
| 49 | IssueExcuter              | منفذي القضية                            |                                 |

### الجداول المرجعية (Lookup Schema)
كل الجداول دي في schema اسمه [Lookup]:
- Country → City → Zone
- Currency, BloodType, Degree, Grade, Language, DocumentType, BusinessType, BusinessSize, ...

---
---

# تحليل العلاقات بين الجداول في قاعدة البيانات

## **1. جداول المستخدمين والهوية (Identity & User)**

### **AspNetUsers → Employee**
- **النوع:** One-to-One
- **العلاقة:** كل مستخدم (AspNetUsers) يمكن أن يكون موظفًا واحدًا (Employee)
- **الربط:** `Employee.Id` يشير إلى `AspNetUsers.Id`

### **AspNetUsers → UserAddress**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمكن أن يمتلك عدة عناوين
- **الربط:** `UserAddress.UserId` → `AspNetUsers.Id`

### **AspNetUsers → UserEmail**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمكن أن يمتلك عدة إيميلات
- **الربط:** `UserEmail.UserId` → `AspNetUsers.Id`

### **AspNetUsers → UserTelephone**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمتلك عدة أرقام هواتف
- **الربط:** `UserTelephone.UserId` → `AspNetUsers.Id`

### **AspNetUsers → UserAttachment**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمتلك عدة مرفقات
- **الربط:** `UserAttachment.UserId` → `AspNetUsers.Id`

### **AspNetUsers → UserLifeStyle**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمتلك عدة أنماط حياة
- **الربط:** `UserLifeStyle.UserId` → `AspNetUsers.Id`

### **AspNetUsers → UserRefernce**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يمتلك عدة مراجع شخصية
- **الربط:** `UserRefernce.UserId` → `AspNetUsers.Id`

---

## **2. جداول الأدوار والصلاحيات (Roles & Permissions)**

### **AspNetRoles → AspNetRoleClaims**
- **النوع:** One-to-Many
- **العلاقة:** الدور الواحد له عدة صلاحيات
- **الربط:** `AspNetRoleClaims.RoleId` → `AspNetRoles.Id`

### **AspNetUsers ↔ AspNetRoles (عبر AspNetUserRoles)**
- **النوع:** Many-to-Many
- **جدول الربط:** `AspNetUserRoles`
- **العلاقة:** المستخدم يمكن أن يكون له عدة أدوار، والدور يمكن أن يُسند لعدة مستخدمين
- **الربط:** 
  - `AspNetUserRoles.UserId` → `AspNetUsers.Id`
  - `AspNetUserRoles.RoleId` → `AspNetRoles.Id`

---

## **3. الهيكل التنظيمي (Organizational Structure)**

### **Company → Branch**
- **النوع:** One-to-Many
- **العلاقة:** الشركة تحتوي على عدة فروع
- **الربط:** `Branch.CompanyId` → `Company.Id`

### **Branch → Department**
- **النوع:** One-to-Many
- **العلاقة:** الفرع يحتوي على عدة أقسام
- **الربط:** `Department.BranchId` → `Branch.Id`

### **Department → Position**
- **النوع:** One-to-Many
- **العلاقة:** القسم يحتوي على عدة مناصب
- **الربط:** `Position.DepartmentId` → `Department.Id`

### **PositionType → Position**
- **النوع:** One-to-Many
- **العلاقة:** نوع المنصب يُستخدم في عدة مناصب
- **الربط:** `Position.PositionTypeId` → `PositionType.Id`

### **Position → Position (Self-Reference)**
- **النوع:** One-to-Many (Hierarchical)
- **العلاقة:** المنصب يمكن أن يكون تابعًا لمنصب آخر (مدير)
- **الربط:** `Position.PositionManagerId` → `Position.Id`

---

## **4. بيانات الموظف (Employee Data)**

### **Employee → Branch**
- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين ينتمون لفرع واحد
- **الربط:** `Employee.BranchId` → `Branch.Id`

### **Employee → Department**
- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين ينتمون لقسم واحد
- **الربط:** `Employee.DepartmentId` → `Department.Id`

### **Employee → Position**
- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين يشغلون منصبًا واحدًا
- **الربط:** `Employee.PositionId` → `Position.Id`

### **Employee → Teams**
- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين ينتمون لفريق واحد
- **الربط:** `Employee.TeamId` → `Teams.Id`

### **Employee → EmployeeLevel**
- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين ينتمون لمستوى وظيفي واحد
- **الربط:** `Employee.EmployeeLevelId` → `EmployeeLevel.Id`

### **Employee → EmployeeEducational**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يمتلك عدة مؤهلات تعليمية
- **الربط:** `EmployeeEducational.EmployeeId` → `Employee.Id`

### **Employee → EmployeeCertificate**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يمتلك عدة شهادات
- **الربط:** `EmployeeCertificate.EmployeeId` → `Employee.Id`

### **Employee → EmployeeWorkHistory**
- **النوع:** One-to-Many
- **العلاقة:** الموظف لديه عدة سجلات خبرات عمل سابقة
- **الربط:** `EmployeeWorkHistory.EmployeeId` → `Employee.Id`

### **Employee → EmployeeHealthInformation**
- **النوع:** One-to-Many
- **العلاقة:** الموظف لديه معلومات صحية
- **الربط:** `EmployeeHealthInformation.EmployeeId` → `Employee.Id`

### **Employee → EmployeeIdentityDocument**
- **النوع:** One-to-Many
- **العلاقة:** الموظف لديه عدة وثائق هوية
- **الربط:** `EmployeeIdentityDocument.EmployeeId` → `Employee.Id`

### **Employee → EmployeeLanguage**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يتحدث عدة لغات
- **الربط:** `EmployeeLanguage.EmployeeId` → `Employee.Id`

### **Employee → EmployeeSkills**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يمتلك عدة مهارات
- **الربط:** `EmployeeSkills.EmployeeId` → `Employee.Id`

---

## **5. الحضور والانصراف (Attendance)**

### **Employee → EmployeeAttendance**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة سجلات حضور وانصراف
- **الربط:** `EmployeeAttendance.EmployeeId` → `Employee.Id`

### **AttendanceType → EmployeeAttendance**
- **النوع:** One-to-Many
- **العلاقة:** نوع الحضور (دخول/خروج) يُستخدم في عدة سجلات
- **الربط:** `EmployeeAttendance.AttendanceTypeId` → `AttendanceType.Id`

### **Employee → EmployeeAttendanceLocation**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة مواقع حضور مسموحة
- **الربط:** `EmployeeAttendanceLocation.EmployeeId` → `Employee.Id`

### **Employee → EmployeeAttendanceMains**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة سجلات حضور رئيسية (ملخصات يومية)
- **الربط:** `EmployeeAttendanceMains.EmployeeId` → `Employee.Id`

---

## **6. الرواتب والمستحقات (Payroll)**

### **Employee → EmployeeDeduction**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة خصومات
- **الربط:** `EmployeeDeduction.EmployeeId` → `Employee.Id`

### **DeductionTypes → EmployeeDeduction**
- **النوع:** One-to-Many
- **العلاقة:** نوع الخصم يُستخدم في عدة خصومات
- **الربط:** `EmployeeDeduction.DeductionTypeId` → `DeductionTypes.Id`

### **Employee → EmployeeEntitlment**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة مستحقات
- **الربط:** `EmployeeEntitlment.EmployeeId` → `Employee.Id`

### **EntitlmentType → EmployeeEntitlment**
- **النوع:** One-to-Many
- **العلاقة:** نوع المستحق يُستخدم في عدة مستحقات
- **الربط:** `EmployeeEntitlment.EntitlmentTypeId` → `EntitlmentType.Id`

### **Employee → EmployeeSalaryArchives**
- **النوع:** One-to-Many
- **العلاقة:** الموظف له عدة أرشيفات رواتب شهرية
- **الربط:** `EmployeeSalaryArchives.EmployeeId` (غير مصرح به FK لكن منطقياً موجود)

### **MassiveActionTransactions → EmployeeDeduction**
- **النوع:** One-to-Many
- **العلاقة:** العملية الجماعية يمكن أن تنشئ عدة خصومات
- **الربط:** `EmployeeDeduction.MassiveActionTransactionId` → `MassiveActionTransactions.Id`

### **MassiveActionTransactions → EmployeeEntitlment**
- **النوع:** One-to-Many
- **العلاقة:** العملية الجماعية يمكن أن تنشئ عدة مستحقات
- **الربط:** `EmployeeEntitlment.MassiveActionTransactionId` → `MassiveActionTransactions.Id`

---

## **7. الطلبات (Requests)**

### **Employee → Request**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يقدم عدة طلبات
- **الربط:** `Request.EmployeeId` → `Employee.Id`

### **RequestType → Request**
- **النوع:** One-to-Many
- **العلاقة:** نوع الطلب يُستخدم في عدة طلبات
- **الربط:** `Request.RequestTypeId` → `RequestType.Id`

### **RequestStatus → Request**
- **النوع:** One-to-Many
- **العلاقة:** حالة الطلب تُستخدم في عدة طلبات
- **الربط:** `Request.RequestStatusId` → `RequestStatus.Id`

### **Assets → Request**
- **النوع:** One-to-Many
- **العلاقة:** الأصل (سيارة مثلاً) يُستخدم في عدة طلبات
- **الربط:** `Request.AssetId` → `Assets.Id`

### **Request → RequestAttachment**
- **النوع:** One-to-Many
- **العلاقة:** الطلب له عدة مرفقات
- **الربط:** `RequestAttachment.RequestId` → `Request.Id`

### **Request → RequestApprovalMatrix**
- **النوع:** One-to-Many
- **العلاقة:** الطلب يمر بعدة مراحل موافقة
- **الربط:** `RequestApprovalMatrix.RequestId` → `Request.Id`

### **Employee → RequestApprovalMatrix**
- **النوع:** One-to-Many
- **العلاقة:** الموظف (المدير) يستقبل عدة طلبات للموافقة
- **الربط:** `RequestApprovalMatrix.ReceiverId` → `Employee.Id`

### **RequestCategory → RequestType**
- **النوع:** One-to-Many
- **العلاقة:** تصنيف الطلب يحتوي على عدة أنواع طلبات
- **الربط:** `RequestType.RequestCategoryId` → `RequestCategory.Id`

### **Position → RequestTypeConfigs**
- **النوع:** One-to-Many
- **العلاقة:** المنصب له عدة إعدادات للطلبات (مصفوفة الموافقات)
- **الربط:** `RequestTypeConfigs.PositionId` → `Position.Id`

### **RequestType → RequestTypeConfigs**
- **النوع:** One-to-Many
- **العلاقة:** نوع الطلب له عدة إعدادات حسب المناصب
- **الربط:** `RequestTypeConfigs.RequestTypeId` → `RequestType.Id`

---

## **8. الأصول (Assets)**

### **AssetCategory → Assets**
- **النوع:** One-to-Many
- **العلاقة:** تصنيف الأصل يحتوي على عدة أصول
- **الربط:** `Assets.AssetCategoryId` → `AssetCategory.Id`

### **AssetCategory → AssetCategory (Self-Reference)**
- **النوع:** One-to-Many (Hierarchical)
- **العلاقة:** تصنيف رئيسي يحتوي على تصنيفات فرعية
- **الربط:** `AssetCategory.MainAssetId` → `AssetCategory.Id`

### **AssetStatusId → Assets**
- **النوع:** One-to-Many
- **العلاقة:** حالة الأصل تُستخدم في عدة أصول
- **الربط:** `Assets.AssetStatusId` → `AssetStatusId.Id`

### **Employee → Assets**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يُسند له عدة أصول
- **الربط:** `Assets.EmployeeId` → `Employee.Id`

### **Assets → Assets (Self-Reference)**
- **النوع:** One-to-Many (Hierarchical)
- **العلاقة:** أصل رئيسي له أصول فرعية (سيارة وملحقاتها)
- **الربط:** `Assets.ParentAssetId` → `Assets.Id`

### **Assets → RelatedAssets**
- **النوع:** One-to-Many
- **العلاقة:** الأصل له عدة أصول مرتبطة
- **الربط:** `RelatedAssets.AssetId` → `Assets.Id`

### **Assets → AssetAttachment**
- **النوع:** One-to-Many
- **العلاقة:** الأصل له عدة مرفقات
- **الربط:** `AssetAttachment.AssetId` → `Assets.Id`

---

## **9. القضايا والشكاوى (Issues)**

### **Employee → Issue**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يقدم عدة قضايا/شكاوى
- **الربط:** `Issue.EmployeeId` → `Employee.Id`

### **AgainestType → Issue**
- **النوع:** One-to-Many
- **العلاقة:** نوع الجهة المشكو منها يُستخدم في عدة قضايا
- **الربط:** `Issue.AgainestTypeId` → `AgainestType.Id`

### **IssueType → Issue**
- **النوع:** One-to-Many
- **العلاقة:** نوع القضية يُستخدم في عدة قضايا
- **الربط:** `Issue.IssueTypeId` → `IssueType.Id`

### **IssueStatus → Issue**
- **النوع:** One-to-Many
- **العلاقة:** حالة القضية تُستخدم في عدة قضايا
- **الربط:** `Issue.IssueStatusId` → `IssueStatus.Id`

### **Issue → IssueAttachment**
- **النوع:** One-to-Many
- **العلاقة:** القضية لها عدة مرفقات
- **الربط:** `IssueAttachment.IssueId` → `Issue.Id`

### **Issue → IssueExcuter**
- **النوع:** One-to-Many
- **العلاقة:** القضية يتم تعيين عدة منفذين للتعامل معها
- **الربط:** `IssueExcuter.IssueId` → `Issue.Id`

### **AspNetUsers → IssueExcuter**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم (المنفذ) يُسند له عدة قضايا
- **الربط:** `IssueExcuter.ExcuterId` → `AspNetUsers.Id`

### **Issue → IssueComment**
- **النوع:** One-to-Many
- **العلاقة:** القضية لها عدة تعليقات
- **الربط:** `IssueComment.IssueId` → `Issue.Id`

### **AspNetUsers → IssueComment**
- **النوع:** One-to-Many
- **العلاقة:** المستخدم يكتب عدة تعليقات على القضايا
- **الربط:** `IssueComment.IssueExcuterId` → `AspNetUsers.Id`

---

## **10. المهام (Tasks)**

### **Tasks → TaskAssignments**
- **النوع:** One-to-Many
- **العلاقة:** المهمة يُسند لها عدة موظفين
- **الربط:** `TaskAssignments.TaskId` → `Tasks.Id`

### **Employee → TaskAssignments**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يُسند له عدة مهام
- **الربط:** `TaskAssignments.EmployeeId` → `Employee.Id`

### **Tasks → ToDoItems**
- **النوع:** One-to-Many
- **العلاقة:** المهمة تحتوي على عدة بنود فرعية
- **الربط:** `ToDoItems.TaskId` → `Tasks.Id`

### **Employee → ToDoItems**
- **النوع:** One-to-Many
- **العلاقة:** الموظف مسؤول عن عدة بنود
- **الربط:** `ToDoItems.EmployeeId` → `Employee.Id`

### **TaskStatus → Tasks**
- **النوع:** One-to-Many
- **العلاقة:** حالة المهمة تُستخدم في عدة مهام
- **الربط:** `Tasks.StatusId` → `TaskStatus.Id`

### **TaskPriority → Tasks**
- **النوع:** One-to-Many
- **العلاقة:** أولوية المهمة تُستخدم في عدة مهام
- **الربط:** `Tasks.PriorityId` → `TaskPriority.Id`

### **Branch → Tasks**
- **النوع:** One-to-Many
- **العلاقة:** الفرع له عدة مهام
- **الربط:** `Tasks.BranchId` → `Branch.Id`

### **Teams → Tasks**
- **النوع:** One-to-Many
- **العلاقة:** الفريق له عدة مهام
- **الربط:** `Tasks.TeamId` → `Teams.Id`

### **Tasks → TaskAttachments**
- **النوع:** One-to-Many
- **العلاقة:** المهمة لها عدة مرفقات
- **الربط:** `TaskAttachments.TaskId` → `Tasks.Id`

### **Tasks ↔ Department (عبر TaskDepartments)**
- **النوع:** Many-to-Many
- **جدول الربط:** `TaskDepartments`
- **العلاقة:** المهمة يمكن أن تكون مشتركة بين عدة أقسام
- **الربط:**
  - `TaskDepartments.TaskId` → `Tasks.Id`
  - `TaskDepartments.DepartmentId` → `Department.Id`

### **Tasks → ArchivedTasks**
- **النوع:** One-to-Many
- **العلاقة:** المهمة يمكن أرشفتها عدة مرات
- **الربط:** `ArchivedTasks.TaskId` → `Tasks.Id`

---

## **11. الفرق (Teams)**

### **Teams → TeamLocations**
- **النوع:** One-to-Many
- **العلاقة:** الفريق له عدة مواقع عمل
- **الربط:** `TeamLocations.TeamId` → `Teams.Id`

### **Teams → Employee**
- **النوع:** One-to-Many
- **العلاقة:** الفريق يضم عدة موظفين
- **الربط:** `Employee.TeamId` → `Teams.Id`

---

## **12. المسارات والعملاء (Paths & Customers)**

### **Employee ↔ Paths (عبر EmployeePaths)**
- **النوع:** Many-to-Many
- **جدول الربط:** `EmployeePaths`
- **العلاقة:** الموظف يُسند له عدة مسارات، والمسار يمكن أن يُسند لعدة موظفين
- **الربط:**
  - `EmployeePaths.EmployeeId` → `Employee.Id`
  - `EmployeePaths.PathsId` → `Paths.Id`

### **Customers ↔ Paths (عبر CustomerPath)**
- **النوع:** Many-to-Many
- **جدول الربط:** `CustomerPath`
- **العلاقة:** العميل موجود في عدة مسارات، والمسار يحتوي على عدة عملاء
- **الربط:**
  - `CustomerPath.CustomerId` → `Customers.Id`
  - `CustomerPath.PathsId` → `Paths.Id`

### **Customers → Customers (Self-Reference)**
- **النوع:** One-to-Many
- **العلاقة:** عميل رئيسي له عملاء فرعيين (تاجر جملة وتجار تجزئة)
- **الربط:** `Customers.RelatedToId` → `Customers.Id`

---

## **13. المدفوعات والمستحقات المالية (Payables & Receivables)**

### **Customers → Payables**
- **النوع:** One-to-Many
- **العلاقة:** العميل له عدة مستحقات مالية (ديون)
- **الربط:** `Payables.CustomerId` → `Customers.Id`

### **Employee → Payables**
- **النوع:** One-to-Many
- **العلاقة:** الموظف (المندوب) يسجل عدة مستحقات
- **الربط:** `Payables.EmployeeId` → `Employee.Id`

### **Paths → Payables**
- **النوع:** One-to-Many
- **العلاقة:** المسار له عدة مستحقات
- **الربط:** `Payables.PathId` → `Paths.Id`

### **Payables → Receivables**
- **النوع:** One-to-Many
- **العلاقة:** المستحق المالي يتم تحصيله على دفعات
- **الربط:** `Receivables.PayableId` → `Payables.Id`

### **PaymentTypes → Receivables**
- **النوع:** One-to-Many
- **العلاقة:** طريقة الدفع تُستخدم في عدة تحصيلات
- **الربط:** `Receivables.PaymentTypeId` → `PaymentTypes.Id`

### **Employee → FinancialCustodies**
- **النوع:** One-to-Many
- **العلاقة:** الموظف (أمين الخزينة) له عدة عهد مالية
- **الربط:** `FinancialCustodies.EmployeeId` → `Employee.Id`

### **Employee → BankTransfareFromEmployees**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يقوم بعدة تحويلات بنكية
- **الربط:** `BankTransfareFromEmployees.EmployeeId` → `Employee.Id`

---

## **14. الموردين (Vendors)**

### **VendorTypes → Vendors**
- **النوع:** One-to-Many
- **العلاقة:** نوع المورد يحتوي على عدة موردين
- **الربط:** `Vendors.VendorTypeId` → `VendorTypes.Id`

---

## **15. الإشعارات (Notifications)**

### **Employee → NotificationLog**
- **النوع:** One-to-Many
- **العلاقة:** الموظف يستقبل عدة إشعارات
- **الربط:** `NotificationLog.EmployeeId` (غير مصرح به FK لكن منطقياً موجود)

---

## **16. الدعم والمساعدة (Support)**

### **Support → SupportAttachment**
- **النوع:** One-to-Many
- **العلاقة:** طلب الدعم له عدة مرفقات
- **الربط:** `SupportAttachment.SupportId` → `Support.Id`

### **Help → HelpAttachment**
- **النوع:** One-to-Many
- **العلاقة:** طلب المساعدة له عدة مرفقات
- **الربط:** `HelpAttachment.HelpId` → `Help.Id`

---

## **17. الجداول المرجعية (Lookup Tables)**

جميع الجداول في Schema `[Lookup]` هي جداول مرجعية مثل:
- **Country → City → Zone** (علاقة تسلسلية)
- **Degree, Grade, Language, BloodType, DocumentType...** إلخ

معظم هذه الجداول لها علاقة **One-to-Many** مع الجداول الرئيسية.

### **Country → City**
- **النوع:** One-to-Many
- **العلاقة:** الدولة تحتوي على عدة مدن
- **الربط:** `City.CountryId` → `Country.Id`

### **City → Zone**
- **النوع:** One-to-Many
- **العلاقة:** المدينة تحتوي على عدة مناطق
- **الربط:** `Zone.CityId` → `City.Id`

### **Company → (جميع الجداول)**
- **النوع:** One-to-Many
- **العلاقة:** الشركة تملك بيانات جميع الجداول (Multi-tenancy)
- **الربط:** معظم الجداول لديها `CompanyId` → `Company.Id`

---

## **18. الاشتراكات (Subscriptions)**

### **SubscriptionPlan → Company**
- **النوع:** One-to-Many
- **العلاقة:** خطة الاشتراك يمكن أن تُستخدم لعدة شركات
- **الربط:** `Company.SubscriptionPlanId` → `SubscriptionPlan.Id`

### **Company → CompanySubscriptions**
- **النوع:** One-to-Many
- **العلاقة:** الشركة لها عدة طلبات اشتراك
- **الربط:** `CompanySubscriptions.CompanyId` → `Company.Id`

---

## **19. مستويات الموظفين (Employee Levels)** - تكملة

### **EmployeeLevel ↔ EmployeeLevelSetting (عبر EmployeeLevelSettingAssignment)**

- **النوع:** Many-to-Many
- **جدول الربط:** `EmployeeLevelSettingAssignment`
- **العلاقة:** المستوى الوظيفي له عدة إعدادات (مثل: أيام إجازة سنوية)، والإعداد يمكن أن يُستخدم لعدة مستويات
- **الربط:**
    - `EmployeeLevelSettingAssignment.EmployeeLevelId` → `EmployeeLevel.Id`
    - `EmployeeLevelSettingAssignment.EmployeeLevelSettingId` → `EmployeeLevelSetting.Id`

### **Employee → EmployeeLevel**

- **النوع:** Many-to-One
- **العلاقة:** عدة موظفين ينتمون لمستوى وظيفي واحد
- **الربط:** `Employee.EmployeeLevelId` → `EmployeeLevel.Id`

---

## **20. العمليات الجماعية (Massive Actions)**

### **MassiveActionTransactions → Branch**

- **النوع:** Many-to-One
- **العلاقة:** العملية الجماعية تستهدف فرعاً معيناً (اختياري)
- **الربط:** `MassiveActionTransactions.BranchId` → `Branch.Id`

### **MassiveActionTransactions → Department**

- **النوع:** Many-to-One
- **العلاقة:** العملية الجماعية تستهدف قسماً معيناً (اختياري)
- **الربط:** `MassiveActionTransactions.DepartmentId` → `Department.Id`

### **MassiveActionTransactions → Position**

- **النوع:** Many-to-One
- **العلاقة:** العملية الجماعية تستهدف منصباً معيناً (اختياري)
- **الربط:** `MassiveActionTransactions.PositionId` → `Position.Id`

### **MassiveActionTransactions → Employee**

- **النوع:** Many-to-One
- **العلاقة:** العملية الجماعية تستهدف موظفاً معيناً (اختياري)
- **الربط:** `MassiveActionTransactions.EmployeeId` → `Employee.Id`

**ملاحظة:** العملية الجماعية يمكن أن تستهدف:

- جميع الموظفين في فرع معين
- جميع الموظفين في قسم معين
- جميع الموظفين في منصب معين
- موظف واحد محدد

---

## **21. الإجازات والعطلات (Holidays & Company Off Days)**

### **Company → CompanyOffDays**

- **النوع:** One-to-Many
- **العلاقة:** الشركة لها عدة أيام عطلات رسمية
- **الربط:** `CompanyOffDays.CompanyId` → `Company.Id`

### **Company → Holiday**

- **النوع:** One-to-Many
- **العلاقة:** الشركة تحدد عدة إجازات رسمية (أعياد وطنية/دينية)
- **الربط:** `Holiday.CompanyId` → `Company.Id`

**الفرق بين CompanyOffDays و Holiday:**

- `CompanyOffDays`: أيام عطلات خاصة بالشركة (مثل: يوم تأسيس الشركة)
- `Holiday`: أيام العطلات الرسمية (مثل: عيد الفطر، عيد الأضحى)

---

## **22. الجداول المرجعية التعليمية والمهنية**

### **Degree → EmployeeEducational**

- **النوع:** One-to-Many
- **العلاقة:** الدرجة العلمية (بكالوريوس، ماجستير...) تُستخدم في عدة مؤهلات
- **الربط:** `EmployeeEducational.DegreeId` → `Degree.Id`

### **Grade → EmployeeEducational**

- **النوع:** One-to-Many
- **العلاقة:** التقدير (ممتاز، جيد جداً...) يُستخدم في عدة مؤهلات
- **الربط:** `EmployeeEducational.GradeId` → `Grade.Id`

### **CertificateType → EmployeeCertificate**

- **النوع:** One-to-Many
- **العلاقة:** نوع الشهادة (مهنية، تدريبية...) يُستخدم في عدة شهادات
- **الربط:** `EmployeeCertificate.CertificateTypeId1` → `CertificateType.Id`

### **DocumentType → EmployeeIdentityDocument**

- **النوع:** One-to-Many
- **العلاقة:** نوع الوثيقة (جواز سفر، بطاقة هوية...) يُستخدم في عدة وثائق
- **الربط:** `EmployeeIdentityDocument.DocumentTypeId1` → `DocumentType.Id`

### **Language → EmployeeLanguage**

- **النوع:** One-to-Many
- **العلاقة:** اللغة تُستخدم في عدة سجلات موظفين
- **الربط:** `EmployeeLanguage.LanguageId1` → `Language.Id`

### **SkillsType → EmployeeSkills**

- **النوع:** One-to-Many
- **العلاقة:** نوع المهارة (تقنية، إدارية...) يُستخدم في عدة مهارات
- **الربط:** `EmployeeSkills.SkillTypeId` → `SkillsType.Id`

---

## **23. الجداول المرجعية الصحية والشخصية**

### **BloodType → EmployeeHealthInformation**

- **النوع:** One-to-Many
- **العلاقة:** فصيلة الدم تُستخدم في عدة سجلات صحية
- **الربط:** `EmployeeHealthInformation.BloodTypeId` → `BloodType.Id`

### **LifeStyleType → UserLifeStyle**

- **النوع:** One-to-Many
- **العلاقة:** نوع نمط الحياة (رياضي، مدخن...) يُستخدم في عدة سجلات
- **الربط:** `UserLifeStyle.LifeStyleTypeId` → `LifeStyleType.Id`

### **AttachmentCategory → UserAttachment**

- **النوع:** One-to-Many
- **العلاقة:** تصنيف المرفق (سيرة ذاتية، صورة شخصية...) يُستخدم في عدة مرفقات
- **الربط:** `UserAttachment.AttachmentCategoryId1` → `AttachmentCategory.Id`

---

## **24. الجداول المرجعية للخبرات العملية**

### **BusinessType → EmployeeWorkHistory**

- **النوع:** One-to-Many
- **العلاقة:** نوع الشركة (صناعية، تجارية...) يُستخدم في عدة خبرات عملية
- **الربط:** `EmployeeWorkHistory.BusinessTypeId` → `BusinessType.Id`

### **BusinessSize → EmployeeWorkHistory**

- **النوع:** One-to-Many
- **العلاقة:** حجم الشركة (صغيرة، متوسطة، كبيرة) يُستخدم في عدة خبرات
- **الربط:** `EmployeeWorkHistory.BusinessSizeId` → `BusinessSize.Id`

---

## **25. الجداول المرجعية الجغرافية (تفصيلية)**

### **Country → Company**

- **النوع:** One-to-Many
- **العلاقة:** الدولة تحتوي على عدة شركات
- **الربط:** `Company.CountryId` → `Country.Id`

### **City → Company**

- **النوع:** One-to-Many
- **العلاقة:** المدينة تحتوي على عدة شركات
- **الربط:** `Company.CityId` → `City.Id`

### **Country → UserAddress**

- **النوع:** One-to-Many
- **العلاقة:** الدولة تُستخدم في عدة عناوين
- **الربط:** `UserAddress.CountryId` → `Country.Id`

### **City → UserAddress**

- **النوع:** One-to-Many
- **العلاقة:** المدينة تُستخدم في عدة عناوين
- **الربط:** `UserAddress.CityId` → `City.Id`

### **Zone → UserAddress**

- **النوع:** One-to-Many
- **العلاقة:** المنطقة تُستخدم في عدة عناوين
- **الربط:** `UserAddress.ZoneId` → `Zone.Id`

---

## **26. العملة والشركات (Currency)**

### **Currency → Company**

- **النوع:** One-to-Many
- **العلاقة:** العملة الافتراضية تُستخدم في عدة شركات
- **الربط:** `Company.CurrencyId` → `Currency.Id`

---

## **27. الإشعارات (تفصيلية)**

### **Employee → NotificationLog**

- **النوع:** One-to-Many (محتملة - غير مصرح بها في الـ FK)
- **العلاقة:** الموظف يستقبل عدة إشعارات
- **الربط المنطقي:** `NotificationLog.EmployeeId` → `Employee.Id`
- **ملاحظة:** الحقل موجود في الجدول لكن لا يوجد Foreign Key مصرح به

---

## **28. المرفقات (Attachments)**

### **FileDescriptionType → FileDescription**

- **النوع:** One-to-Many
- **العلاقة:** نوع وصف الملف يحتوي على عدة أوصاف
- **الربط:** `FileDescription.FileDescriptionTypeId` → `FileDescriptionType.Id`

**ملاحظة:** هذه الجداول تبدو غير مستخدمة بكثرة في الـ Schema الحالي

---

## **29. الرسم التنظيمي (Organization Chart)**

### **Company → OrganizationChart**

- **النوع:** One-to-Many
- **العلاقة:** الشركة لها رسم تنظيمي واحد أو عدة رسومات تنظيمية
- **الربط:** `OrganizationChart.CompanyId` → `Company.Id`

**ملاحظة:** جدول `OrganizationChart` يحتوي على:

- `ParentId`: معرف العنصر الأب في الشجرة
- `EntityId`: معرف الكيان (قسم أو منصب أو موظف)
- `Type`: نوع الكيان (Department, Position, Employee)

هذا الجدول يُستخدم لبناء شجرة تنظيمية ديناميكية

---

## **30. علاقات الهوية الإضافية (Identity Additional)**

### **AspNetUsers → AspNetUserClaims**

- **النوع:** One-to-Many
- **العلاقة:** المستخدم له عدة Claims (صلاحيات إضافية)
- **الربط:** `AspNetUserClaims.UserId` → `AspNetUsers.Id`

### **AspNetUsers → AspNetUserLogins**

- **النوع:** One-to-Many
- **العلاقة:** المستخدم له عدة طرق تسجيل دخول خارجية (Google, Facebook...)
- **الربط:** `AspNetUserLogins.UserId` → `AspNetUsers.Id`

### **AspNetUsers → AspNetUserTokens**

- **النوع:** One-to-Many
- **العلاقة:** المستخدم له عدة Tokens (للمصادقة)
- **الربط:** `AspNetUserTokens.UserId` → `AspNetUsers.Id`

---

## **31. علاقات الشركة الذاتية (Company Self-Reference)**

### **Company → Company (Self-Reference)**

- **النوع:** One-to-Many (محتملة)
- **العلاقة:** شركة أم لها عدة شركات فرعية (في حالة المجموعات الكبيرة)
- **الربط:** `Company.CompanyId1` → `Company.Id`
- **ملاحظة:** هذه العلاقة موجودة في الكود لكن غير واضحة الاستخدام

---

## **32. العلاقات المحسوبة (Computed Relationships)**

### **Request.Duration**

- **نوع:** Computed Column
- **الحساب:** عدد أيام العمل بين `StartDate` و `EndDate` باستخدام دالة `GetDayOffCount`
- **الاستخدام:** حساب مدة الإجازة أو الطلب تلقائياً

### **EmployeeAttendanceMains.WorkingHours**

- **نوع:** Computed Column
- **الحساب:** الفرق بين `firstHour` و `lastHour`
- **الاستخدام:** حساب ساعات العمل الفعلية للموظف

---

## **33. العلاقات الاختيارية المهمة (Important Optional Relationships)**

### **Branch → EmployeeAttendanceLocation**

- **النوع:** One-to-Many (اختياري)
- **العلاقة:** الفرع يحدد مواقع الحضور المسموحة للموظفين
- **الربط:** `EmployeeAttendanceLocation.BranchId` → `Branch.Id`

### **Branch → PersonalType**

- **النوع:** One-to-Many
- **العلاقة:** الفرع له عدة أنواع من الأفراد (موظف، متعاقد...)
- **الربط:** `PersonalType.BranchId` → `Branch.Id`

---

## **34. ملخص الأنماط الرئيسية للعلاقات**

### **النمط 1: العلاقات التسلسلية (Hierarchical)**

```
Company → Branch → Department → Position → Employee
Country → City → Zone
AssetCategory → AssetCategory (self)
Position → Position (self)
Assets → Assets (self)
Customers → Customers (self)
```

### **النمط 2: العلاقات الكثيرة لكثير (Many-to-Many)**

```
Employee ↔ Paths (via EmployeePaths)
Customers ↔ Paths (via CustomerPath)
Tasks ↔ Department (via TaskDepartments)
AspNetUsers ↔ AspNetRoles (via AspNetUserRoles)
EmployeeLevel ↔ EmployeeLevelSetting (via EmployeeLevelSettingAssignment)
```

### **النمط 3: علاقات التوثيق والمرفقات (Documentation Pattern)**

```
Request → RequestAttachment
Issue → IssueAttachment
Assets → AssetAttachment
Support → SupportAttachment
Help → HelpAttachment
Tasks → TaskAttachments
AspNetUsers → UserAttachment
```

### **النمط 4: علاقات سير العمل (Workflow Pattern)**

```
Request → RequestApprovalMatrix (مصفوفة الموافقات)
Issue → IssueExcuter → IssueComment (معالجة القضايا)
Tasks → TaskAssignments → ToDoItems (إدارة المهام)
```

### **النمط 5: علاقات Multi-tenancy**

```
Company → [جميع الجداول تقريباً عبر CompanyId]
```

كل جدول تقريباً يحتوي على `CompanyId` لعزل البيانات بين الشركات

---

## **35. الملاحظات الفنية المهمة**

1. **الحقول المحسوبة:** استخدام `COMPUTED COLUMNS` في:
    
    - `Request.Duration`
    - `EmployeeAttendanceMains.WorkingHours`
2. **الدالة المخصصة:** دالة `GetDayOffCount` لحساب أيام العمل (تستثني عطلة نهاية الأسبوع)
    
3. **العلاقات الاختيارية:** معظم Foreign Keys قابلة لأن تكون `NULL` مما يعطي مرونة كبيرة
    
4. **الأمان:** استخدام ASP.NET Identity مع جداول:
    
    - AspNetUsers, AspNetRoles, AspNetUserRoles
    - AspNetUserClaims, AspNetRoleClaims
    - AspNetUserLogins, AspNetUserTokens
5. **Soft Delete Pattern:** معظم الجداول لديها:
    
    - `IsDelete` (bit)
    - `DeletedBy` (int)
    - `DeletedOn` (datetime2)
6. **Audit Trail:** كل جدول لديه:
    
    - `CreatedBy`, `CreatedOn`
    - `DeletedBy`, `DeletedOn`
    - `IsActive`

---

## **36. الجداول غير المربوطة بشكل واضح**

بعض الجداول لها علاقات منطقية لكن بدون Foreign Keys:

- `EmployeeSalaryArchives` → `Employee`
- `NotificationLog` → `Employee`
- `OrganizationChart` (يستخدم EntityId و Type بدلاً من FK مباشر)

---
