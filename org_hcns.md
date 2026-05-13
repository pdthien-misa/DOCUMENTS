# API bên thứ 3 cần cung cấp — CCTC Getting Started

> **PBI liên quan:**
> - [604083 — Mua mới: KH có DL cũ (Mimosa/QLTS/SalaGov) — Step 1 Khai báo CCTC](http://tfs2017-app:8080/tfs/DefaultCollection/MISAPlatform/_workitems/edit/604083)
> - [604919 — Mua thêm: KH đang dùng nền tảng, nâng cấp thêm Mimosa/QLTS/SalaGov — Khai báo CCTC](http://tfs2017-app:8080/tfs/DefaultCollection/MISAPlatform/_workitems/edit/604919)
>
> **Tính năng:** Getting Started Step 1 — "Lấy từ <<tên SP cũ>>"  
> **Model tham chiếu:** `OrganizationUnit.cs`, `OrganizationUnitDTO.cs`

---

## API duy nhất cần thiết

### Lấy danh sách Cơ cấu tổ chức

```
GET /api/organization-units
```

Trả về **mảng phẳng** toàn bộ (hoặc một phần theo filter) phòng ban/đơn vị của tenant. Core sẽ tự lọc theo `BudgetCode` và dựng cây.

---

## Request

| Parameter | Type | Bắt buộc | Mô tả |
|---|---|---|---|
| `TenantID` | `string (Guid)` | ✅ | ID tenant trên app lẻ |
| `BudgetCodes` | `string[]` | ❌ | Danh sách mã QHNS cần lọc. Nếu truyền vào, chỉ trả về phòng ban thuộc các mã QHNS này (và tổ tiên của chúng để dựng cây). Nếu bỏ trống → trả về toàn bộ. |


---

## Response

### Fields bắt buộc — tất cả app

| Key | Type | Mô tả | Model field |
|---|---|---|---|
| `OrganizationUnitID` | `string (Guid)` | ID phòng ban | `OrganizationUnit.OrganizationUnitID` |
| `OrganizationUnitCode` | `string` | Mã phòng ban/đơn vị | `OrganizationUnit.OrganizationUnitCode` |
| `OrganizationUnitName` | `string` | Tên phòng ban/đơn vị | `OrganizationUnit.OrganizationUnitName` |
| `ParentID` | `string (Guid)?` | ID cha — dùng để dựng cây cha-con | `OrganizationUnit.ParentID` |
| `BudgetCode` | `string` | **Mã QHNS** — key mapping với Core | `OrganizationUnit.BudgetCode` |
| `SortOrder` | `int?` | Thứ tự sắp xếp đã khai báo trong app lẻ | `OrganizationUnit.SortOrder` |
| `Inactive` | `bool` | Trạng thái ngừng theo dõi | `OrganizationUnit.Inactive` |

### Fields bổ sung cho (iHOS)

| Key | Type | Mô tả | Model field |
|---|---|---|---|
| `BedCount` | `int?` | Số giường bệnh | `OrganizationUnit.BedCount` |

---

## Ví dụ Response

```json
[
  {
    "OrganizationUnitID": "a1b2c3d4-...",
    "OrganizationUnitCode": "PB001",
    "OrganizationUnitName": "Phòng Tài chính Kế toán",
    "ParentID": null,
    "BudgetCode": "1040101",
    "SortOrder": 1,
    "Inactive": false,
    "BedCount": null
  },
  {
    "OrganizationUnitID": "e5f6a7b8-...",
    "OrganizationUnitCode": "PB002",
    "OrganizationUnitName": "Khoa Nội",
    "ParentID": "a1b2c3d4-...",
    "BudgetCode": "1040102",
    "SortOrder": 1,
    "Inactive": false,
    "BedCount": 30
  }
]
```

## Lưu ý quan trọng

- `BudgetCode` (Mã QHNS) là field **then chốt** — Core dùng để mapping phòng ban từ app lẻ vào đúng vị trí trong cây CCTC. Nếu app lẻ dùng tên key khác, cần convert sang `BudgetCode` trước khi trả về.
- Trả về **toàn bộ cây** (kể cả phòng ban không có `BudgetCode`) để Core tự lọc và dựng cây đúng.
- `ParentID = null` nghĩa là phòng ban gốc (root).
- Với iGOV xã: cần bao gồm cả các phòng ban chuyên môn (PBCM) thuộc xã.
- Nếu app lẻ không có `OrganizationUnitID` dạng Guid, có thể dùng ID nội bộ — Core sẽ không lưu lại ID này mà chỉ dùng để dựng cây trong phiên làm việc.
