---
name: route-optimization
description: >
  Tìm và so sánh lộ trình bay qua các hub chiến lược rẻ hơn đường bay thẳng, tính tỷ lệ tiết kiệm trên mỗi giờ quá cảnh thêm. Dùng khi user hỏi "bay thẳng hay có điểm dừng rẻ hơn", "quá cảnh ở đâu rẻ nhất", "đường bay nào tiết kiệm nhất", hoặc khi chênh lệch giá bay thẳng và có điểm dừng cần được phân tích. Trigger ngay khi route có nhiều lựa chọn hub.
---

# Skill: Route Optimization

Phân tích và so sánh các lộ trình thay thế qua hub chiến lược để tìm điểm cân bằng tối ưu giữa giá và thời gian.

## Hub chiến lược theo khu vực

Đọc `references/hub-airports.md` để có danh sách đầy đủ. Các hub quan trọng nhất:

**Châu Á → Mỹ:**
- ICN (Seoul) — thường rẻ hơn HND/NRT 15–25%
- TPE (Taipei) — rẻ với China Airlines / EVA Air
- HKG (Hong Kong) — Cathay Pacific, tốt cho premium
- SIN (Singapore) — Singapore Airlines, tốt cho long-haul
- PVG/PEK (Trung Quốc) — rẻ nhất nhưng transit visa phức tạp

**Châu Á → Châu Âu:**
- IST (Istanbul) — Turkish Airlines, thường rẻ nhất
- DOH (Doha) — Qatar Airways, tốt cho premium
- DXB (Dubai) — Emirates, tốt nhưng ít rẻ hơn IST/DOH
- HEL (Helsinki) — Finnair, châu Á Bắc Âu → Tây Âu

**Nội Bộ:**
- Hub "thứ hai" của hãng thường rẻ hơn hub chính (VD: Manchester thay vì LHR)

## Quy trình phân tích

### 1. Thiết lập baseline

```
Baseline = lộ trình thẳng hoặc kết nối nhanh nhất
           (không nhất thiết là bay thẳng không điểm dừng)

baseline_price:    $1,240
baseline_duration: 14h30
```

### 2. Tìm lộ trình thay thế

Với mỗi hub tiềm năng, truy vấn `flight-search` để lấy:
- Giá all-in
- Tổng thời gian (bao gồm layover)
- Số điểm dừng
- Yêu cầu transit visa

Tối đa 5 lộ trình thay thế để không overwhelm user.

### 3. Tính metrics so sánh

```python
for route in alternatives:
    savings_usd   = baseline_price - route.price
    savings_pct   = savings_usd / baseline_price * 100
    extra_time_h  = (route.duration - baseline_duration) / 60
    value_ratio   = savings_usd / extra_time_h   # USD tiết kiệm / giờ thêm

    # Cờ hiệu
    route.flags = []
    if route.requires_transit_visa:   route.flags.append("⚠️ cần transit visa")
    if route.layover < 90:            route.flags.append("⚠️ layover ngắn")
    if route.requires_ground_transit: route.flags.append("🚌 cần tàu/xe đất liền")
    if extra_time_h < 2:              route.flags.append("✅ thời gian chấp nhận được")
```

## Output format

```
🗺️ PHÂN TÍCH LỘTRÌNH — SFO → CDG (Economy, 1 khách, 15/9)

BASELINE — BAY THẲNG / NHANH NHẤT
  Air France: SFO → CDG (thẳng)
  Giá: $1,240 · Thời gian: 10h30 · Điểm dừng: 0

LỘ TRÌNH THAY THẾ:

#1 ★ GIÁ TRỊ TỐT NHẤT          qua LHR (London Heathrow)
   British Airways: SFO → LHR → CDG
   Giá: $890  ·  Tiết kiệm: $350 (28%)
   Thời gian: 13h15  ·  Thêm: +2h45  ·  Layover: 1h45 tại LHR
   Giá trị: $127/giờ thêm
   ✅ Layover đủ dài · ✅ Không cần transit visa (Schengen → UK OK)

#2 qua IST (Istanbul)
   Turkish Airlines: SFO → IST → CDG
   Giá: $780  ·  Tiết kiệm: $460 (37%)
   Thời gian: 18h45  ·  Thêm: +8h15  ·  Layover: 5h30 tại IST
   Giá trị: $56/giờ thêm
   ✅ Không cần transit visa · ℹ️ Layover dài, có thể nghỉ tại IST miễn phí (Turkish lounge)

#3 qua CDG qua AMS (Amsterdam → Paris train)     🚌 đất liền
   KLM: SFO → AMS + Thalys: AMS → CDG
   Giá: $710  ·  Tiết kiệm: $530 (43%)
   Thời gian: 15h + 1h25 tàu  ·  Thêm: +6h
   ⚠️ Cần book tàu riêng ($45–80) · Không phù hợp nếu có hành lý nhiều

#4 qua DXB (Dubai)
   Emirates: SFO → DXB → CDG
   Giá: $920  ·  Tiết kiệm: $320 (26%)
   Thời gian: 21h30  ·  Thêm: +11h  ·  Layover: 3h tại DXB
   Giá trị: $29/giờ thêm
   ✅ Lounge Emirates tốt cho layover dài

TỔNG HỢP:
Nếu bạn có thể thêm 2h45 → Chọn #1 (British Airways qua London): $127/giờ = đáng nhất
Nếu muốn tiết kiệm tối đa và linh hoạt thời gian → Chọn #2 (Turkish): $780
Nếu thích bay thẳng và không muốn phức tạp → Baseline Air France: $1,240
```

## Lưu ý

- **Transit visa**: kiểm tra kỹ với passport của user. Hộ chiếu VN cần visa transit UK, Canada, Úc.
- **Minimum connection time (MCT)**: LHR = 60 phút, IST = 45 phút, ICN = 45 phút. Layover ngắn hơn MCT → rủi ro cao.
- Sau phân tích, nếu user chọn lộ trình có điểm dừng → chạy `fee-analysis` để verify tổng chi phí thực.
