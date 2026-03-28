# API Endpoints Reference

> **last_updated**: 2025-05-01
> **note**: Reference file cho `flight-search` và `date-optimization` skills. Kiểm tra docs chính thức nếu gặp lỗi API.

---

## Đăng ký API & Khai báo credentials

### Amadeus Self-Service API — Free Tier

**Free tier:** 500 requests/tháng (production) + sandbox không giới hạn

**Bước đăng ký:**

1. Truy cập https://developers.amadeus.com/register
2. Tạo tài khoản → xác nhận email
3. Vào **My Apps** → **Create new app**
4. Điền tên app (VD: `travel-optimizer-personal`) → chọn **Self-Service**
5. Copy **API Key** và **API Secret**

**Môi trường:**
- **Sandbox** (test): `https://test.api.amadeus.com` — dữ liệu giả, không giới hạn call
- **Production** (giá thật): `https://api.amadeus.com` — 500 req/tháng free, sau đó tính phí

**Khai báo credentials trong Claude:**

```
Amadeus API Key: [dán key ở đây]
Amadeus API Secret: [dán secret ở đây]
Dùng môi trường: production (hoặc sandbox để test)
```

> Nói với Claude: *"Hãy dùng Amadeus API key sau để tìm giá thật: [key] / [secret]"*

---

### Kiwi Tequila API — Free Tier

**Free tier:** Có tài khoản partner miễn phí cho cá nhân/indie developer

**Bước đăng ký:**

1. Truy cập https://tequila.kiwi.com/portal
2. Đăng ký tài khoản → chọn **"I'm an individual / small business"**
3. Vào **API Keys** → **Generate new key**
4. Copy **API Key**

**Giới hạn free:**
- Không công bố số call cụ thể, thực tế ~1,000–5,000 req/ngày cho tài khoản indie
- Kết quả đầy đủ bao gồm virtual interlining

**Khai báo credentials trong Claude:**

```
Kiwi Tequila API Key: [dán key ở đây]
```

> Nói với Claude: *"Dùng Kiwi API key sau để tìm virtual interlining: [key]"*

---

### Sử dụng cả hai API trong một session

Khi có đủ cả hai key, cung cấp cho Claude lúc bắt đầu session:

```
Tôi muốn tối ưu vé máy bay. Credentials API:
- Amadeus: key=[...] / secret=[...]
- Kiwi: key=[...]
Dùng production cho Amadeus.
```

Plugin sẽ tự động query song song và trả về giá **thực tế** thay vì estimate.

---

### So sánh chế độ hoạt động

| Chế độ | Amadeus | Kiwi | Độ chính xác giá |
|--------|---------|------|-----------------|
| Không có API | ✗ | ✗ | Estimate ±30–50% |
| Chỉ Amadeus | ✓ (500/tháng) | ✗ | Tốt · Thiếu virtual interlining |
| Chỉ Kiwi | ✗ | ✓ | Tốt · Thiếu một số FSC |
| Cả hai | ✓ | ✓ | **Tốt nhất** · Đầy đủ cả FSC + LCC + virtual |

---

## Amadeus Self-Service API

Base URL: `https://api.amadeus.com`  
Sandbox: `https://test.api.amadeus.com`  
Docs: https://developers.amadeus.com/self-service

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
