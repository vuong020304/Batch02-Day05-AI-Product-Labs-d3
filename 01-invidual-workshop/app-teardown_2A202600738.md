# Workshop — Mổ App AI Thật: MoMo AI Moni

**Thời gian:** 35-45 phút  
**Hình thức:** cá nhân trước, chia sẻ theo nhóm sau  
**Tác giả:** Cao Đặng Quốc Vương — 2A202600738  
**Output:** finding note + sketch `as-is / to-be`

Mục tiêu không phải chấm "UI đẹp hay xấu". Mục tiêu là dùng sản phẩm thật như một bài needfinding: tìm chỗ product gãy trong workflow thật, rồi viết finding đó thành quyết định product.

## 1. Chọn một sản phẩm để dùng thử

| Sản phẩm | AI feature | Cách truy cập |
|---|---|---|
| MoMo — Moni | Trợ thủ tài chính, phân tích chi tiêu, chatbot | App MoMo |

## 2. Dùng thử: promise vs reality

**Product hứa gì?**
> "Chỉ cần chat, AI ghi chép hộ và hỗ trợ phân tích chi tiêu cá nhân."

**User nào được hứa sẽ được giúp?**
Người mới bắt đầu quản lý tài chính cá nhân, cần trải nghiệm đơn giản, ít bước và dễ sửa sai.

**Bạn kỳ vọng AI làm được task nào?**
- Nhận diện số tiền, thời gian, danh mục từ câu tự nhiên
- Xử lý tình huống chia tiền / hoàn tiền / ghi nhầm
- Duy trì ngữ cảnh hội thoại
- Giảm nhu cầu chuyển sang màn hình nhập liệu truyền thống

**Khi dùng thật, điểm gãy xuất hiện ở đâu?**

| Query | Kết quả | Điểm gãy |
|---|---|---|
| "Hôm qua đi chợ hết 150k" | ✅ Ghi nhận đúng | — |
| "Sáng nay ăn phở 45k được thối lại 5k, mà quên ví MoMo còn bao nhiêu tiền rồi" | ❌ Nhầm tiền thừa là thu nhập, không tra được số dư | Intent + Data-tool |
| "Cho mình xin file Excel chi tiết chi tiêu tháng này" | ❌ Không xuất được file, chỉ hướng dẫn thủ công | UX Recovery |
| "Khoản chi đi chợ 150k hôm qua mình ghi nhầm, thực ra chỉ có 120k thôi" | ❌ Không tìm được giao dịch, không sửa được | Promise + Data-tool |

## 3. Vẽ 4 paths

| Path | Câu hỏi cần trả lời | Trên MoMo Moni |
|---|---|---|
| Happy | Khi AI đúng và tự tin, user thấy gì? | Ghi nhận chi tiêu thành công, AI trả lời xác nhận với số tiền + danh mục + thời gian. User thấy sao kê ngay trong chat. |
| Low-confidence | Khi AI không chắc, hệ thống có hỏi lại, show options hoặc chuyển người không? | **Chưa có.** AI thường trả lời theo suy luận mặc định mà không hỏi lại. VD: nhầm "thối lại 5k" thành thu nhập — không có bước confirm. |
| Failure | Khi AI sai, user biết bằng cách nào và sửa thế nào? | **Yếu.** User phải tự phát hiện sai, rời chat vào Sổ chi tiêu, tìm giao dịch và sửa thủ công. Không có cơ chế báo sai trong chat. |
| Correction | Khi user sửa, correction có được lưu/log/học lại không hay biến mất? | **Không.** Sửa thủ công ngoài chat không được dùng để cải thiện AI. Mỗi lần chat lại là một phiên mới, không học từ hành vi sửa của user. |

## 4. Viết finding thành quyết định

**Finding 1**

```text
Khi user hỏi "Khoản chi đi chợ 150k hôm qua ghi nhầm, sửa lại giúp mình",
AI/product không thể tìm lại giao dịch và không thể chỉnh sửa trực tiếp trong chat,
hậu quả là user phải rời hội thoại, tự mở Sổ chi tiêu, tìm và sửa thủ công.
Lỗi thuộc layer Promise + Data-tool + UX Recovery.
Nên sửa bằng hybrid conversational UI: AI truy vấn Sổ chi tiêu, hiển thị ứng viên để user chọn, xác nhận trước khi sửa, có nút hoàn tác.
```

**Finding 2**

```text
Khi user nói "Sáng nay ăn phở 45k được thối lại 5k",
AI hiểu "thối lại 5k" là thu nhập thay vì nhận ra đó là tiền thừa giao dịch tiền mặt,
hậu quả là phân loại sai và user không có cách giải thích lại cho AI hiểu.
Lỗi thuộc layer Intent + UX Recovery.
Nên sửa bằng low-confidence path: AI hỏi lại "Đây có phải là tiền thừa không?" hoặc cho user chọn phân loại đúng.
```

**Finding 3**

```text
Khi user yêu cầu "Cho mình xin file Excel chi tiêu",
AI không xuất được file trong chat, chỉ hướng dẫn vào Sổ chi tiêu để tự xuất,
hậu quả là gián đoạn luồng làm việc, user phải rời chat.
Lỗi thuộc layer UX Recovery.
Nên sửa bằng deep-link "Mở Sổ chi tiêu" kèm hướng dẫn từng bước trong chat, hoặc gửi file qua email/Zalo nếu được tích hợp.
```

## 5. Sketch as-is / to-be

**As-is flow:**

```text
[User gửi truy vấn]
        ↓
[AI phân tích ý định và thực thể]
        ↓
[Kiểm tra khả năng xử lý]
        ├─ Đủ thông tin → Ghi nhận / tư vấn thành công
        ├─ Thiếu chắc chắn → Phản hồi chưa chính xác hoặc hỏi lại
        └─ Cần quyền truy cập / thao tác ngoài chat
               → Từ chối hoặc hướng dẫn thủ công
               → User phải rời khỏi luồng chat ❌
```

**To-be flow (hybrid conversational UI):**

```text
[User gửi truy vấn]
        ↓
[AI phân tích ý định và thực thể]
        ↓
[Kiểm tra khả năng xử lý]
        ├─ Đủ thông tin → Ghi nhận / tư vấn thành công
        │                   → [Hoàn tác] [Xem trong Sổ chi tiêu]
        ├─ Thiếu chắc chắn → Hỏi lại với options
        │                      → User chọn → Xác nhận → Ghi nhận
        └─ Cần sửa/xuất/truy vấn
               → AI tìm giao dịch liên quan
                  ├─ Tìm thấy 1 → Hỏi xác nhận → Sửa → [Hoàn tác] ✅
                  ├─ Tìm thấy nhiều → Hiển thị danh sách → User chọn → Sửa ✅
                  └─ Không tìm thấy → Deep-link mở Sổ chi tiêu ✅
```

## 6. Tự kiểm trước khi nộp

- [x] Có ít nhất 1 screenshot hoặc observation cụ thể — có 6 query test với kết quả chi tiết
- [x] Có đủ 4 paths hoặc nói rõ path nào chưa có trong product — Low-confidence và Correction chưa có
- [x] Finding được viết thành product decision, không chỉ là nhận xét — 3 findings với layer + giải pháp
- [x] Sketch có as-is và to-be — có flow chi tiết cả hai
- [x] Có một câu nói rõ finding này sẽ đổi gì trong SPEC — Moni cần chuyển từ chatbot văn bản sang hybrid conversational UI có khả năng truy vấn, tìm kiếm, xác nhận và cập nhật giao dịch ngay trong chat
