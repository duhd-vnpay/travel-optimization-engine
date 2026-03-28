---
name: flexibility-analysis
description: >
  Phân tích rủi ro và break-even của từng loại vé (refundable, non-refundable, semi-flexible) để giúp user quyết định khi nào nên mua vé linh hoạt và khi nào vé cứng rẻ hơn là đáng. Dùng khi user đang so sánh vé có các điều kiện hoàn/đổi khác nhau, khi lịch trình chưa chắc chắn, hoặc khi muốn hiểu rõ rủi ro thực sự trước khi bấm mua. Trigger ngay khi thấy câu hỏi về "non-refundable", "có thể đổi không", "nếu hủy thì sao".
---

# Skill: Flexibility Analysis

Phân tích định lượng để trả lời câu hỏi: "Vé linh hoạt có đáng tiền hơn không — và khi nào?"

## Thu thập thông tin

```yaml
tickets_to_compare:
  - id: A
    name: "Economy Light (VJ)"
    price: 1800000      # VND hoặc USD
    change_fee: 750000
    cancel_refund: 0    # 0 = không hoàn
    cancel_credit: 0
    change_limit: "đổi 1 lần, trước 4h"
    
  - id: B
    name: "Economy Standard (VN)"
    price: 2400000
    change_fee: 200000
    cancel_refund: 0
    cancel_credit: 1800000  # hoàn credit 75%
    change_limit: "đổi không giới hạn, trước 24h"
    
  - id: C
    name: "Business Flex (VN)"
    price: 6500000
    change_fee: 0
    cancel_refund: 5500000   # hoàn 85%
    cancel_credit: 6500000
    change_limit: "đổi không giới hạn, đến giờ bay"

user_context:
  trip_purpose:       "công tác"    # cá nhân / công tác / kỳ nghỉ
  schedule_certainty: 0.7           # 0=rất không chắc, 1=chắc chắn
  lead_time_days:     30            # đặt trước bao nhiêu ngày
  change_history:     "đổi 2/10 chuyến gần nhất"
```

## Tính điểm linh hoạt

```python
def flexibility_score(ticket):
    """0 = hoàn toàn cứng, 100 = hoàn tiền 100% bất kỳ lúc nào"""
    
    change_score   = 0
    if ticket.change_fee == 0:          change_score = 40
    elif ticket.change_fee < price*0.1: change_score = 25
    elif ticket.change_fee < price*0.3: change_score = 10
    else:                               change_score = 0
    
    cancel_score   = 0
    if ticket.cancel_refund > price*0.8:   cancel_score = 40
    elif ticket.cancel_refund > price*0.5: cancel_score = 25
    elif ticket.cancel_credit > 0:         cancel_score = 15
    else:                                  cancel_score = 0
    
    deadline_score = 0
    if "giờ bay" in ticket.change_limit:  deadline_score = 20
    elif "24h" in ticket.change_limit:    deadline_score = 15
    elif "72h" in ticket.change_limit:    deadline_score = 10
    else:                                 deadline_score = 5
    
    return change_score + cancel_score + deadline_score
```

## Break-even analysis

```python
def break_even_probability(cheap, flex):
    """
    Xác suất phải đổi/hủy tối thiểu để vé linh hoạt đáng tiền hơn.
    
    Nếu p = xác suất phải thay đổi kế hoạch:
    Expected cost(cheap) = cheap.price + p * cheap.change_fee + (1-p)*0
    Expected cost(flex)  = flex.price  + p * flex.change_fee  + (1-p)*0
    
    Break-even khi expected cost bằng nhau.
    """
    price_diff   = flex.price - cheap.price
    penalty_diff = cheap.change_fee - flex.change_fee
    
    if penalty_diff <= 0:
        return None  # vé linh hoạt không bao giờ đáng tiền
    
    return price_diff / penalty_diff   # xác suất cần đạt để justify
```

## Output format

```
⚖️ PHÂN TÍCH RỦI RO VÉ — HAN → SGN, 20/9

VÉ ĐƯỢC SO SÁNH:
                     Economy Light   Economy Flex    Business Flex
Giá                     1.800k          2.400k          6.500k
Điểm linh hoạt           15/100          55/100          92/100
Đổi ngày (phí)           750k            200k            Miễn phí
Hủy hoàn tiền            Không           Không           85% (5.5tr)
Hủy credit               Không           75% (1.8tr)     100%
Deadline đổi/hủy          4h trước        24h trước       Đến giờ bay

KỊCH BẢN RỦI RO:

▪ Đổi ngày ±2 ngày:
  Economy Light:  1.800k + 750k = 2.550k
  Economy Flex:   2.400k + 200k = 2.600k      ← gần bằng nhau
  Business Flex:  6.500k + 0    = 6.500k

▪ Hủy hoàn toàn (không bay):
  Economy Light:  Mất 1.800k
  Economy Flex:   Mất 600k (lấy credit 1.8tr, nhưng phải dùng trong 12 tháng)
  Business Flex:  Mất 1.000k (lấy lại 5.5tr)

▪ No-show (không báo trước):
  Tất cả:         Mất toàn bộ

BREAK-EVEN ANALYSIS:

Light vs Flex:
  Chênh lệch giá: 600k
  Chênh lệch phí đổi: 550k
  → Nếu xác suất đổi vé > 52%: Flex đáng tiền hơn
  → Với lịch sử 2/10 chuyến đổi (20%): Economy Light vẫn hợp lý hơn

Light vs Business:
  Chênh lệch: 4.700k — chỉ đáng nếu xác suất hủy > 65% hoặc đây là 
  chuyến quan trọng không thể miss (ký hợp đồng, đám cưới, phẫu thuật...)

KHUYẾN NGHỊ CHO BẠN:
Với lịch trình công tác thường xuyên và 20% lịch sử đổi vé:
→ Economy Flex (2.4tr) là lựa chọn tốt nhất
→ Credit 1.8tr khi hủy đủ để bù đắp chênh lệch nếu phải thay đổi
→ Tránh Economy Light cho chuyến quan trọng dù rẻ hơn 600k

ĐIỀU KHOẢN ẨN CẦN CHÚ Ý:
⚠️ Credit Economy Flex phải dùng trong 12 tháng, chỉ dùng cho VietJet
⚠️ Đổi ngày Economy Light chỉ được 1 lần duy nhất
⚠️ "Trước 4h" = 4h trước giờ khởi hành, không phải 4h trước check-in
```

## Lưu ý cuối

- Verify điều khoản cụ thể tại trang web hãng — phí hay thay đổi theo route và thời điểm
- Một số thẻ tín dụng (Amex Platinum, một số Visa Infinite) có trip delay insurance giúp giảm rủi ro khi dùng vé cứng
- Với chuyến công tác quan trọng: luôn chọn vé có khả năng đổi ít nhất 1 lần
