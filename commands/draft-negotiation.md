---
name: draft-negotiation
description: >
  Soạn nhanh email thương lượng giá với hãng bay. Dùng khi user muốn có email sẵn sàng gửi mà không cần điền template thủ công.
---

# Command: /draft-negotiation

Soạn email thương lượng corporate rate với hãng bay trong vài phút.

## Cách dùng

```
/draft-negotiation
```

## Flow

Hỏi 4 câu nhanh:

```
📧 DRAFT NEGOTIATION EMAIL

1. Tên, chức vụ, công ty của bạn?
   > ___

2. Hãng bay muốn thương lượng?
   > ___

3. Số chuyến/năm và tổng chi phí ước tính?
   > ___

4. Tình huống: lần đầu tiếp cận / có đề xuất từ hãng khác / renewal?
   > ___
```

→ Gọi `negotiation-email`, trả về email hoàn chỉnh với subject line, nội dung, và gợi ý người nhận.
