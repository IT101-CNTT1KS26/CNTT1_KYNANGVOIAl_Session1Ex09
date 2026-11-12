# PHÂN TÍCH ẢO GIÁC AI VÀ THIẾT KẾ RAG PROMPT CHUẨN XÁC — MARTX



## PHẦN 1: PHÂN TÍCH NGUYÊN NHÂN ẢO GIÁC (AI HALLUCINATION)

Hiện tượng nhân viên CSKT/Thu mua MartX nhận được câu trả lời sai lệch hoàn toàn từ ChatGPT khi hỏi không kèm tài liệu xuất phát từ bản chất kỹ thuật của các mô hình ngôn ngữ lớn (LLM):

1. **Cơ chế dự đoán xác suất (Probabilistic Prediction):**
LLM không hoạt động như một cơ sở dữ liệu tra cứu tra cứu tĩnh (database lookup), mà hoạt động dựa trên xác suất thống kê từ dữ liệu huấn luyện trên Internet. Khi nhận được một câu hỏi không có ngữ cảnh giới hạn (*"Theo quy định MartX..."*), mô hình cố gắng "điền vào chỗ trống" bằng cách tìm kiếm các mẫu câu tương tự trong các văn bản thương mại phổ biến trên mạng.
2. **Hội chứng tự tin giả tạo (Overconfidence):**
Vì được huấn luyện để luôn đưa ra câu trả lời trôi chảy, có cấu trúc rõ ràng và mang giọng điệu uy quyền, AI đã tự bịa ra những con số phạt nặng (20% và 30 ngày) có vẻ "hợp lý" trong tư duy thống kê của nó, dù thực tế đó hoàn toàn là sản phẩm của **ảo giác (Hallucination)**.
3. **Thiếu lớp neo giữ dữ liệu (Lack of Grounding):**
Việc đặt câu hỏi mà không đính kèm tài liệu gốc khiến AI bị buông lỏng hoàn toàn, dẫn đến việc dùng dữ liệu chung chung trên Internet để áp đặt vào quy định nội bộ đặc thù của doanh nghiệp MartX.


## PHẦN 2: CẤU TRÚC RAG PROMPT HOÀN CHỈNH (GROUNDING & NEGATIVE CONSTRAINT)

Để loại bỏ hoàn toàn rủi ro ảo giác, câu hỏi gửi cho AI bắt buộc phải được cấu trúc theo mô hình **RAG Prompt** gồm 3 thành phần chuẩn xác:

```text
[Context / Văn bản hợp đồng chuẩn]
Dưới đây là điều khoản pháp lý chính thức được trích xuất trực tiếp từ hệ thống quản lý hợp đồng của MartX:
"Điều 8.2 (Quy định xử lý vi phạm chất lượng rau củ): Trường hợp nhà cung cấp giao hàng bị dập nát, MartX áp dụng mức phạt 5% giá trị lô bị dập nát và yêu cầu đổi hàng trong 24h."

[Question / Câu hỏi nghiệp vụ]
Theo quy định MartX, nhà cung cấp giao rau bị dập nát (kể cả trên 10%) thì bị phạt bao nhiêu và có hình thức xử lý thế nào?

[Negative Constraint / Khóa an toàn]
- Ràng buộc tuyệt đối: Bạn chỉ được phép sử dụng thông tin có trong [Văn bản hợp đồng chuẩn] ở trên để đưa ra câu trả lời. 
- Tuyệt đối không tự suy diễn, không tự bịa đặt thêm các mức phạt khác (như 20% hay đình chỉ nhập hàng) dựa trên kinh nghiệm hoặc dữ liệu ngoài đời thực.
- Nếu thông tin chi tiết không có hoặc không được đề cập rõ trong văn bản, bạn PHẢI trả lời chính xác câu: "Không có dữ liệu trong tài liệu."

```