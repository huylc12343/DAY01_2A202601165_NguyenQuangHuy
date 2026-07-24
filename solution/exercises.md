# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *0.0 thì câu trả lời rất ổn định, các thông tin chính xác, 0.5 thì câu trả lời có thêm những mô tả về thông tin được đưa và có thêm những từ mô tả bay bổng hơn, 1.0 độ sáng tạo cao hơn nhưng mỗi lần gọi hàm lại thì sẽ là 1 câu trả lời khác nhau, 1.5 câu trả lời sáng tạo hơn nhưng nội dụng được đưa ra ít chính xác hơn*

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *em sẽ đặt temperature trong khoảng 0.1-0.5 cho chatbot hỗ trợ khách hàng. bởi vì khi tư vấn khách hàng cần cung cấp thông tin chính xác, hạn chế các thông tin tự suy diễn*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> *GPT-4o đắt hơn GPT-4o-mini xấp xỉ 16,7 lần, Trường hợp GPT-4o xứng đáng với chi phí là cho các bài toán có yêu cầu chuyên môn và  kiến thức như làm toán, nghiên cứu, tư vấn chuyên môn. Những bài toán này yêu cầu sự chính xác hơn là tối ưu chi phí. Trường hợp nên dùng mini là những trường hợp như bài toán hỏi đáp chăm sóc khách hàng, hoặc trả lời theo kịch bản có sẵn, FAQ, tóm tắt văn bản.*

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *với system prompt bạn là giáo viên tiểu học thì câu trả lời của model sẽ ngắn gọn, đơn giản. Còn đối với system prompt bạn là chuyên gia tài chính thì câu trả lời sẽ dài hơn, sử dụng nhiều thuật ngữ chuyên ngành hơn và giải thích theo góc nhìn của chuyên gia. Vì vậy, system prompt định hướng phong cách trả lời và độ chi tiết của mô hình khi trả lời*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Khi thử 1 đoạn văn 100 từ tiếng việt, số token chênh nhau trong khoảng 10-20%. Bởi vì tiếng việt có những ký tự như dấu câu và những ký tự đó cũng được tính thành token.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *streaming quan trọng trọng với các bài toán có thời gian trả lời lâu, vì người dùng có thể thấy câu trả lời được xuất hiện ngay thay vì chờ toàn bộ kết quả được trả ra. Điều này giúp giảm cảm giác chờ đợi. Non-streaming phù hợp với các tác vụ cần nhận kết quả hoàn chỉnh 1 lần ví dụ: phân loại dữ liệu, trích xuất thông tin, trả về json hoặc câu trả lười ngắn*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Exponential backoff giúp giảm số lượng yêu cầu gửi lại khi API đang quá tải bằng cách tăng dần thời gian chờ sau mỗi lần thất bại. Điều này tạo cơ hội để máy chủ phục hồi và giảm nguy cơ tiếp tục bị quá tải.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Persona em lựa chọn là trợ lý AI hỗ trợ lập trình Python và AI. System prompt: "Bạn là một trợ lý AI chuyên về Python và Trí tuệ nhân tạo. Luôn trả lời bằng tiếng Việt, giải thích rõ ràng". Em yêu cầu "trả lời bằng tiếng Việt" để người dùng dễ tiếp cận và "giải thích rõ ràng" để câu trả lời sẽ có những giải thích về cách hoạt động của code được viết ra*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất của trợ lý của em là chưa có bộ nhớ dài hạn nên không thể ghi nhớ các cuộc hội thoại cũ của người dùng giữa các phiên làm việc. Đề xuất cải thiện là xây dựng hệ thống lưu trữ lịch sử hội thoại hoặc thông tin người dùng trong cơ sở dữ liệu và sử dụng kỹ thuật RAG để truy xuất những thông tin liên quan mỗi khi gửi prompt đến mô hình. Điều này giúp trợ lý trả lời nhất quán và cá nhân hóa hơn.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
