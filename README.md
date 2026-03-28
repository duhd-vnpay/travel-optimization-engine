# ✈️ Travel Optimization Engine

**Cowork Plugin · 8 AI Skills · Tối ưu chi phí vé máy bay end-to-end**

> Từ $879 xuống $299 trong 20 phút — không VPN, không điểm thưởng, không mánh lới.

---

## Cài đặt

### Cách 1 — Upload ZIP (mọi paid plan)

1. Tải [`travel-optimization-plugin.zip`](https://github.com/duhd-vnpay/travel-optimization-engine/archive/refs/heads/main.zip)
2. Mở **Claude Desktop** → **Cowork** → **Customize** → **+** → **Upload plugin**
3. Chọn file ZIP → plugin active ngay

### Cách 2 — Org Marketplace từ GitHub *(Team / Enterprise)*

```
Organization Settings → Plugins → + → Add marketplace from GitHub
Repository: duhd-vnpay/travel-optimization-engine
```

Bật **Sync automatically** để nhận update mỗi khi có commit mới.

---

## Slash Commands

| Command | Mô tả |
|---------|-------|
| `/optimize-flight` | Khởi động pipeline đầy đủ — hỏi 5 câu rồi chạy tất cả skills phù hợp |
| `/analyze-fees` | Tính true_total nhanh cho một vé đang xem, phát hiện phí ẩn |
| `/draft-negotiation` | Soạn email thương lượng corporate rate, sẵn sàng gửi |

---

## 8 Skills

| Skill | Kích hoạt khi | Tiết kiệm |
|-------|--------------|----------|
| `date-optimization` | Ngày bay chưa cố định, muốn biết ngày nào rẻ nhất | ±30% |
| `flight-search` | Tìm vé cụ thể, bao gồm virtual interlining Amadeus + Kiwi | 30–40% |
| `route-optimization` | So sánh bay thẳng vs quá cảnh qua hub chiến lược | $200–400 |
| `deals-verification` | Tìm mã giảm giá, promo hiện hành kèm confidence rating | 5–20% |
| `fee-analysis` | Bóc tách phí ẩn LCC, tính true_total, re-rank kết quả | Re-rank |
| `negotiation-email` | Soạn email B2B thương lượng giá với hãng bay | 10–15% |
| `flexibility-analysis` | Break-even refundable vs non-refundable, phân tích rủi ro | Break-even |
| `hidden-city-strategy` | Phân tích hidden city khi được hỏi trực tiếp (không tự đề xuất) | Phân tích |

Skills tự kích hoạt theo ngữ cảnh — không cần gọi tên skill, chỉ cần nói tự nhiên.

---

## Cấu trúc

```
travel-optimization-engine/
├── .claude-plugin/
│   └── plugin.json              # Manifest
├── skills/
│   ├── orchestrator.md          # Router chính
│   ├── date-optimization.md
│   ├── flight-search.md
│   ├── route-optimization.md
│   ├── deals-verification.md
│   ├── fee-analysis.md
│   ├── negotiation-email.md
│   ├── flexibility-analysis.md
│   └── hidden-city-strategy.md
├── commands/
│   ├── optimize-flight.md
│   ├── analyze-fees.md
│   └── draft-negotiation.md
├── agents/
│   └── flight-optimizer.md      # Sub-agent pipeline song song
└── references/
    ├── hub-airports.md           # Transit visa + MCT table
    ├── airline-fee-database.md   # 8 hãng, phí đầy đủ
    └── api-endpoints.md          # Amadeus + Kiwi API spec
```

---

## Yêu cầu

- Claude Desktop (macOS hoặc Windows)
- Paid plan: Pro / Max / Team / Enterprise
- Cowork phải được bật (research preview)

---

## Phù hợp với ai

| Đối tượng | Lợi ích chính |
|-----------|--------------|
| Cá nhân & gia đình | Tiết kiệm 30–66% so với đặt thẳng không có chiến lược |
| Doanh nghiệp | Giảm 10–15% ngân sách công tác, báo cáo kiểm toán được |
| Freelancer / Remote | Phân tích rủi ro vé linh hoạt, tối ưu lộ trình qua hub |

---

## Lưu ý

- Giá trong phân tích là **estimate** nếu không cấu hình Amadeus/Kiwi API key thật — luôn verify trên booking site trước khi thanh toán
- `hidden-city-strategy` chỉ phân tích khi user hỏi trực tiếp, không bao giờ tự đề xuất
- Plugin không tự đặt vé — bước thanh toán luôn do user thực hiện

---

## Tác giả

<table>
  <tr>
    <td width="80">
      <img src="https://avatars.githubusercontent.com/u/237002926?v=4" width="64" height="64" style="border-radius:50%" alt="Du Hoang Dinh"/>
    </td>
    <td>
      <strong>Du Hoang Dinh</strong><br/>
      <a href="https://github.com/duhd-vnpay">@duhd-vnpay</a> · VNPAY · Vietnam<br/><br/>
      Xây dựng Travel Optimization Engine như một thử nghiệm thực tế về việc dùng AI Agent để giải quyết bài toán thực — tối ưu chi phí vé máy bay cho cá nhân và doanh nghiệp.
    </td>
  </tr>
</table>

---

## License

MIT — tự do sử dụng, chỉnh sửa, và phân phối.
