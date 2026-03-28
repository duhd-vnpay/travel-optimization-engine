# ✈️ Travel Optimization Engine

**Cowork Plugin · 8 AI Skills · Tối ưu chi phí vé máy bay end-to-end**

> Từ $879 xuống $299 trong 20 phút — không VPN, không điểm thưởng, không mánh lới.

---

## Cài đặt

### Cách 1 — Claude CLI *(nhanh nhất, mọi paid plan)*

```bash
# Cài Claude CLI nếu chưa có
npm install -g @anthropic-ai/claude-code

# Add marketplace từ GitHub repo này
claude plugin marketplace add duhd-vnpay/travel-optimization-engine

# Cài plugin
claude plugin install travel-optimization-engine@travel-optimization-engine
```

Kiểm tra đã cài thành công:

```bash
claude plugin list
# travel-optimization-engine  v1.0.0  ✔ enabled
```

---

### Cách 2 — Cài local từ source *(phát triển / tùy chỉnh)*

```bash
# Clone repo
git clone https://github.com/duhd-vnpay/travel-optimization-engine.git
cd travel-optimization-engine

# Add marketplace từ thư mục local
claude plugin marketplace add .

# Cài plugin
claude plugin install travel-optimization-engine@local-travel-plugins
```

---

### Cách 3 — Org Marketplace *(Team / Enterprise)*

```
Organization Settings → Plugins → + → Add marketplace from GitHub
Repository: duhd-vnpay/travel-optimization-engine
```

Bật **Sync automatically** để nhận update mỗi khi có commit mới.

---

### Cách 4 — Claude.ai Chat qua Projects *(không cần cài đặt)*

Dùng được ngay trên **claude.ai** mà không cần CLI hay Cowork — thông qua tính năng **Projects**.

#### Bước 1: Tạo Project

1. Vào **claude.ai** → **Projects** (sidebar trái) → **New project**
2. Đặt tên: `Travel Optimizer`

#### Bước 2: Upload files tham chiếu

Trong Project → **Add content** → Upload các file sau từ repo:

| File | Mục đích |
|------|----------|
| `references/airline-fee-database.md` | Bảng phí 8 hãng |
| `references/hub-airports.md` | Hub + transit visa + MCT |
| `references/api-endpoints.md` | Hướng dẫn API (nếu dùng) |

Tải trực tiếp:
```
https://github.com/duhd-vnpay/travel-optimization-engine/archive/refs/heads/main.zip
```
Giải nén → upload 3 file trong thư mục `references/`.

#### Bước 3: Set Project Instructions

Trong Project → **Project instructions** → dán nội dung sau:

```
Bạn là travel optimization specialist với đầy đủ kiến thức về:
- Tối ưu ngày bay qua date matrix (thứ 3/4 rẻ hơn ~15%)
- Tìm virtual interlining qua Kiwi Tequila
- So sánh lộ trình qua hub chiến lược (ICN, IST, TPE...)
- Bóc tách phí ẩn LCC để tính true_total thực phải trả
- Phân tích break-even refundable vs non-refundable
- Soạn email thương lượng corporate rate B2B
- Phân tích hidden city (CHỈ khi được hỏi trực tiếp)

Nguyên tắc:
- Luôn tính giá all-in (base + thuế + hành lý + ghế)
- Không bao giờ báo giá base fare mà không có cảnh báo
- Nếu không có API thực, nói rõ đây là estimate và yêu cầu user verify
- Không đặt vé thay user — chỉ phân tích và đề xuất
- Tham chiếu các file đã upload để tra phí và hub chính xác

Khi nhận câu hỏi về vé máy bay, thu thập đủ: điểm đi/đến, ngày, số người,
hành lý, độ linh hoạt — trước khi phân tích.
```

#### Bước 4: Bắt đầu dùng

