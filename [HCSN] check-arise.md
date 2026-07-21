# Webhook API — Kiểm tra phát sinh dữ liệu (CCTC, Chức vụ, Chức danh, CBNV) trên subapp

**PBI**: [#594103](http://tfs2017-app:8080/tfs/defaultcollection/MISAPlatform/_workitems?id=594103&_a=edit) — Xóa CCTC, Chức vụ, Chức danh, CBNV đã phát sinh tại các subapp  
**Created**: 2026-06-30

---

## Bối cảnh

Khi có thao tác xóa CCTC, Chức vụ, Chức danh, CBNV cần validate xem các subapp có phát sinh dữ liệu không. Nếu có phát sinh → không được thao tác.

Do vậy, các subapp cần cung cấp **1 Webhook API** để MPL gọi khi có thao tác cần kiểm tra.

**Trên dữ liệu:** CCTC, Chức vụ, Chức danh, CBNV

 
---

## Endpoint

```
POST /api/CheckArise
```

---

## Request

### Headers

| Name | Value |
|---|---|
| `x-clientid` | ClientId cấp cho ứng dụng khi đăng ký vào AMIS Platform. Ứng dụng validate key này trùng với key được cung cấp. |
| `x-clientsecret` | ClientSecret cấp cho ứng dụng khi đăng ký vào AMIS Platform. Ứng dụng validate key này trùng với key được cung cấp. |

### Query Parameters

| Name | Type | Required | Mô tả |
|---|---|---|---|
| `tenantID` | `string (GUID)` | ✅ | ID của công ty cần kiểm tra |
| `type` | `string (enum)` | ✅ | Loại dữ liệu cần kiểm tra (xem bảng dưới) |
| `state` | `integer` | ✅ | Trạng thái dữ liệu. `3`: Xóa |

### Enum `type`

| Giá trị | Đối tượng |
|---|---|
| `Employee` | Nhân viên |
| `OrganizationUnit` | Cơ cấu tổ chức (phòng ban, đơn vị) |
| `JobPosition` | Chức vụ |
| `JobTitle` | Chức danh |

### Body

```
string — chuỗi serialize dữ liệu cần validate
```

**Sửa CCTC:** `List<GUID>` — danh sách ID các bản ghi cần kiểm tra.

**Xóa các loại dữ liệu:** `List<GUID>` — danh sách ID các bản ghi cần kiểm tra.

---

## Response

### Body

```json
DICTIONARY<GUID, BOOL>
```

| Key | Value | Mô tả |
|---|---|---|
| `GUID` | Key | ID Khóa chính của dữ liệu cần validate |
| `BOOL` | Value | `TRUE`: Đã có phát sinh ↔ Không cho phép sửa/xóa. `FALSE`: Chưa phát sinh ↔ Cho phép sửa/xóa |

---

## Ví dụ

### Kiểm tra xóa CCTC

**Request:**

```http
POST /api/webhook/check-arise?tenantID=3fa85f64-5717-4562-b3fc-2c963f66afa6&type=OrganizationUnit&state=3
Content-Type: application/json
x-clientid: your-client-id

["a1b2c3d4-0000-0000-0000-000000000001", "b2c3d4e5-0000-0000-0000-000000000002"]
```

**Response (có phát sinh):**

```json
{
  "a1b2c3d4-0000-0000-0000-000000000001": true,
  "b2c3d4e5-0000-0000-0000-000000000002": false
}
```
