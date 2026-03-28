---
name: negotiation-email
description: >
  Soạn email thương lượng giá corporate với hãng bay — đúng người nhận, chiến thuật đàm phán, tạo urgency. Dùng khi user muốn giảm giá corporate, thương lượng hợp đồng, hoặc đàm phán điều khoản bay.
---

# Skill: Negotiation Email

Soạn email thương lượng giá với hãng bay — đúng chiến thuật đàm phán, đúng người nhận, tạo urgency mà không để lộ điểm yếu.

## Thu thập thông tin trước khi soạn

```yaml
sender:
  name:         "Nguyễn Văn A"
  title:        "Trưởng phòng Hành chính"
  company:      "Công ty ABC"
  email:        "nva@abc.com.vn"

travel_volume:
  flights_per_year:   120        # số chuyến/năm
  total_spend_usd:    85000      # hoặc ước tính
  main_routes:        ["HAN-SGN", "HAN-SIN", "SGN-KUL"]
  avg_lead_time:      "14 ngày"  # đặt vé trước bao lâu

current_situation:
  current_carrier:    "Vietnam Airlines"
  current_rate:       "công khai, không có ưu đãi"
  competitor_price:   "$X thấp hơn" | "Bamboo đang offer Y"
  loyalty_status:     "Lotusmiles Silver" | "none"

ask:
  discount_target:    "10–15%"
  other_terms:        ["waive change fee", "priority boarding", "lounge access"]
  timeline:           "quyết định trong 2 tuần"
```

## Nguyên tắc đàm phán

### Không bao giờ:
- Tiết lộ mức giá tối đa chấp nhận được
- Nói "chúng tôi cần hãng của bạn" — luôn có alternative
- Xin xỏ — đây là giao dịch B2B, không phải ân huệ
- Gửi cho người sai (không gửi cho customer service — gửi cho Sales/Corporate Accounts)

### Đảm bảo chính xác:
- Mọi số liệu trong email (số chuyến, tổng spend, giá competitor) PHẢI là thông tin thật từ user
- Không phóng đại hoặc bịa số — thông tin sai sẽ ảnh hưởng uy tín công ty khi hãng verify
- Nếu user không có số liệu cụ thể, gợi ý ước tính hợp lý và label "ước tính" trong email

### Luôn:
- Dùng số liệu cụ thể (số chuyến, tổng spend) — không nói chung chung
- Đề cập competitor như một thực tế, không phải đe dọa
- Đặt deadline phản hồi rõ ràng để tạo urgency thật
- Để ngỏ cửa cho counter-offer

## CC và người nhận phù hợp

**CC gợi ý theo tình huống:**
- CC giám đốc tài chính / CFO khi email > $50k/năm (tạo weight)
- CC trưởng phòng liên quan nếu họ là decision maker thực sự
- Không CC quá nhiều người — tối đa 2, tránh tạo cảm giác committee

## Người nhận phù hợp

| Tình huống | Gửi cho |
|------------|---------|
| Lần đầu, spend < $50k/năm | Corporate Sales Manager |
| Spend $50k–$200k/năm | Head of Corporate Accounts |
| Spend > $200k/năm | Director of Commercial / VP Sales |
| Đang có hợp đồng, muốn renegotiate | Account Manager hiện tại |
| Không biết ai | sales@[hãng].com hoặc corporate@[hãng].com |

Vietnam Airlines corporate: corporate@vietnamairlines.com  
Bamboo: corporate@bambooairways.com  
Singapore Airlines: siacorporate@singaporeair.com.sg

## Template theo tình huống

### Tình huống 1: Lần đầu tiếp cận (không có hợp đồng)

