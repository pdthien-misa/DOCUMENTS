# API Spec: check-arise — Kiểm tra phát sinh dữ liệu tại Subapp

**PBI**: [#594103](http://tfs2017-app:8080/tfs/defaultcollection/MISAPlatform/_workitems?id=594103&_a=edit) — Xóa CCTC, Chức vụ, Chức danh, CBNV đã phát sinh tại các subapp  
**Created**: 2026-06-30

---

## Bối cảnh

Core gọi API này **trước khi thực hiện xóa** để xác định đối tượng có phát sinh dữ liệu tại subapp hay không.

- Nếu subapp trả về `HasArise = true` → Core từ chối xóa, hiển thị thông báo lỗi.
- Nếu subapp trả về `HasArise = false` → Core tiến hành xóa bình thường.
- Nếu subapp **timeout / không phản hồi** → Core coi như `HasArise = true` (an toàn, từ chối xóa).

---

## Endpoint

```
POST /api/check-arise
```

---

## Request

### Headers

| Header | Giá trị |
|---|---|
| `Content-Type` | `application/json` |
| `x-clientid` | `string` — Client ID của subapp |
| `x-clientsecret` | `string` — Client Secret của subapp |

### Body

```json
{
  "BudgetCode": "string",
  "TenantID": "string (GUID)",
  "Type": "integer (enum)",
  "ObjectID": "string (GUID)"
}
```

### Field Description

| Field | Type | Required | Mô tả |
|---|---|---|---|
| `BudgetCode` | `string` | ✅ | Mã ngân sách — sử dụng cho các subapp HCSN (Hành chính sự nghiệp) |
| `TenantID` | `string (GUID)` | ✅ | Giá trị `TenantID` của Platform |
| `Type` | `integer (enum)` | ✅ | Loại đối tượng cần kiểm tra. Xem giá trị bên dưới. |
| `ObjectID` | `string (GUID)` | ✅ | ID của đối tượng cần kiểm tra (ID CCTC / Chức vụ / Chức danh / CBNV trên Core) |

### Enum `Type`

| Giá trị | Đối tượng |
|---|---|
| `1` | CCTC — Cơ cấu tổ chức (phòng ban, đơn vị) |
| `2` | Chức vụ |
| `3` | Chức danh |
| `4` | CBNV — Cán bộ nhân viên |

---

## Response

### 200 OK — Có phát sinh dữ liệu

```json
{
  "HasArise": true
}
```

### 200 OK — Chưa phát sinh dữ liệu

```json
{
  "HasArise": false
}
```

### Response Fields

| Field | Type | Mô tả |
|---|---|---|
| `HasArise` | `boolean` | `true` nếu đã có dữ liệu phát sinh, `false` nếu chưa |

---

## Error Responses

| HTTP Code | Trường hợp |
|---|---|
| `400 Bad Request` | Request thiếu field bắt buộc hoặc `Type` không hợp lệ |
| `401 Unauthorized` | `x-clientid` / `x-clientsecret` không hợp lệ |
| `500 Internal Server Error` | Subapp lỗi nội bộ |

> **Timeout behavior**: Core đặt timeout khi gọi API này. Nếu subapp không phản hồi trong thời gian quy định → Core xử lý như `HasArise = true`.

---

## Ví dụ — Kiểm tra CCTC

**Request:**

```http
POST /api/check-arise
Content-Type: application/json
x-clientid: your-client-id
x-clientsecret: your-client-secret

{
  "BudgetCode": "BVND",
  "TenantID": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "Type": 1,
  "ObjectID": "a1b2c3d4-0000-0000-0000-000000000001"
}
```

**Response (đã phát sinh):**

```json
{
  "HasArise": true
}
```

**Response (chưa phát sinh):**

```json
{
  "HasArise": false
}
```
