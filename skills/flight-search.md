---
name: flight-search
description: >
  Tìm kiếm vé máy bay từ nhiều nguồn song song, bao gồm virtual interlining (ghép 2 vé từ 2 hãng khác nhau thành một hành trình rẻ hơn mà không aggregator thông thường nào bán). Dùng khi user muốn tìm vé cụ thể cho một ngày, so sánh tất cả hãng, hỏi giá rẻ nhất cho một chuyến bay cụ thể, hoặc hỏi tại sao giá trên Google Flights khác với nơi khác. Trigger ngay cả khi user chỉ nói "tìm vé", "check giá", "bay tháng X", hoặc "tìm chuyến bay" mà không đề cập kỹ thuật. Luôn chạy song song với date-optimization khi ngày bay chưa cố định.
---

# Skill: Flight Search

Truy vấn song song nhiều nguồn để tìm tổ hợp vé tốt nhất, kể cả các combination ẩn mà OTA thông thường không bán.

## Nguồn dữ liệu theo thứ tự ưu tiên

### Nguồn 1: Amadeus Self-Service API
Phủ toàn bộ GDS — Sabre, Travelport, cùng đa số FSC và LCC lớn.

```http
GET /v2/shopping/flight-offers
  ?originLocationCode=HAN
  &destinationLocationCode=SFO
  &departureDate=2025-08-13
  &adults=1
  &travelClass=ECONOMY
  &currencyCode=USD
  &max=50
  &nonStop=false
```

Lưu ý: VietJet, AirAsia, Scoot có mặt qua GDS nhưng giá có thể cao hơn đặt trực tiếp. Flag các LCC để kiểm tra thêm.

### Nguồn 2: Kiwi Tequila API (virtual interlining)
Chuyên ghép vé nhiều hãng thành hành trình mà không ai bán dưới dạng single ticket.

```http
GET /v2/search
  ?fly_from=HAN
  &fly_to=SFO
  &date_from=13/08/2025
  &date_to=13/08/2025
  &adults=1
  &selected_cabins=M
  &curr=USD
  &limit=50
  &sort=price
  &partner=picky
```

Kiwi trả về `virtual_interlining: true` cho các combination ghép hãng. Filter và label riêng nhóm này.

### Nguồn 3: Direct airline check
Với một số route, đặt thẳng tại hãng rẻ hơn GDS 10–20%:
- VietJet: vietjetair.com (không bán qua GDS đầy đủ)
- AirAsia: airasia.com
- Scoot: flyscoot.com
- Ryanair, EasyJet: luôn đặt trực tiếp

## Chuẩn hóa và dedup

Sau khi thu thập từ tất cả nguồn, thực hiện:

```python
def normalize(offer):
    return {
        "flight_id": f"{carrier}-{flight_no}-{dep_date}",
        "route": segments,           # list of legs
        "total_duration": minutes,
        "stops": len(segments) - 1,
        "base_fare": amount,
        "taxes_fees": amount,
        "total_price": base_fare + taxes_fees,  # LUÔN dùng total_price để sort
        "currency": "USD",
        "baggage_included": {
            "cabin": "7kg" | None,
            "checked": "23kg" | None
        },
        "source": "amadeus" | "kiwi" | "direct",
        "virtual_interlining": bool,
        "book_url": url,
        "expires_at": timestamp
    }
```

**Dedup rule**: Cùng flight number + cùng ngày từ nhiều nguồn → giữ lại giá thấp nhất, ghi nguồn.

## Output format

```
✈️ KẾT QUẢ TÌM KIẾM — HAN → SFO, 13/8/2025 (Economy, 1 khách)
Tìm thấy 23 chuyến từ 3 nguồn · Đã loại 8 trùng lặp

#1  $299  ·  VN + UA (Virtual Interlining ★)
    HAN 06:00 → ICN 13:30 (VN647)
    ICN 16:00 → SFO 09:45+1 (UA892)
    Tổng: 21h45 · 1 điểm dừng · Hành lý: 7kg xách tay, không ký gửi
    Nguồn: Kiwi Tequila · ⚠️ 2 vé riêng, tự lo nếu lỡ chuyến

#2  $412  ·  Korean Air (KE)
    HAN 09:30 → ICN 14:20 (KE693)
    ICN 17:45 → SFO 11:30+1 (KE25)
    Tổng: 19h · 1 điểm dừng · Hành lý: 23kg ký gửi included
    Nguồn: Amadeus

#3  $445  ·  Vietnam Airlines (VN)
    HAN 10:00 → CDG 17:30 (VN7)
    CDG 20:00 → SFO 22:15 (AF66)
    Tổng: 22h · 1 điểm dừng · Hành lý: 23kg ký gửi included
    Nguồn: Amadeus

[... thêm kết quả ...]

⚠️ VIRTUAL INTERLINING — ĐỌC TRƯỚC KHI BOOK:
Chuyến #1 gồm 2 vé riêng. Nếu chuyến đầu trễ và bạn lỡ VN647, Korean Air
KHÔNG có nghĩa vụ đặt lại. Phù hợp nếu: bạn không có hành lý ký gửi,
layover > 3 tiếng, và chấp nhận rủi ro tự xử lý.
```

## Cảnh báo bắt buộc

Luôn hiển thị với virtual interlining kết quả:
- Đây là 2 vé độc lập, không phải 1 itinerary
- Lỡ chuyến đầu = mất vé chuyến sau, không được bồi thường
- Hành lý ký gửi phải claim và check-in lại tại điểm dừng
- Phù hợp nhất khi layover > 3h và không có hành lý ký gửi

## Sau khi có kết quả

Nếu user muốn phân tích sâu hơn về phí ẩn → chuyển sang `fee-analysis`.
Nếu user muốn so sánh lộ trình qua hub khác → chuyển sang `route-optimization`.
