---
name: fee-analysis
description: >
  Bóc tách tất cả phí ẩn (hành lý, chọn ghế, đổi vé, ưu tiên lên máy bay) và tính true_total thực sự phải trả, sau đó xếp hạng lại các lựa chọn theo giá thực. Dùng khi user muốn so sánh vé "rẻ" từ LCC với vé FSC, khi có cảm giác bị bất ngờ bởi phí lúc checkout, hoặc khi cần biết tổng chi phí thực của một chuyến bay. Trigger ngay khi thấy hãng LCC (Spirit, Frontier, VietJet, AirAsia, Ryanair, v.v.) trong kết quả.
---

# Skill: Fee Analysis

Tính true_total thực phải trả bằng cách cộng tất cả phí áp dụng theo profile của user, rồi xếp hạng lại để so sánh công bằng.

## Thu thập user profile

Trước khi phân tích, cần biết:

```yaml
baggage:
  cabin_bag:   "7kg xách tay"    | "không có" | "15kg xách tay"
  checked:     "23kg 1 kiện"     | "không cần"
seat_preference: "bất kỳ"       | "cửa sổ"   | "lối đi" | "extra legroom"
travel_frequency: "thỉnh thoảng" | "thường xuyên" | "doanh nghiệp"
loyalty_status:  none | silver  | gold | platinum   # với hãng cụ thể
payment_method:  "Visa/Master"   | "Amex"     | "thẻ hãng" | "tiền mặt"
changes_likely:  false           | true
```

## Database phí theo hãng

Đọc `references/airline-fee-database.md` để có số liệu đầy đủ. Tổng hợp nhanh:

### Hành lý xách tay (cabin bag)
| Hãng | Included | Phí thêm |
|------|----------|----------|
| Vietnam Airlines | 10kg | — |
| Bamboo Airways | 7kg | — |
| VietJet Air | Không | $10–25 |
| AirAsia | Không | $8–20 |
| Spirit | Không | $40–79 |
| Frontier | Không | $35–65 |
| Southwest | 1 free | — |
| Korean Air | 12kg | — |

### Hành lý ký gửi (checked bag, 23kg)
| Hãng | Route | Giá |
|------|-------|-----|
| VietJet | Nội địa | $15–25 |
| AirAsia | Nội địa | $12–20 |
| Spirit | Nội địa US | $35–69 |
| Frontier | Nội địa US | $35–60 |
| Southwest | Nội địa US | Free (2 kiện) |
| Korean Air | International | Free (1 kiện) |
| Vietnam Airlines | International | Free (1 kiện) |

### Chọn ghế
| Hãng | Standard | Extra legroom |
|------|----------|---------------|
| VietJet | $3–8 | $15–30 |
| AirAsia | $4–10 | $20–40 |
| Spirit | $5–15 | $25–50 |
| Korean Air | Free | $35–80 |
| Vietnam Airlines | Free (online check-in) | $20–50 |

### Phí đổi/hủy vé
| Loại | Spirit | VietJet | Vietnam Airlines | Korean Air |
|------|--------|---------|-----------------|-----------|
| Đổi ngày | $99–119 | $30–50 | $0–50 | $0–75 |
| Hủy hoàn tiền | Không | Không (Eco) | $50–100 | $75–150 |
| Hủy credit | $69–119 | $20–30 | $0 | $0 |

## Tính true_total

```python
def calculate_true_total(offer, user_profile):
    base = offer.base_fare
    taxes = offer.taxes_fees
    
    # Hành lý xách tay
    cabin_fee = 0
    if not offer.includes_cabin_bag and user_profile.needs_cabin_bag:
        cabin_fee = airline_fee_db[offer.carrier]["cabin_bag"]
    
    # Hành lý ký gửi
    checked_fee = 0
    if not offer.includes_checked and user_profile.needs_checked:
        checked_fee = airline_fee_db[offer.carrier]["checked_23kg"]
    
    # Ghế
    seat_fee = 0
    if user_profile.seat_preference != "bất kỳ":
        seat_fee = airline_fee_db[offer.carrier][user_profile.seat_preference]
    
    # Payment surcharge (một số hãng tính thêm với Visa/credit)
    payment_fee = 0
    if offer.carrier in LCC_WITH_PAYMENT_FEE:
        payment_fee = offer.base_fare * 0.02  # ~2%
    
    return base + taxes + cabin_fee + checked_fee + seat_fee + payment_fee
```

## Output format

```
💰 PHÂN TÍCH PHÍ THỰC — HAN → SGN (ngày 20/9)
Profile: 1 hành khách · 7kg xách tay · 23kg ký gửi · ghế bất kỳ

                    VietJet     Bamboo      Vietnam Air
─────────────────────────────────────────────────────
Base fare           $45         $72         $89
Thuế & phí          $12         $15         $18
Hành lý XTay       +$18         $0          $0
Hành lý ký gửi     +$22         $0          $0
Chọn ghế           +$5          $0          $0
Phí thanh toán     +$2          $0          $0
─────────────────────────────────────────────────────
TRUE TOTAL         $104         $87         $107
Xếp hạng            #2          #1          #3
─────────────────────────────────────────────────────

⚠️ ĐẢO THỨ HẠNG: VietJet (quảng cáo $45) thực ra đắt hơn Bamboo ($72)
sau khi tính đủ phí theo nhu cầu của bạn.

CHIẾN LƯỢC GIẢM PHÍ:
→ Nếu dùng thẻ tín dụng VPBank hoặc Vietcombank, VietJet miễn phí payment surcharge
→ Nếu chỉ cần xách tay, VietJet giảm còn $77 (vẫn đắt hơn Bamboo $87 một chút)
→ Bamboo có điểm số tốt nhất với profile hành lý đầy đủ của bạn
```

## Cảnh báo kích thước xách tay

Nếu user muốn tránh phí ký gửi bằng cách đóng gói vào xách tay, kiểm tra giới hạn:

| Hãng | Kích thước tối đa | Cân nặng |
|------|--------------------|----------|
| VietJet | 56×36×23 cm | 7kg |
| AirAsia | 56×36×23 cm | 7kg |
| Spirit | 45×35×20 cm | không giới hạn |
| Vietnam Airlines | 56×36×23 cm | 10kg |
| Korean Air | 55×40×20 cm | 12kg |

Đề xuất: nếu cần 10kg xách tay → Vietnam Airlines hoặc Korean Air, tránh Spirit/VietJet.
