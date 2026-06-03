# Toolkit — Từ Evidence Đến Build Slice

Dùng sau khi nhóm đã có evidence. Mục tiêu là chốt một build slice đủ nhỏ cho Day 06.

**Nhóm D3 — Track E: Healthcare**
1. Cao Đặng Quốc Vương — 2A202600738
2. Nguyễn Thành Vinh — 2A202600971
3. Giáp Minh Hiếu — 2A202600667

## 1. Gom evidence thành cụm

Gom theo **workflow/pain**, không gom theo tên feature.

- **Cụm 1 — Không hiểu thuốc:** "Bác sĩ kê toa xong về đọc trên mạng chẳng hiểu gì" + "Google search ra toàn thuật ngữ y khoa" + "ChatGPT trả lời chung chung"
- **Cụm 2 — Không biết lịch uống:** "1 ngày uống 4-5 loại, không biết cái nào trước cái nào sau" + "BS nói nhanh quá quên mất"
- **Cụm 3 — Lo tương tác thuốc:** "Uống Atorvastatin với Metformin có sao không" + "Tự search không có kết quả rõ ràng"
- **Cụm 4 — Dễ chọn sai:** Self-use test: chọn nhầm Amlodipine 10mg thay vì 5mg + không có chỗ sửa
- **Cụm 5 — LLM không đáng tin cậy:** Competitor analysis: ChatGPT có thể hallucinate về thuốc, nguy hiểm nếu user tin tưởng

## 2. Viết insight

```text
User [bệnh nhân mua thuốc tại Long Châu] không chỉ cần [tra cứu thông tin thuốc].
Họ thật ra cần [một người phiên dịch đáng tin cậy — dịch từ "ngôn ngữ y khoa" sang "ngôn ngữ thường ngày", cá nhân hóa cho đơn thuốc của họ],
vì [evidence cho thấy Google search ra thông tin kỹ thuật, dược sĩ không đủ thời gian, và họ lo lắng về tương tác/lịch uống].
```

## 3. Viết opportunity

```text
Cơ hội là dùng AI để [augment: search + giải thích + timeline + tương tác],
giúp user [hiểu đơn thuốc trong 3 giây — không cần Google],
trong khi vẫn kiểm soát [LLM hallucinate bằng Drug DB làm source of truth, safety layer "hỏi dược sĩ" cho case không chắc].
```

## 4. Chọn build slice

Build slice tốt phải qua 5 câu hỏi:

| Câu hỏi | Đạt khi |
|---|---|
| User cụ thể chưa? | ✅ Bệnh nhân Long Châu, người lớn tuổi / mẹ bỉm sữa |
| Task đủ hẹp chưa? | ✅ Chỉ search → giải thích → timeline (không OCR, không đặt thuốc, không push notification) |
| AI decision rõ chưa? | ✅ Augmentation: AI search + giải thích, user quyết |
| Failure path rõ chưa? | ✅ LLM timeout → DB fallback. LLM hallucinate → safety layer. Không tìm thấy → browse nhóm |
| Có evidence không? | ✅ Self-use (7 observations) + user review (5 quotes) + competitor (6 đối thủ) |

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

| Tình huống | Quyết định |
|---|---|
| Evidence yếu, user mơ hồ | — |
| Ý tưởng quá rộng | ✅ **Giảm scope:** Không OCR, không push notification, không voice. Chỉ search + giải thích + timeline + interaction |
| AI không cần thiết | — |
| Rủi ro cao | ✅ **Chọn augmentation:** AI chỉ giải thích, user quyết. Safety layer "hỏi dược sĩ" cho case nguy hiểm |
| Không demo được trong 1 ngày | ✅ **Cắt:** Giữ 4 paths (happy/low-confidence/failure/correction), bỏ feedback path nếu không đủ thời gian |

## 6. Câu chốt cuối

Điền câu này trước khi rời lớp:

```text
Dựa trên [self-use test + user review + competitor analysis],
nhóm sẽ build [AI giải thích đơn thuốc — search → giải thích → timeline → tương tác],
cho [bệnh nhân mua thuốc tại Long Châu],
để giải quyết [không hiểu đơn thuốc — không biết uống lúc nào, có tương tác không, tác dụng phụ gì],
bằng cách AI [augment: search fuzzy match + LLM giải thích đơn giản + timeline tự động + cảnh báo tương tác],
và sẽ test failure path [LLM hallucinate / timeout / sai liều / không tìm thấy thuốc].
```

## 7. Backlog

Những thứ **không build trong Day 06**:

- OCR scan đơn thuốc (cần model nhận dạng tiếng Việt, phức tạp)
- Push notification nhắc uống thuốc (cần mobile app + background service)
- Voice input/output (cần speech-to-text tiếng Việt)
- Đặt thuốc online (cần integration với Long Châu POS)
- User authentication / login (không cần cho demo)
- Drug interaction database đầy đủ (chỉ làm 300+ cặp phổ biến nhất)
- Dược sĩ chat real-time (cần staffing)
