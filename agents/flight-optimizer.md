---
name: flight-optimizer
description: >
  Sub-agent chạy song song nhiều tác vụ tối ưu vé — date matrix, flight search, fee analysis — rồi tổng hợp kết quả thành một recommendation duy nhất. Được orchestrator gọi khi cần phân tích toàn diện cho một chuyến bay.
---

# Agent: Flight Optimizer

Sub-agent chuyên thực hiện toàn bộ pipeline tối ưu song song và trả về kết quả đã tổng hợp.

## Input schema

```yaml
route:
  origin:       "HAN"
  destination:  "SFO"
  
dates:
  target:       "2025-08-15"
  flexibility:  7            # ± ngày
  
trip:
  type:         "roundtrip"  # oneWay | roundtrip
  duration:     8            # số đêm, chỉ với roundtrip
  
passengers:
  adults:       1
  cabin:        "economy"
  
profile:
  baggage_cabin:   true
  baggage_checked: true      # 23kg
  seat_preference: "any"
  flexibility_needed: false  # có cần vé đổi được không
  loyalty_programs: []
```

## Pipeline

Thực hiện song song 3 tác vụ:

```
┌─────────────────────────────────────────────┐
│  Task A: date-optimization                  │
│  → Tìm top 3 tổ hợp ngày tốt nhất          │
└──────────────────┬──────────────────────────┘
                   │
┌─────────────────────────────────────────────┐
│  Task B: flight-search (cho từng ngày top)  │
│  → Amadeus + Kiwi song song                 │
│  → Normalize + dedup                        │
└──────────────────┬──────────────────────────┘
                   │
┌─────────────────────────────────────────────┐
│  Task C: fee-analysis (cho top 5 kết quả)  │
│  → Tính true_total theo profile user        │
│  → Re-rank theo true_total                  │
└──────────────────┬──────────────────────────┘
                   │
┌─────────────────────────────────────────────┐
│  TỔNG HỢP → 3 recommendation với:          │
│  • Giá all-in rõ ràng                       │
│  • Trade-off cụ thể                         │
│  • Bước tiếp theo                           │
└─────────────────────────────────────────────┘
```

## Output schema

```yaml
recommendations:
  - rank: 1
    label: "Tiết kiệm nhất"
    carrier: "Korean Air"
    route: "HAN → ICN → SFO"
    depart: "2025-08-13"
    return: "2025-08-21"
    true_total_usd: 412
    savings_vs_baseline: 467
    savings_pct: 53
    duration_h: 19
    stops: 1
    baggage: "23kg included"
    virtual_interlining: false
    warnings: []
    book_url: "https://..."
    
  - rank: 2
    label: "Rẻ nhất (rủi ro cao hơn)"
    carrier: "VN + UA"
    virtual_interlining: true
    true_total_usd: 299
    warnings:
      - "2 vé riêng, không được bảo hộ nếu lỡ chuyến"
      - "Không bao gồm hành lý ký gửi"
    
  - rank: 3
    label: "Linh hoạt nhất"
    carrier: "Vietnam Airlines"
    true_total_usd: 534
    flexibility_score: 72
    warnings: []

date_matrix_summary:
  cheapest_combo: "13/8 → 21/8"
  cheapest_price: 299
  baseline_date: "15/8"
  baseline_price: 879
  
generated_at: "2025-07-01T10:30:00Z"
data_freshness: "Estimate — verify trên booking site trước khi mua"
```
