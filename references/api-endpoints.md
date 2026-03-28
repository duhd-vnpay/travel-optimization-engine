# API Endpoints Reference

> **last_updated**: 2026-03-28
> **note**: Reference file cho `flight-search` và `date-optimization` skills. Kiểm tra docs chính thức nếu gặp lỗi API.

---

## Đăng ký API & Khai báo credentials

> ⚠️ **Tình trạng API 2026:**
> - **Amadeus Self-Service**: đóng cửa, tắt hoàn toàn 17/7/2026
> - **Kiwi Tequila**: chuyển sang invitation-only, không đăng ký tự do
> - **Khuyến nghị hiện tại**: SerpApi (Google Flights) hoặc FlightAPI.io

---

### SerpApi — Google Flights API ✅ Mở đăng ký, 250 calls/tháng free

Truy cập dữ liệu Google Flights qua API có cấu trúc JSON.

**Bước đăng ký:**

1. Truy cập **https://serpapi.com/users/sign_up**
2. Tạo tài khoản → xác nhận email → lấy ngay **API Key**
3. Không cần approval, dùng được ngay

**Giới hạn free:**
- **250 queries/tháng** (free plan)
- Dữ liệu từ Google Flights — phủ hầu hết hãng toàn cầu
- Hỗ trợ round trip, one-way, multi-city, economy/business/first

**Khai báo credentials trong Claude:**

```
SerpApi API Key: [dán key ở đây]
```

> Nói với Claude: *"Dùng SerpApi key sau để tìm giá Google Flights: [key]"*

---

### FlightAPI.io ✅ Mở đăng ký, 20 calls free trial

API chuyên flight pricing, phủ 700+ hãng, JSON output.

**Bước đăng ký:**

1. Truy cập **https://www.flightapi.io**
2. Sign up → lấy ngay **API Key** — không cần approval
3. 20 calls free để test, sau đó $49/tháng (30,000 calls)

**Giới hạn free:**
- 20 API calls (đủ để test và demo)
- Giá thật realtime từ 700+ hãng, multi-currency JSON

**Khai báo credentials trong Claude:**

```
FlightAPI.io API Key: [dán key ở đây]
```

> Nói với Claude: *"Dùng FlightAPI key sau để tìm giá thật: [key]"*

---

### Skyscanner API ⏳ Cần approval (1–3 ngày)

Nếu cần phủ rộng hơn và chấp nhận chờ approval:

1. Truy cập **https://www.partners.skyscanner.net/product/travel-api**
2. Apply làm partner → nhận **API Key** qua email sau 1–3 ngày làm việc

---

### Sử dụng API trong session Claude

Cung cấp key khi bắt đầu session:

```
Credentials API cho session này:
- SerpApi (Google Flights): key=[...]
- FlightAPI.io: key=[...]
```

Plugin sẽ query song song và trả về giá **thực tế** thay vì estimate.

---

### So sánh chế độ hoạt động

| Chế độ | Free calls | Đăng ký | Độ chính xác |
|--------|-----------|---------|-------------|
| Không có API | — | — | Estimate ±30–50% |
| SerpApi (Google Flights) | 250/tháng | Ngay, không cần approval | ✅ Tốt · Dữ liệu Google Flights |
| FlightAPI.io | 20 calls trial | Ngay, không cần approval | ✅ Tốt · 700+ hãng |
| Skyscanner | Free (approved) | Cần approval 1–3 ngày | ✅ Tốt · Phủ rộng |
| **SerpApi + FlightAPI.io** | — | — | **Tốt nhất hiện tại** |

---

## Amadeus Self-Service API

> ⚠️ **Đã ngừng hoạt động** — Không nhận đăng ký mới. Tắt hoàn toàn 17/7/2026.
> Dùng **Kiwi Tequila** hoặc **Skyscanner** thay thế (xem phần trên).

~~Base URL: `https://api.amadeus.com`~~
~~Sandbox: `https://test.api.amadeus.com`~~
Docs (lưu trữ): https://developers.amadeus.com/self-service

### Authentication

```http
POST /v1/security/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={AMADEUS_API_KEY}
&client_secret={AMADEUS_API_SECRET}
```

Response: `{ "access_token": "...", "expires_in": 1799 }`

### Flight Offers Search