Chat tự nhiên trong Project, ví dụ:
- *"Tôi muốn bay HAN → Tokyo tháng 5, ngày nào rẻ nhất?"*
- *"So sánh VietJet 850k với Bamboo 1.1tr nếu tôi cần 23kg ký gửi"*
- *"Soạn email thương lượng giá với Vietnam Airlines, công ty tôi bay 80 chuyến/năm"*

> **Lưu ý:** Chế độ này không có slash commands (`/optimize-flight`...). Thay vào đó dùng ngôn ngữ tự nhiên — Claude sẽ tự áp dụng đúng skill theo ngữ cảnh.

---

### So sánh các cách cài đặt

| | Claude Chat (Projects) | Claude Code CLI | Cowork Plugin | Org Marketplace |
|---|---|---|---|---|
| Yêu cầu | Pro plan | Node.js + CLI | Cowork preview | Team/Enterprise |
| Cài đặt | Không cần | 3 lệnh | 3 lệnh | Admin setup |
| Slash commands | ✗ | ✓ | ✓ | ✓ |
| Tự động update | ✗ (manual) | ✗ | ✗ | ✓ |
| Phù hợp | Dùng nhanh | Dev/cá nhân | Power user | Tổ chức |

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
| `flight-search` | Tìm vé cụ thể, bao gồm virtual interlining qua Kiwi + Skyscanner | 30–40% |
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
│   ├── plugin.json              # Manifest
│   └── marketplace.json         # Local marketplace config
├── skills/
│   ├── orchestrator/SKILL.md    # Router chính
│   ├── date-optimization/SKILL.md
│   ├── flight-search/SKILL.md
│   ├── route-optimization/SKILL.md
│   ├── deals-verification/SKILL.md
│   ├── fee-analysis/SKILL.md
│   ├── negotiation-email/SKILL.md
│   ├── flexibility-analysis/SKILL.md
│   └── hidden-city-strategy/SKILL.md
├── commands/
│   ├── optimize-flight.md
│   ├── analyze-fees.md
│   └── draft-negotiation.md
├── agents/
│   └── flight-optimizer.md      # Sub-agent pipeline song song
└── references/
    ├── hub-airports.md           # Transit visa + MCT table
    ├── airline-fee-database.md   # 8 hãng, phí đầy đủ
    └── api-endpoints.md          # Kiwi + Skyscanner API spec
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

## Cấu hình API để lấy giá thật

Mặc định plugin chạy ở chế độ **estimate** (dữ liệu lịch sử). Để lấy giá thật thời gian thực, đăng ký các API miễn phí sau:

> ⚠️ **Amadeus Self-Service** đã đóng (tắt 17/7/2026) và **Kiwi Tequila** chuyển sang invitation-only. Dùng 2 API bên dưới — đăng ký được ngay, không cần approval.

### 1. SerpApi — Google Flights ✅ 250 calls/tháng free

1. Đăng ký tại **https://serpapi.com/users/sign_up** — lấy key ngay, không cần approval
2. Khai báo cho Claude:

```
SerpApi API Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Lợi thế:** Dữ liệu từ Google Flights, phủ gần như tất cả hãng toàn cầu.

### 2. FlightAPI.io ✅ 20 calls free trial

1. Đăng ký tại **https://www.flightapi.io** — lấy key ngay, không cần approval
2. Khai báo cho Claude:

```
FlightAPI.io API Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Lợi thế:** 700+ hãng, pricing realtime, JSON đơn giản. Sau free trial: $49/tháng.

### Dùng cả hai cùng lúc

```
Credentials API cho session này:
- SerpApi: key=xxx
- FlightAPI.io: key=xxx
```

| Cấu hình | Free calls | Độ chính xác giá |
|----------|-----------|-----------------|
| Không có API | — | Estimate ±30–50% |
| SerpApi | 250/tháng | Giá thật · Dữ liệu Google Flights |
| FlightAPI.io | 20 trial | Giá thật · 700+ hãng |
| **Cả hai** | — | **Tốt nhất** |

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
