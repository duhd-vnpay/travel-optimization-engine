---
name: optimize-flight
description: >
  Slash command khởi động toàn bộ quy trình tối ưu vé máy bay. Người dùng nhập /optimize-flight và trả lời các câu hỏi ngắn, orchestrator sẽ tự động chọn và chạy các sub-skills phù hợp. Phù hợp khi muốn bắt đầu từ đầu mà không cần biết skill nào để dùng.
---

# Command: /optimize-flight

Khởi động quy trình tối ưu vé từ đầu với giao diện hỏi–đáp nhanh.

## Cách dùng

```
/optimize-flight
```

Hoặc với thông tin sẵn:
```
/optimize-flight HAN SFO 2025-08-13 1 economy
```

## Flow

### Nếu không có arguments — hỏi tuần tự:

```
✈️ TRAVEL OPTIMIZER — Bắt đầu

Tôi sẽ hỏi bạn 5 câu nhanh để tìm phương án tối ưu nhất.

1. Bay từ đâu → đến đâu? (VD: Hà Nội → San Francisco)
   > ___

2. Ngày bay dự kiến? Và có linh hoạt ±5–7 ngày không?
   > ___

3. Một chiều hay khứ hồi? Bao nhiêu ngày?
   > ___

4. Bao nhiêu người? Hạng vé?
   > ___

5. Hành lý: chỉ xách tay / có ký gửi / chưa biết?
   > ___
```

### Sau khi có đủ thông tin:

Gọi orchestrator với đầy đủ context → orchestrator chọn sub-skills phù hợp.

Thứ tự mặc định:
1. `date-optimization` (nếu ngày linh hoạt)
2. `flight-search` (tìm chuyến cụ thể)
3. `fee-analysis` (tính true_total)
4. Tổng hợp top 3 recommendation

### Kết quả trả về:

```
✈️ KẾT QUẢ TỐI ƯU — HAN → SFO

KHUYẾN NGHỊ TỐT NHẤT:
  Korean Air qua ICN
  13/8 đi · 21/8 về · Economy · 1 khách
  Giá all-in: $412 (hành lý 23kg included)
  
SO VỚI BASELINE (15/8 bạn muốn ban đầu):
  Tiết kiệm: $467 (53%)
  Đánh đổi: đi sớm 2 ngày, về muộn 1 ngày

CÒN 2 PHƯƠNG ÁN KHÁC:
  #2 Virtual interlining $299 · rủi ro cao hơn · không ký gửi
  #3 Economy Flex $456 · đổi vé miễn phí · phù hợp nếu lịch chưa chắc

Bạn muốn tìm hiểu thêm về phương án nào?
```