```http
GET /v2/shopping/flight-offers
Authorization: Bearer {access_token}

Params:
  originLocationCode:      HAN          # IATA code
  destinationLocationCode: SFO
  departureDate:           2025-08-13   # YYYY-MM-DD
  returnDate:              2025-08-21   # optional, khứ hồi
  adults:                  1
  children:                0            # optional
  infants:                 0            # optional
  travelClass:             ECONOMY      # ECONOMY|PREMIUM_ECONOMY|BUSINESS|FIRST
  includedAirlineCodes:    KE,VN        # optional filter
  excludedAirlineCodes:    NK           # optional
  nonStop:                 false
  currencyCode:            USD
  maxPrice:                1000         # optional
  max:                     50           # số kết quả tối đa
```

### Date Matrix (Cheapest Dates)

```http
GET /v1/shopping/flight-dates
Authorization: Bearer {access_token}

Params:
  origin:          HAN
  destination:     SFO
  departureDate:   2025-08-01,2025-08-31   # hoặc range
  duration:        7                         # số đêm (khứ hồi)
  nonStop:         false
  currencyCode:    USD
  viewBy:          DATE                      # DATE|DURATION
```

### Price Confirmation (trước khi book)

```http
POST /v1/shopping/flight-offers/pricing
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "data": {
    "type": "flight-offers-pricing",
    "flightOffers": [{ ...offer_object... }]
  }
}
```

---

## Kiwi Tequila API

Base URL: `https://api.tequila.kiwi.com`  
Docs: https://tequila.kiwi.com/portal/docs  
Requires: API key từ Kiwi partner program

### Search

```http
GET /v2/search
apikey: {KIWI_API_KEY}

Params:
  fly_from:        HAN
  fly_to:          SFO
  date_from:       13/08/2025    # DD/MM/YYYY
  date_to:         13/08/2025    # same day = specific date
  return_from:     21/08/2025    # optional, khứ hồi
  return_to:       21/08/2025
  adults:          1
  children:        0
  infants:         0
  selected_cabins: M             # M=Economy Y=Economy C=Business F=First
  curr:            USD
  locale:          en
  limit:           50
  sort:            price         # price|duration|quality
  partner:         picky         # partner code
  vehicle_type:    aircraft      # tránh bus/train
  max_stopovers:   2
```

Response fields quan trọng:
- `price`: giá USD all-in (đã bao gồm thuế)
- `virtual_interlining`: true/false
- `local_departure`, `local_arrival`
- `route`: array of legs với carrier info
- `deep_link`: URL đặt vé trực tiếp

### Nomad (Multi-city optimization)

```http
GET /v2/search
Params (thêm):
  fly_to:   SFO,LAX,SJC    # nhiều điểm đến, chọn rẻ nhất
```

---

## Rate Limits và Best Practices

| API | Free tier | Paid |
|-----|----------|------|
| Amadeus (sandbox) | Unlimited | — |
| Amadeus (prod) | 500 req/month | $X/1000 req |
| Kiwi Tequila | Cần partner account | Volume-based |

### Retry logic

```python
import time

def search_with_retry(endpoint, params, max_retries=3):
    for attempt in range(max_retries):
        response = requests.get(endpoint, params=params)
        
        if response.status_code == 200:
            return response.json()
        elif response.status_code == 429:  # rate limited
            wait = 2 ** attempt  # exponential backoff: 1s, 2s, 4s
            time.sleep(wait)
        elif response.status_code == 401:  # token expired
            refresh_token()
        else:
            break
    
    return None  # fallback to cached/estimated data
```

### Parallel queries

```python
import asyncio
import aiohttp

async def search_all_sources(route, date, params):
    async with aiohttp.ClientSession() as session:
        amadeus_task = search_amadeus(session, route, date, params)
        kiwi_task    = search_kiwi(session, route, date, params)
        
        results = await asyncio.gather(
            amadeus_task, kiwi_task,
            return_exceptions=True  # không fail nếu 1 source lỗi
        )
        
        return normalize_and_merge(results)
```

---

## Fallback khi không có API access

Nếu không có credentials hoặc trong môi trường không có network:

1. **Báo rõ**: "Tôi không có kết nối API thời gian thực. Phân tích dưới đây dựa trên pattern giá lịch sử."
2. **Estimate dựa trên pattern**:
   - Thứ 3, Thứ 4 = -15% so với trung bình
   - Thứ 6, Thứ 7 = +20% so với trung bình
   - Mùa hè = +40%, Tết = +80%, off-peak = baseline
3. **Gợi ý verify**: Google Flights, Kayak, Matrix Airfare Search (ITA Matrix) để user tự check