```
Subject: Corporate Account Inquiry — [Công ty] | [Số chuyến]/năm | [Tuyến chính]

Kính gửi [Tên] / Phòng Corporate Sales [Hãng bay],

Tôi là [Tên], [Chức vụ] tại [Công ty] — [mô tả ngắn công ty, 1 dòng].

Chúng tôi hiện có [X] chuyến bay/năm trên các tuyến [Tuyến 1], [Tuyến 2], 
tổng chi phí vé khoảng [Số tiền]/năm. Phần lớn trong số này đang book 
qua [Kênh hiện tại] theo giá thị trường.

Chúng tôi đang đánh giá lại nhà cung cấp vận tải cho năm tới. Giá thị 
trường hiện tại với [Hãng cạnh tranh] trên cùng tuyến dao động 
[X–Y USD/chuyến]. Trước khi đưa ra quyết định, tôi muốn tìm hiểu 
xem [Hãng] có thể cung cấp điều kiện nào cho volume của chúng tôi.

Cụ thể, chúng tôi quan tâm đến:
- Mức giá ưu đãi cho Economy và/hoặc Business trên [Tuyến chính]
- Chính sách đổi vé linh hoạt (không tính phí hoặc phí thấp)
- Điều kiện tích điểm cho tập thể hoặc chuyển điểm cá nhân

Nếu [Hãng] có chương trình corporate phù hợp, tôi mong nhận được 
phản hồi trước [Ngày — 10 ngày từ hôm nay] để kịp đưa vào kế hoạch 
ngân sách Q[X].

Trân trọng,
[Tên] | [Chức vụ]
[Công ty] | [Email] | [SĐT]
```

### Tình huống 2: Có đề xuất từ đối thủ, muốn counter

```
Subject: Re: Corporate Rate — [Hãng] vs Competing Offer | [Công ty]

Kính gửi [Tên],

Cảm ơn anh/chị đã phản hồi proposal trước đó. Chúng tôi đang trong 
giai đoạn đánh giá cuối và đã nhận được đề xuất cụ thể từ [Hãng B].

Chúng tôi vẫn ưu tiên tiếp tục với [Hãng] vì [lý do thật — dịch vụ, 
lịch bay, loyalty program] — nhưng cần con số thuyết phục hơn để 
trình ban lãnh đạo phê duyệt.

Với volume [X chuyến/năm] và cam kết [Y tháng], đề nghị anh/chị 
xem xét lại có thể đưa ra mức nào tốt hơn cho Economy [Tuyến chính].

Tôi cần phản hồi trước [Ngày] để kịp trình duyệt nội bộ.

Trân trọng, [Tên]
```

### Tình huống 3: Renewal — đang có hợp đồng, muốn điều kiện tốt hơn

```
Subject: Contract Renewal Discussion — [Công ty] | [Hãng] Corporate Account

Kính gửi [Account Manager],

Hợp đồng corporate hiện tại của chúng tôi sẽ hết hạn vào [Ngày]. 
Trong [X] tháng qua, chúng tôi đã thực hiện [N] chuyến, tổng 
[Số tiền], đúng cam kết volume.

Chúng tôi muốn tiếp tục hợp tác — nhưng muốn review lại điều kiện 
cho năm tới, cụ thể:
1. Giảm thêm [X]% so với hiện tại (thị trường đã thay đổi)
2. Nâng giới hạn đổi vé miễn phí từ [X] lên [Y] lần/tháng
3. Thêm [Điều kiện khác]

Tôi mong sắp xếp một cuộc gặp ngắn [30 phút] trong tuần tới để 
thảo luận. Anh/chị có rảnh [ngày] hoặc [ngày] không?

Trân trọng, [Tên]
```

## Sau khi gửi email

- **Follow up**: nếu không có phản hồi sau 5 ngày làm việc, gửi 1 email follow-up ngắn
- **Không chờ quá 2 lần**: nếu sau 2 lần vẫn không có phản hồi → chuyển sang đàm phán với hãng khác
- **Ghi lại tất cả**: mọi offer bằng văn bản, kể cả email, trước khi ký bất kỳ hợp đồng nào
