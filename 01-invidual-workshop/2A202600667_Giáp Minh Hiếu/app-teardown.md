# Workshop — Mổ App AI Thật

**Sản phẩm chọn:** MoMo — Moni  
**AI feature:** Trợ thủ tài chính, phân tích chi tiêu, chatbot  
**Người làm:** Giáp Minh Hiếu - 2A202600667
**Mục tiêu teardown:** Kiểm tra Moni xử lý thế nào khi user yêu cầu loại bỏ một giao dịch khỏi báo cáo chi tiêu cá nhân, đặc biệt khi thông tin giao dịch user đưa ra không tồn tại trong dữ liệu thật.

---

## 1. Sản phẩm và promise

| Câu hỏi | Ghi nhận |
|---|---|
| Product hứa gì? | Moni hứa giúp user hiểu chi tiêu, hỏi đáp về giao dịch và hỗ trợ quản lý tài chính cá nhân trong MoMo. |
| User nào được hứa sẽ được giúp? | Người dùng MoMo muốn xem, phân tích, phân loại hoặc điều chỉnh cách tính chi tiêu cá nhân. |
| Kỳ vọng AI làm được task nào? | Khi user nói một giao dịch là "quẹt hộ công ty", Moni cần kiểm tra giao dịch thật, hỏi xác nhận đúng đối tượng, rồi hỗ trợ loại trừ khỏi báo cáo chi tiêu cá nhân nếu hệ thống cho phép. |
| Khi dùng thật, điểm gãy ở đâu? | Moni nhận lời xử lý một giao dịch không được kiểm chứng trước, sau đó báo "hệ thống đang lỗi" thay vì nói rõ không tìm thấy giao dịch phù hợp. |

---

## 2. Evidence: prompt/input đã thử và hành vi quan sát được

### Input 1

```text
cái giao dịch 1tr5 ở kichi hômmqua là tôi quẹt hộ công ty,
không tính vào chi tiêu cá nhân hiểu chưa
```

### Moni trả lời

```text
Tôi đã hiểu, giao dịch 1 triệu 5 ở Kichi hôm qua là quẹt hộ công ty,
không tính vào chi tiêu cá nhân của bạn.
Bạn muốn tôi giúp bỏ khoản đó ra khỏi báo cáo chi tiêu cá nhân không?
```

### Input 2

```text
oke bỏ khoản đó đi
```

### Moni trả lời

```text
Đã nhận yêu cầu loại bỏ giao dịch 1.500.000đ ở Kichi hôm qua khỏi chi tiêu cá nhân.
Tuy nhiên, hiện tại hệ thống đang gặp lỗi, chưa thể xóa giao dịch này ngay.
Bạn có thể thử lại sau hoặc nhấn...
```

### Observation chính

Moni hành xử như thể giao dịch `1.500.000đ ở Kichi hôm qua` là một object thật đã được tìm thấy, dù chưa có bằng chứng rằng hệ thống đã truy xuất được giao dịch này trong database. Khi thao tác không thực hiện được, sản phẩm chuyển lỗi thành "hệ thống đang lỗi", làm user nghĩ MoMo gặp lỗi kỹ thuật thay vì hiểu rằng thông tin giao dịch ban đầu có thể sai hoặc không đủ rõ.

---

## 3. Promise vs reality

| Khía cạnh | Promise / kỳ vọng | Reality quan sát được | Rủi ro |
|---|---|---|---|
| Data grounding | AI phải kiểm tra giao dịch thật trước khi hứa thao tác. | Moni xác nhận lại giao dịch như thể đã tìm thấy. | User tin rằng AI đã hiểu đúng dữ liệu tài chính. |
| Action safety | Với update/delete, hệ thống cần xác nhận object cụ thể trước khi thay đổi dữ liệu. | Moni tạo confirmation flow trước khi có object ID rõ ràng. | Dễ dẫn tới thao tác sai hoặc thất bại mơ hồ. |
| UX recovery | Khi thông tin user sai/mơ hồ, app nên hỏi lại hoặc đưa lựa chọn. | Moni báo "hệ thống đang lỗi". | User mất niềm tin vào sản phẩm tài chính. |
| Transparency | User cần biết vì sao task không làm được. | Lý do thật có thể là không tìm thấy giao dịch, nhưng UI lại nói lỗi hệ thống. | Sai mental model: user nghĩ lỗi do app, không biết cách sửa input. |

