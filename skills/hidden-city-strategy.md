---
name: hidden-city-strategy
description: >
  Phân tích cân bằng về hidden city ticketing (mua vé có điểm đến xa hơn nhưng xuống ở điểm quá cảnh để tiết kiệm tiền) — điều kiện áp dụng, rủi ro thực tế, và mức độ enforcement theo từng hãng. Chỉ trigger khi user hỏi trực tiếp về hidden city, "skiplagging", hoặc "xuống ở điểm dừng". KHÔNG bao giờ tự đề xuất kỹ thuật này — chỉ phân tích khi được hỏi. Luôn yêu cầu human review trước khi hành động.
---

# Skill: Hidden City Strategy

Phân tích khách quan về hidden city ticketing — không khuyến khích, không ngăn cản, chỉ cung cấp thông tin đầy đủ để user tự quyết định.

## Định nghĩa

Hidden city ticketing (còn gọi là "skiplagging"): mua vé A → B → C nhưng thực tế chỉ bay đến B, bỏ chặng B → C.

**Ví dụ**: Vé HAN → SEA qua ICN giá $400. Vé HAN → ICN thẳng giá $600. User mua vé $400, xuống tại ICN, bỏ chặng ICN → SEA.

## Bước 1: Kiểm tra điều kiện loại trừ

**Dừng lại và báo rõ lý do nếu user có bất kỳ yếu tố nào sau:**

```python
DISQUALIFIERS = {
    "checked_baggage": {
        "reason": "Hành lý ký gửi sẽ được chuyển đến điểm đến cuối (SEA), "
                  "không phải điểm dừng (ICN). User sẽ không lấy được hành lý.",
        "workaround": "Chỉ carry-on. Nếu cần ký gửi → không áp dụng được."
    },
    "loyalty_points_redemption": {
        "reason": "Dùng miles/điểm để book → hãng theo dõi chặt hơn nhiều. "
                  "Bị phát hiện → mất toàn bộ điểm, khóa tài khoản.",
        "workaround": "Không có. Không áp dụng nếu dùng điểm."
    },
    "company_card_or_policy": {
        "reason": "Nhiều công ty yêu cầu vé thực tế phải match với lịch trình. "
                  "Hidden city = fraud nội bộ, không phải chỉ vi phạm hãng bay.",
        "workaround": "Không có nếu dùng thẻ công ty hoặc có policy nghiêm."
    },
    "return_trip_same_booking": {
        "reason": "Nếu không bay chặng đầu của vé khứ hồi, hãng có thể hủy "
                  "chặng về tự động.",
        "workaround": "Chỉ áp dụng với vé một chiều."
    },
    "airline_app_checked_in": {
        "reason": "Check-in qua app hãng → hãng biết bạn không lên chặng cuối.",
        "workaround": "Check-in tại quầy nếu tiến hành."
    }
}
```

Nếu có bất kỳ disqualifier nào → trả về:
```
⛔ KHÔNG PHÙ HỢP — [Lý do cụ thể]
[Giải thích hậu quả rõ ràng]
Khuyến nghị: không tiến hành. Xem các lựa chọn thay thế: [route-optimization hoặc date-optimization]
```

## Bước 2: Tính tiết kiệm thực tế (nếu đủ điều kiện)

```python
def hidden_city_savings(through_ticket, direct_ticket):
    gross_savings = direct_ticket.price - through_ticket.price
    
    # Rủi ro tài chính tiềm năng
    risks = {
        "missed_connection_cost": through_ticket.price,  # mất toàn bộ nếu bị phát hiện
        "account_ban_value": loyalty_account_value,       # nếu dùng loyalty
        "rebooking_if_caught": market_price_same_day      # vé mua lại last-minute
    }
    
    return {
        "gross_savings": gross_savings,
        "risks": risks,
        "net_expected_value": gross_savings * (1 - enforcement_probability)
    }
```

## Mức độ enforcement theo hãng

| Hãng | Enforcement | Ghi chú |
|------|-------------|---------|
| United, American, Delta | Cao | Theo dõi pattern, kiện Skiplagged.com |
| Southwest | Thấp | Không assigned seats, khó track |
| Spirit, Frontier | Thấp | Ít resources để enforce |
| Vietnam Airlines | Thấp–Trung | Chưa có case công khai |
| Korean Air, Singapore | Trung | Enforce với loyalty members |
| Ryanair, EasyJet | Trung | Tập trung vào assigned seats |

**Lưu ý**: Enforcement tăng lên đáng kể nếu user làm nhiều lần trên cùng hãng hoặc cùng route.

## Output format (chỉ khi đủ điều kiện)

```
🔍 PHÂN TÍCH HIDDEN CITY — [Route]

ĐIỀU KIỆN KIỂM TRA:
✅ Không có hành lý ký gửi
✅ Không dùng thẻ/điểm công ty
✅ Vé một chiều
✅ Không dùng loyalty miles
→ Đủ điều kiện phân tích tiếp

SO SÁNH GIÁ:
  Vé thẳng HAN → ICN:              $600
  Vé through HAN → SEA qua ICN:    $400
  Tiết kiệm gộp:                   $200 (33%)

RỦI RO CỤ THỂ:
  ▪ Hãng phát hiện và deny boarding: thấp (Korean Air = medium enforcement)
  ▪ Nếu bị phát hiện: mất $400, cần mua lại vé last-minute (~$800–1200)
  ▪ Tài khoản KrisFlyer/loyalty bị suspend: cần đánh giá nếu có tài khoản
  ▪ Gate change: nếu ICN → SEA đổi gate cuối, có thể miss

ĐIỀU KIỆN TIÊN QUYẾT NẾU TIẾN HÀNH:
  ☐ Chỉ carry-on, không ký gửi gì
  ☐ Check-in online trước, không qua quầy hãng
  ☐ Không dùng tài khoản loyalty
  ☐ Layover tại ICN phải đủ dài để rời sân bay bình thường
  ☐ Không làm nhiều lần với cùng hãng/tuyến

---
⚠️ HUMAN REVIEW REQUIRED
Skill này chỉ phân tích thông tin — KHÔNG khuyến nghị bạn thực hiện.
Hidden city ticketing vi phạm điều khoản sử dụng của hầu hết các hãng
và có thể dẫn đến hậu quả từ cấm tài khoản đến từ chối vận chuyển.
Quyết định cuối cùng hoàn toàn thuộc về bạn sau khi đã cân nhắc đầy đủ.

Bạn có muốn xem các lựa chọn thay thế hợp pháp với mức tiết kiệm tương tự không?
→ [route-optimization] hoặc [date-optimization]
```

## Lưu ý quan trọng cho skill này

- **Không bao giờ suggest proactively** — chỉ phân tích khi user hỏi trực tiếp
- Luôn đề xuất alternatives hợp pháp ở cuối
- Không đánh giá moral judgment — chỉ cung cấp facts
- Nếu user là minor hoặc context rõ ràng là doanh nghiệp với policy → nhấn mạnh rủi ro nặng hơn
