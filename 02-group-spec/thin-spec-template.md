# Template — Thin SPEC Cuối Day 05

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

**Track:** E — Healthcare
**Nhóm:** D3
**Product/app thật:** Nhà thuốc Long Châu
**User cụ thể:** Bệnh nhân mua thuốc tại nhà thuốc, không hiểu đơn thuốc (đặc biệt người lớn tuổi, phụ nữ mua thuốc cho con)
**Nhóm có phải user thật không? Nếu không, khác ở đâu?** Không — nhóm là sinh viên IT, khác ở chỗ không phải bệnh nhân thật. Cần phỏng vấn nhanh 3-5 khách hàng ngoài nhà thuốc.

**Thành viên:**
1. Cao Đặng Quốc Vương — 2A202600738
2. Nguyễn Thành Vinh — 2A202600971
3. Giáp Minh Hiếu — 2A202600667

## 2. Evidence summary

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| "Bác sĩ kê toa xong về đọc trên mạng chẳng hiểu gì" | FB group Hội mẹ bỉm sữa | User muốn giải thích đơn giản, không phải web y khoa | LLM translation layer là cốt lõi |
| "1 ngày uống 4-5 loại, không biết cái nào trước cái nào sau" | Tư vấn dược sĩ Long Châu | Timeline là nhu cầu thực, không chỉ là "nice to have" | Thêm timeline feature |
| "Uống Atorvastatin với Metformin có sao không?" | Google Search | User lo tương tác thuốc | Thêm interaction check |
| Chọn nhầm Amlodipine 10mg (phải 5mg) | Self-use test | User dễ chọn sai hàm lượng | Thêm correction flow |
| ChatGPT trả lời về thuốc có thể sai | Competitor analysis | LLM hallucinate nguy hiểm với sức khỏe | Drug DB = source of truth, LLM chỉ translator |

## 3. Pain statement

```text
User [bệnh nhân mua thuốc tại Long Châu] đang gặp khó ở [hiểu đơn thuốc của mình],
vì [thông tin trên web quá kỹ thuật, dược sĩ không đủ thời gian giải thích từng người],
dẫn tới [không biết thuốc uống để làm gì, uống lúc nào, có tương tác không, tác dụng phụ gì].
Bằng chứng chính là [review từ hội mẹ bỉm sữa + quan sát self-use: Google search cho kết quả y khoa khó hiểu].
```

## 4. Build slice

```text
Cho [bệnh nhân có đơn thuốc] đang [gõ tên thuốc để tìm hiểu],
prototype sẽ dùng AI để [augment: search → giải thích → timeline → tương tác],
tạo ra [tóm tắt từng thuốc bằng tiếng Việt đơn giản + lịch uống + cảnh báo tương tác],
và xử lý [LLM hallucinate / sai liều / timeout] bằng [DB làm source of truth, fallback khi LLM lỗi, hỏi xác nhận khi confidence thấp].
```

## 5. Auto/Aug decision

Chọn một:

- [x] **Augmentation:** AI gợi ý/draft/phân loại, user quyết cuối.
- [ ] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

**Lý do chọn:** Thông tin thuốc liên quan sức khỏe — AI chỉ giải thích, không quyết định. User (hoặc dược sĩ) là final authority.
**Human role:** reviewer + decider

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| Happy | Search "amlo" → autocomplete → chọn 3 thuốc → giải thích + timeline + interaction warning |
| Low-confidence | Gõ sai "paracetmol" → fuzzy match → gợi ý kèm xác nhận. Chọn thuốc không rõ hàm lượng → hỏi user chọn |
| Failure | LLM timeout → hiển thị DB data trước. LLM hallucinate → safety layer replace bằng "hỏi dược sĩ". Không tìm thấy thuốc → browse theo nhóm |
| Correction | Sửa hàm lượng / thêm / xóa thuốc → tự động update giải thích + timeline + interactions |

## 7. Failure mode nguy hiểm nhất

```text
Nếu user [chọn sai thuốc hoặc AI giải thích sai liều lượng],
AI có thể [khiến user uống sai thuốc / sai liều],
hậu quả là [ảnh hưởng sức khỏe, đặc biệt người lớn tuổi hoặc trẻ em].
Prototype sẽ xử lý bằng [luôn hiển thị cảnh báo "Tham khảo ý kiến dược sĩ" + drug DB làm source of truth, LLM chỉ translator + correction flow cho user sửa].
Owner kiểm thử path này là [Cao Đặng Quốc Vương].
```

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
|---|---|---|---|
| Cao Đặng Quốc Vương | Drug DB + Search API | `data_c4ai/clean.jsonl`, `backend/drug_db.sql`, search API endpoint |
| Nguyễn Thành Vinh | Prompt engineering + Explain API | `backend/prompts/`, explain API endpoint |
| Giáp Minh Hiếu | Frontend demo | `frontend/demo.html` — search → giải thích → timeline |
| Cả nhóm | Test failure path + Demo script | Test report, demo narrative trong README |