---

## 4. Vẽ 4 paths

| Path | Moni hiện tại / quan sát | Vấn đề | Thiết kế nên có |
|---|---|---|---|
| Happy | User nói đúng giao dịch thật; Moni tìm thấy giao dịch, hỏi xác nhận, rồi loại trừ khỏi báo cáo nếu backend hỗ trợ. | Happy path chỉ an toàn nếu có transaction ID thật trước khi xác nhận. | Hiển thị transaction card: merchant, ngày, số tiền, nguồn dữ liệu, nút xác nhận "Loại trừ khỏi chi tiêu cá nhân". |
| Low-confidence | User nhập sai chính tả hoặc thông tin gần đúng như "kichi hômmqua", "1tr5". | Moni vẫn nói như đã hiểu chắc. | Moni cần nói: "Mình chưa tìm thấy giao dịch khớp hoàn toàn. Có phải bạn muốn nói một trong các giao dịch này không?" |
| Failure | Không tìm thấy giao dịch phù hợp trong database. | Moni báo "hệ thống đang lỗi", dù lỗi có thể là không tìm thấy object. | Fallback đúng: "Moni chưa tìm thấy giao dịch 1.500.000đ tại Kichi hôm qua. Bạn có thể kiểm tra lại ngày/số tiền hoặc chọn từ danh sách giao dịch gần nhất." |
| Correction | User muốn sửa thông tin hoặc chọn giao dịch khác. | Chưa thấy path giúp user sửa input/đổi lựa chọn sau khi fail. | Hiển thị 3 giao dịch ăn uống gần nhất dạng list/card, mỗi card có nút "Đây là giao dịch này" và "Loại trừ". |

---

## 5. Finding viết thành product decision

```text
Khi user yêu cầu loại bỏ một giao dịch khỏi báo cáo chi tiêu cá nhân,
AI/product nhận lời xử lý trước khi kiểm chứng giao dịch đó có tồn tại trong dữ liệu thật,
hậu quả là user tin rằng Moni đã hiểu đúng object tài chính, nhưng khi backend không tìm thấy giao dịch thì sản phẩm báo "hệ thống lỗi".
Lỗi thuộc layer data-tool + safety + UX recovery.
Nên sửa bằng requirement: với mọi tác vụ update/delete dữ liệu tài chính,
LLM không được tạo confirmation flow nếu chưa truy xuất được transaction object thật.
Nếu không tìm thấy hoặc confidence thấp, Moni phải chuyển sang low-confidence path:
hỏi lại thông tin hoặc hiển thị danh sách giao dịch gần nhất để user tự chọn.
```

### Product decision

```text
Đối với các tác vụ làm thay đổi trạng thái dữ liệu như update/delete,
tuyệt đối không cho phép LLM tự sinh luồng xác nhận nếu chưa truy xuất được object dữ liệu thực tế.
Khi thông tin từ user sai hoặc mơ hồ, AI phải chuyển sang UI gợi ý để user rà soát và chọn giao dịch thật,
thay vì trả về thông báo "hệ thống lỗi" gây tổn hại niềm tin vào sản phẩm tài chính.
```

---

## 6. Sketch as-is / to-be

### As-is: flow hiện tại

