# Template — Evidence Pack

Nộp kèm thin SPEC cuối Day 05.

## 1. Nhóm và track

**Tên nhóm:** D3
**Track:** E — Healthcare
**Product/app đã chọn:** Nhà thuốc Long Châu → AI giải thích đơn thuốc
**Build slice đang nghĩ:** Giải thích đơn thuốc bằng AI (search → giải thích → timeline → tương tác)

### Thành viên

1. Cao Đặng Quốc Vương — 2A202600738
2. Nguyễn Thành Vinh — 2A202600971
3. Giáp Minh Hiếu — 2A202600667

## 2. Self-use evidence

Nhóm tự dùng app/workflow và ghi lại điểm gãy.

| Observation | Screenshot/link | Path liên quan | Điều học được |
|---|---|---|---|
| Gõ "amlo" thấy gợi ý Amlodipine 5mg ngay | | Happy | Autocomplete giúp tiết kiệm thời gian |
| Gõ "paracetmol" (sai chính tả) — không thấy kết quả | | Low-confidence | Cần fuzzy match + gợi ý alternative |
| Chọn 3 thuốc — timeline tự động sáng/tối | | Happy | Timeline là killer feature cho người lớn tuổi |
| Hỏi "Metformin uống với canxi được không?" — chờ LLM >5s | | Failure | Cần fallback data DB trước, LLM update sau |
| Chọn nhầm Amlodipine 10mg (phải là 5mg) — phải xóa chọn lại | | Correction | Cần edit function ngay trong card |
| Đọc thông tin thuốc từ Google — toàn thuật ngữ y khoa khó hiểu | | Happy | LLM giải thích đơn giản hơn Google |
| Không biết Metformin ↔ Atorvastatin có tương tác không | | Happy | Interaction warning là tính năng cốt lõi |

## 3. User / review / social evidence

Nguồn có thể là review App Store/Play, group, comment, phỏng vấn nhanh, hoặc nguồn public khác.

| Quote / review / observation | Nguồn | User là ai? | Pain/failure mode |
|---|---|---|---|
| "Bác sĩ kê toa xong về đọc trên mạng chẳng hiểu gì" | FB group Hội mẹ bỉm sữa | Mẹ trẻ | P1 — Không hiểu thuốc |
| "1 ngày uống 4-5 loại thuốc, không biết cái nào trước cái nào sau" | Tư vấn dược sĩ Long Châu | Người lớn tuổi | P5 — Không biết lịch uống |
| "Uống Atorvastatin với Metformin có sao không nhỉ?" | Google Search "thuốc tương tác" | Bệnh nhân tiểu đường | P4 — Tương tác thuốc |
| "Dược sĩ đông quá, hỏi 1 câu ngại quá" | Forum sức khỏe | Bệnh nhân nói chung | P1-P5 — Ngại hỏi, thiếu thời gian |
| "App này có support tiếng Việt không? Toàn tiếng Anh" | DrugApp review | Người dùng VN | Language barrier |
| "Thuốc này uống lúc nào vậy BS nói nhanh quá quên mất" | Tự quan sát | Người lớn tuổi | P2 — Quên hướng dẫn |

Nếu chưa có nguồn ngoài nhóm, ghi rõ:

```text
Đây là giả định. Nhóm sẽ kiểm bằng [phỏng vấn nhanh 3-5 khách hàng ngoài nhà thuốc Long Châu] trước checkpoint M1 Day 06.
```

## 4. Competitor / analog evidence

| App / mô hình tham khảo | Họ xử lý task này thế nào? | Pattern học được | Có áp dụng trong 1 ngày không? |
|---|---|---|---|
| **Google Search** | User search tên thuốc → hiển thị web y khoa | Không tối ưu — thông tin kỹ thuật, không cá nhân hóa | ❌ |
| **DrugBank** | API tra cứu thuốc quốc tế, 14k+ drugs | DB có cấu trúc, interaction data | ✅ (nguồn tham khảo) |
| **traCuuThuoc.com** | Web tra cứu thuốc VN, có thông tin chi tiết | Dữ liệu tiếng Việt, format quen thuộc | ✅ (nguồn crawl) |
| **NXHealth / Medisafe** | Pill reminder + drug info | Timeline + nhắc uống thuốc | ✅ (timeline pattern) |
| **Bác sĩ / dược sĩ** | Giải thích trực tiếp, hỏi đáp real-time | Đơn giản dễ hiểu, trust cao | ⚠️ Không scale được |
| **ChatGPT / Claude** | Hỏi về thuốc → LLM trả lời | Giải thích dễ hiểu nhưng có thể hallucinate | ✅ (có guardrail) |

## 5. Evidence -> Insight

```text
Evidence nổi bật nhất:
User (đặc biệt người lớn tuổi / mẹ bỉm sữa) nhận đơn thuốc nhưng KHÔNG HIỂU.
Họ Google tên thuốc → thông tin y khoa khó hiểu.
Họ ngại/dược sĩ không có thời gian giải thích từng người.
Họ không biết thuốc uống lúc nào, có tương tác không, tác dụng phụ gì.

Insight:
User không chỉ gặp "không hiểu đơn thuốc".
Thật ra họ cần một người phiên dịch đáng tin cậy — 
dịch từ "ngôn ngữ y khoa" sang "ngôn ngữ thường ngày",
cá nhân hóa cho đơn thuốc của họ (không phải thông tin chung).
Họ cần CONFIRMATION rằng "uống thế này là đúng".

Opportunity:
AI có thể giúp bằng cách:
- Drug DB = source of truth (không hallucinate)
- LLM = translator (giải thích đơn giản, dễ hiểu)
- Timeline = cá nhân hóa lịch uống
- Interaction check = safety net
- Feedback loop = cải thiện dần
```

## 6. Evidence đổi SPEC như thế nào?

- [ ] Đổi user chính.
- [ ] Đổi pain statement.
- [ ] Đổi build slice.
- [ ] Đổi Auto/Aug decision.
- [x] Đổi 4 paths.
- [ ] Đổi failure mode.
- [x] Đổi owner/test plan.

Ghi rõ 1-2 thay đổi quan trọng:

```text
Trước evidence, nhóm định chỉ làm "search + giải thích 1 thuốc".
Sau evidence, nhóm thêm:
- Timeline (lịch uống sáng/tối) — vì user có nhiều thuốc không biết uống xen kẽ thế nào  
- Interaction check (tương tác) — vì user lo lắng uống chung có sao không
- Correction flow (sửa thuốc) — vì user hay chọn nhầm hàm lượng
- Feedback loop (👍/👎) — vì cần cải thiện chất lượng giải thích

Lý do: Evidence cho thấy pain không chỉ là "không hiểu 1 thuốc" 
mà là "không biết quản lý CẢ ĐƠN thuốc" — nhiều thuốc cùng lúc,
tương tác giữa chúng, lịch uống trong ngày.
```
