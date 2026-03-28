---
name: deals-verification
description: >
  Tổng hợp mã giảm giá, promo code, và deal hiện hành cho vé máy bay, kèm điều kiện áp dụng và mức độ tin cậy. Dùng khi user hỏi "có mã giảm giá không", "làm sao tìm promo", "deal tháng này có gì", hoặc trước khi checkout để kiểm tra nhanh. Trigger ngay cả khi user chỉ hỏi chung chung về cách tiết kiệm thêm sau khi đã có vé.
---

# Skill: Deals Verification

Tổng hợp và đánh giá deals, mã giảm giá, và promo hiện hành — giúp user biết cái nào đáng thử trước khi vào checkout.

## Phân loại deals theo nguồn

### Loại 1: Sale chính thức từ hãng
Hãng thường công bố sale theo lịch có thể dự đoán:
- **Vietnam Airlines**: Flash sale thường vào thứ 3, thứ 4 hàng tuần
- **VietJet**: "Siêu sale" không báo trước, thường từ 0h–6h
- **Bamboo**: Sale cuối tháng
- **Singapore Airlines**: KrisFlyer Spontaneous Escapes (thứ 6)
- **Korean Air**: eSaver fare (thứ 4–thứ 5)
- **Thai Airways**: Royal Orchid Sale (periodic)

### Loại 2: Mã giảm giá OTA
Các OTA thường có mã chạy liên tục nhưng có điều kiện:
- **Traveloka**: HEMAT10, TRAVELOKA15 (thường 10–15% cho app)
- **Booking.com**: GENIUS (cần level 2+)
- **Expedia**: EXTRA10 (first booking), app-exclusive
- **Agoda**: Agoda Cash, Flash Deals

### Loại 3: Thẻ tín dụng & ngân hàng
- VPBank: hoàn tiền 5–8% vé máy bay
- Techcombank Visa: ưu đãi với Vietnam Airlines
- VIB: hoàn tiền với đặt qua app
- Amex (quốc tế): Fine Hotels, Platinum travel credit

### Loại 4: Loyalty program & miles
- Bông Sen Vàng (VN): upgrade, companion fare
- LotusMiles (VJ): đổi điểm lấy vé
- KrisFlyer (SQ): award chart favorable với Asia routes

## Output format

```
🎟️ DEALS & MÃ GIẢM GIÁ — [Hãng/Tuyến bay]
Cập nhật dựa trên kiến thức đến [knowledge cutoff]. Luôn verify tại checkout.

DEALS CHÍNH THỨC ĐANG BIẾT:

[HIGH CONFIDENCE ✓]
Vietnam Airlines Flash Sale
  Giảm: 20–40% economy, nội địa và một số quốc tế
  Điều kiện: đặt thứ 3–4 hàng tuần, bay trong vòng 30 ngày
  Cách dùng: đặt trực tiếp trên vietnamairlines.com
  Độ tin cậy: Cao — chương trình thường xuyên, lịch sử ổn định

[MEDIUM CONFIDENCE ~]
Traveloka HEMAT10
  Giảm: 10% tối đa 200,000đ
  Điều kiện: đặt lần đầu qua app, Economy, nội địa VN
  Cách dùng: nhập tại checkout trước khi thanh toán
  Độ tin cậy: Trung bình — mã hay thay đổi, cần verify

[LOW CONFIDENCE ?]
KrisFlyer Spontaneous Escapes
  Giảm: đến 30% cho member SQ
  Điều kiện: công bố thứ 6, bay cuối tuần, cần đăng nhập KrisFlyer
  Cách dùng: tự apply khi đăng nhập, không cần mã
  Độ tin cậy: Thấp — không xác nhận được tuần này có hay không

KHÔNG TÌM THẤY MÃ ĐÁN TIN CẬY CHO:
  ☐ Chặng HAN–SFO trực tiếp
  ☐ Business class tháng 8

---
⚠️ DISCLAIMER QUAN TRỌNG:
Tất cả thông tin trên dựa trên kiến thức huấn luyện và pattern lịch sử — 
KHÔNG được xác minh qua live booking API. Mã có thể đã hết hạn hoặc 
thay đổi điều kiện. Luôn kiểm tra tại checkout trước khi hoàn tất thanh toán.
```

## Chiến lược tìm deal chủ động

Gợi ý user các bước có thể làm ngay:

1. **Google Flights → "Explore"**: tìm deal từ sân bay gần nhất không có điểm đến cố định
2. **Secretflying.com / thepointsguy.com**: tổng hợp error fare và flash sale real-time
3. **Thiết lập price alert**: Google Flights, Hopper, Kayak Explore cho route cụ thể
4. **Đặt qua app mobile**: nhiều hãng có giá thấp hơn 5–10% so với website desktop
5. **Incognito mode**: một số site tăng giá khi detect repeat visit (mức độ ảnh hưởng thực tế còn tranh cãi)

## Sau khi có mã/deal

Nếu user muốn verify tổng chi phí sau khi áp mã → chạy `fee-analysis` với giá sau discount.