```text
User:
"Giao dịch 1tr5 ở Kichi hôm qua là quẹt hộ công ty"
        |
        v
Moni:
Nhận diện intent "loại trừ khỏi chi tiêu cá nhân"
nhưng chưa chứng minh đã tìm được transaction thật
        |
        v
Moni:
"Bạn muốn tôi giúp bỏ khoản đó ra khỏi báo cáo không?"
        |
        v
User:
"oke bỏ khoản đó đi"
        |
        v
Backend/action:
Không tìm thấy transaction ID hoặc action không thực hiện được
        |
        v
Moni:
"Hệ thống đang gặp lỗi, chưa thể xóa giao dịch này"
        |
        v
Điểm gãy:
User không biết sai ở input, thiếu dữ liệu hay hệ thống thật sự lỗi.
```

### To-be: flow đề xuất

```text
User:
"Giao dịch 1tr5 ở Kichi hôm qua là quẹt hộ công ty"
        |
        v
Moni:
Parse intent + entity: merchant=Kichi, amount=1.500.000đ, time=hôm qua
        |
        v
Tool/backend:
Search transaction database theo merchant/amount/time
        |
        +--> Nếu tìm thấy exact match:
        |       Hiển thị transaction card thật
        |       User bấm "Loại trừ khỏi chi tiêu cá nhân"
        |       Backend cập nhật báo cáo
        |
        +--> Nếu confidence thấp:
        |       Hiển thị 2-3 giao dịch gần nhất có thể liên quan
        |       User chọn đúng giao dịch hoặc sửa thông tin
        |
        +--> Nếu không tìm thấy:
                Nói rõ "Moni chưa tìm thấy giao dịch khớp"
                Gợi ý kiểm tra lại ngày/số tiền/merchant
                Hiển thị shortcut mở lịch sử giao dịch
```

---

## 7. Requirement / test case đề xuất

| ID | Requirement / test case | Expected behavior |
|---|---|---|
| R1 | Với mọi request loại trừ/sửa/xóa giao dịch, phải gọi transaction search trước khi hỏi xác nhận. | Không có transaction object thì không hỏi "Bạn muốn tôi bỏ khoản đó không?". |
| R2 | User nhập giao dịch không tồn tại: `1tr5 ở Kichi hôm qua`. | Moni nói không tìm thấy giao dịch khớp và hiển thị cách sửa/chọn giao dịch gần nhất. |
| R3 | User nhập sai chính tả merchant: `kichi hômmqua`. | Moni dùng fuzzy match nhưng vẫn hiển thị confidence thấp và yêu cầu user xác nhận. |
| R4 | Backend action thất bại sau khi đã có object thật. | Moni phân biệt lỗi action thật với lỗi không tìm thấy dữ liệu; message phải nói rõ nguyên nhân và next step. |
| R5 | User chọn một giao dịch từ suggestion list. | Moni lưu transaction ID đã chọn và chỉ thao tác trên ID đó. |

---

## 8. Finding này đổi gì trong SPEC nhóm?

Finding từ Moni ảnh hưởng trực tiếp tới SPEC của nhóm ở phần AI healthcare:

```text
Không để AI trả lời hoặc hành động như đã chắc chắn nếu chưa ground vào dữ liệu thật.
Với thuốc, AI không được tự chọn thuốc/hàm lượng hoặc suy diễn tương tác.
Prototype phải bắt user xác nhận object thật trước khi giải thích,
và khi thiếu dữ liệu phải chuyển sang low-confidence/failure path thay vì nói như đã biết chắc.
```

Áp dụng vào thin SPEC:

- Search thuốc không auto-select khi confidence thấp.
- Phải xác nhận hàm lượng/dạng bào chế trước khi generate phiếu giải thích.
- Output chỉ dùng dữ liệu trong seed DB có source.
- Khi thiếu source, app nói "chưa đủ dữ liệu đã kiểm chứng" và hướng dẫn hỏi dược sĩ/bác sĩ.

---

## 9. Tự kiểm trước khi nộp

- [x] Có ít nhất 1 observation cụ thể từ prompt/input và response của Moni.
- [x] Có đủ 4 paths: happy, low-confidence, failure, correction.
- [x] Finding được viết thành product decision, không chỉ là nhận xét.
- [x] Sketch có as-is và to-be.
- [x] Có một câu nói rõ finding này sẽ đổi gì trong SPEC.
