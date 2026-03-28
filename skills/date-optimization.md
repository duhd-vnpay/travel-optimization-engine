---
name: date-optimization
description: >
  Quét ma trận giá theo ngày để tìm tổ hợp ngày đi + ngày về rẻ nhất trong một khoảng thời gian linh hoạt. Dùng skill này khi người dùng hỏi "ngày nào bay rẻ nhất", "có thể đi sớm hơn/trễ hơn vài ngày không", "so sánh giá cả tuần", hoặc khi ngày bay chưa cố định và user muốn tối ưu theo giá. Đừng chờ user dùng từ "date matrix" — bất kỳ câu hỏi nào về linh hoạt ngày bay đều nên trigger skill này.
---

# Skill: Date Optimization

Phân tích ma trận giá vé để xác định tổ hợp ngày đi + ngày về tối ưu nhất trong khoảng thời gian linh hoạt.

## Input cần có

```
route:        HAN → SFO          # hoặc bất kỳ cặp IATA nào
target_date:  2025-08-15         # ngày mong muốn ban đầu
flexibility:  ±7 ngày            # khoảng tìm kiếm (mặc định ±7)
trip_length:  7 ngày             # số đêm (nếu là khứ hồi)
cabin:        economy            # economy / business / first
passengers:   1 người lớn
```

## Quy trình thực hiện

### 1. Xây dựng date matrix

Tạo lưới ngày với:
- **Hàng** = ngày đi (target ± flexibility)
- **Cột** = ngày về (ngày đi + trip_length ± 3)
- **Ô** = giá all-in thấp nhất tìm được cho tổ hợp đó

Nếu có Amadeus Date Matrix API: gọi `GET /shopping/flight-dates` với `origin`, `destination`, `departureDate`.

Nếu không có API thực: xây dựng bảng dựa trên pattern giá lịch sử (thứ 3, thứ 4, thứ 7 thường rẻ hơn thứ 6, CN 15–30%).

### 2. Xác định TOP 3 tổ hợp

Không chỉ lấy 3 giá thấp nhất. Ưu tiên theo tiêu chí sau:

```
score = savings_vs_target * 0.6 + flexibility_score * 0.2 + convenience_score * 0.2
```

- `savings_vs_target`: % tiết kiệm so với ngày mong muốn ban đầu
- `flexibility_score`: khoảng cách với ngày gốc (càng gần càng điểm cao)
- `convenience_score`: tránh redeye, tránh đổi chuyến nhiều lần

### 3. Output format

Trả về bảng so sánh và 3 khuyến nghị:

```
📅 DATE MATRIX — HAN → SFO (Economy, 1 khách)
Baseline (ngày bạn muốn: 15/8): $879

ĐI\VỀ    | 21/8  | 22/8  | 23/8  | 24/8  | 25/8
---------|-------|-------|-------|-------|------
12/8     | $412  | $398  | $445  | $412  | $489
13/8     | $456  | $441  | $398  | [299] | $412   ← BEST
14/8     | $512  | $467  | $445  | $398  | $423
15/8     | $879  | $834  | $867  | $812  | $834   ← baseline
16/8     | $934  | $879  | $823  | $845  | $867

TOP 3 KHUYẾN NGHỊ:

#1 ★ TIẾT KIỆM NHẤT
   Đi: Thứ 4, 13/8 — Về: Thứ 3, 21/8
   Giá all-in: $299 (tiết kiệm $580 = 66% so với baseline)
   Đổi lịch: sớm hơn 2 ngày đi, muộn hơn 1 ngày về

#2 ★ CÂN BẰNG TỐT
   Đi: Thứ 3, 12/8 — Về: Thứ 4, 22/8
   Giá all-in: $398 (tiết kiệm $481 = 55%)
   Đổi lịch: sớm hơn 3 ngày đi, cùng ngày về

#3 ★ ÍT ĐỔI LỊCH NHẤT
   Đi: Thứ 5, 14/8 — Về: Thứ 3, 21/8
   Giá all-in: $398 (tiết kiệm $481 = 55%)
   Đổi lịch: sớm hơn 1 ngày đi, cùng ngày về
```

## Lưu ý quan trọng

- Giá trong ma trận là **estimate** nếu không có live API — user cần verify trên booking site trước khi quyết định
- Thứ 3, Thứ 4 thường rẻ nhất với long-haul international. Thứ 6, CN đắt nhất.
- Mùa hè (Jun–Aug) và Tết tây (Dec 20–Jan 5): matrix toàn bộ sẽ cao hơn 40–80% so với off-peak
- Sau khi user chọn tổ hợp, chuyển sang `flight-search` để tìm chuyến cụ thể tốt nhất trong ngày đó
