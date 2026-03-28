---
name: analyze-fees
description: >
  Phân tích nhanh phí ẩn của một vé cụ thể và tính giá thực phải trả. Dùng khi user đang xem một vé cụ thể và muốn biết true_total trước khi mua.
---

# Command: /analyze-fees

Phân tích nhanh true_total cho một vé đang xem xét.

## Cách dùng

```
/analyze-fees [hãng] [giá hiển thị] [hành lý cần]
```

Ví dụ:
```
/analyze-fees VietJet 850000 "7kg xách tay + 23kg ký gửi"
/analyze-fees Spirit $89 "carry-on + checked"
```

## Flow

Thu thập thêm nếu cần:
- Tuyến bay (để biết phí chính xác)
- Loại vé (Eco Light / Eco / Flex)
- Ghế ưu tiên không?

→ Gọi `fee-analysis` và trả kết quả nhanh trong 1 block.
