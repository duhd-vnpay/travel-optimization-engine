# Hub Airports — Chiến Lược Quá Cảnh

> **last_updated**: 2025-05-01
> **note**: Reference file cho `route-optimization` skill. Transit visa requirements thay đổi thường xuyên — luôn verify với đại sứ quán hoặc TIMATIC trước khi confirm.

## Châu Á → Bắc Mỹ

| Hub | Code | Hãng chính | Lợi thế | Lưu ý |
|-----|------|-----------|---------|-------|
| Seoul | ICN | Korean Air, Asiana | Rẻ 15–25% vs NRT/HND, MCT 45 phút | Tốt nhất cho HAN/SGN → US West Coast |
| Taipei | TPE | China Airlines, EVA | Rẻ với premium, ít biết đến | Passport VN cần check visa transit |
| Tokyo | NRT/HND | ANA, JAL | Ổn định, ít rủi ro delay | Đắt hơn ICN 10–20% |
| Hong Kong | HKG | Cathay Pacific | Tốt cho Business, lounge tốt | Đắt cho economy |
| Singapore | SIN | Singapore Airlines | Long-haul excellent | Premium hơn, ít giảm giá economy |
| Shanghai | PVG | Air China, China Eastern | Rẻ nhất nhưng... | Cần transit visa TQ với nhiều passport; risk delay |
| Beijing | PEK | Air China | Tương tự PVG | Cùng note với PVG |
| Bangkok | BKK | Thai Airways | Okay cho economy | Ít competitive vs ICN |

**Khuyến nghị mặc định Việt Nam → US**: ICN > TPE > NRT

---

## Châu Á → Châu Âu

| Hub | Code | Hãng chính | Lợi thế | Lưu ý |
|-----|------|-----------|---------|-------|
| Istanbul | IST | Turkish Airlines | Rẻ nhất thường xuyên, lounge tốt | Layover dài thường có Turkish lounge miễn phí |
| Doha | DOH | Qatar Airways | Tốt cho Business/First | Economy ít giảm |
| Dubai | DXB | Emirates | Premium experience | Ít competitive hơn IST/DOH về giá |
| Helsinki | HEL | Finnair | Châu Á → Bắc Âu tốt | Ít route hơn |
| Frankfurt | FRA | Lufthansa | Hub lớn Tây Âu | Giá không ưu đãi nhiều |
| Amsterdam | AMS | KLM | Paris train connection (1h25) | Phù hợp nếu đến AMS/CDG/BRU |
| Copenhagen | CPH | SAS | Scandinavia | Route ít, niche |

**Khuyến nghị mặc định Việt Nam → Châu Âu**: IST > DOH > HEL (Bắc Âu) > AMS/FRA

---

## Nội địa Việt Nam → Khu vực

| Hub | Code | Route đặc biệt |
|-----|------|---------------|
| Singapore | SIN | VN–AU: tốt với Scoot/Singapore Airlines |
| Kuala Lumpur | KUL | VN–AU/NZ: AirAsia rẻ, cộng thêm AirAsia X |
| Bangkok | BKK | VN–India/Middle East |
| Guangzhou | CAN | VN–Europe với China Southern (cần check visa) |

---

## Minimum Connection Times (MCT) quan trọng

| Sân bay | MCT nội địa | MCT quốc tế | Ghi chú |
|---------|------------|-------------|---------|
| ICN | 30 phút | 45 phút | T1 ↔ T2 cần 20 phút shuttle |
| IST | 45 phút | 60 phút | Sân bay mới IST nhanh hơn Ataturk |
| DXB | 45 phút | 60 phút | T1–T3 cần xe buýt |
| HKG | 45 phút | 60 phút | Hiệu quả |
| LHR | 60 phút | 90 phút | Terminal changes = rủi ro cao |
| CDG | 45 phút | 90 phút | T2 phức tạp, T2E → T2F dễ miss |
| FRA | 30 phút | 45 phút | Hub Lufthansa, hiệu quả |
| NRT | 60 phút | 90 phút | Xa Tokyo, cần tính bus/train nếu có plan B |
| HND | 60 phút | 80 phút | Gần Tokyo hơn, terminal mới tốt |

**Nguyên tắc an toàn**: Layover < MCT × 1.5 = rủi ro cao. Luôn cảnh báo.

---

## Transit Visa theo Passport Việt Nam

| Điểm dừng | Transit cần visa? | Ghi chú |
|-----------|-----------------|---------|
| ICN (Hàn Quốc) | Không (≤24h) | Airside transit OK |
| NRT/HND (Nhật) | Không (≤24h airside) | Landside cần visa |
| SIN | Không | |
| KUL | Không | |
| BKK | Không | |
| IST | Không (≤24h airside) | |
| DXB, DOH | Không | |
| LHR (UK) | **Có** | Direct Airside Transit Visa |
| CDG (Pháp) | Không (Schengen) | |
| FRA, AMS (Schengen) | Không | |
| PVG, PEK (Trung Quốc) | **Có** (thường) | TWOV scheme hạn chế |
| SFO, LAX, JFK (US) | **Có** | Không có transit không visa |
| YVR, YYZ (Canada) | **Có** | eTA hoặc visa cần |
| SYD, MEL (Úc) | **Có** | |

**LUÔN kiểm tra visa requirements** trước khi suggest lộ trình qua hub mới với user.
