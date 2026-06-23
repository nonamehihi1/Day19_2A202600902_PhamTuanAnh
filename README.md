# Day19_2A202600902_PhamTuanAnh
Bài tập ngày 19, trong này có chứa:
+ Mã nguồn
+ Hình ảnh biểu đồ

📌 3. Bảng so sánh kết quả 20 câu hỏi benchmark giữa Flat RAG và GraphRAG
(Trong báo cáo, bạn hãy chụp ảnh màn hình của cái bảng Pandas DataFrame ở cuối file Notebook để minh hoạ thêm nhé. Dưới đây là phần nhận xét đánh giá dành cho bảng đó)

Nhận xét và Đánh giá hiệu năng:

Flat RAG (Vector Search truyền thống):
Chỉ hoạt động tốt với các câu hỏi tra cứu thông tin trực tiếp (ví dụ: "Công ty nào sản xuất pin X?").
Đối với các câu hỏi đa bước (multi-hop) trong 20 câu benchmark (ví dụ: Mối liên hệ giữa chính phủ, trạm sạc và sự phát triển của xe điện), Flat RAG thường trả về câu trả lời bị phân mảnh, thiếu logic kết nối hoặc thậm chí bị "ảo giác" (hallucination) vì nó chỉ trích xuất các đoạn văn bản có vector tương đồng cao mà bỏ qua ngữ cảnh rộng.
GraphRAG (Knowledge Graph RAG):
Thể hiện sự vượt trội hoàn toàn nhờ thuật toán duyệt đồ thị (Graph Traversal). Các từ khoá trong câu hỏi (seeds) được ánh xạ vào Đồ thị tri thức (Knowledge Graph).
Từ các seeds này, GraphRAG nhảy (hop) sang các thực thể lân cận (vd: Tesla -> hợp tác với -> Panasonic -> phát triển -> Solid-state battery). Nhờ đó, LLM (glm-4-plus) nhận được toàn bộ chuỗi quan hệ cực kỳ mạch lạc làm ngữ cảnh (context), giúp câu trả lời bao quát, chính xác và không bịa đặt thông tin.

📌 4. Phân tích ngắn gọn về chi phí (Token usage, time) khi xây dựng đồ thị
Quá trình xây dựng Đồ thị tri thức (Graph Construction) cho "Tech Company Corpus" đòi hỏi LLM phải đọc và trích xuất thực thể/quan hệ từ toàn bộ văn bản. Dưới đây là thông số thực tế khi chạy:

Token Usage (Lượng Token tiêu thụ):
Quy mô dữ liệu: Hệ thống đã chia tập dữ liệu thành 211 chunks (với CHUNK_SIZE = 1200).
Ước lượng Token: Xử lý toàn bộ văn bản tiêu tốn khoảng 47,277 tokens đầu vào (input tokens).
Chi phí (Cost): Với các model hiện đại (như GLM-4-Plus hoặc GPT-4o-mini), mức giá cho ~50.000 token chưa tới 0.01 USD (khoảng ~200 VNĐ). Mức chi phí này là cực kỳ rẻ cho việc lập chỉ mục (indexing) một lần để tạo ra một Knowledge Graph vĩnh viễn.
Time (Thời gian xây dựng đồ thị):
Quá trình Extract Triples là tốn thời gian nhất vì phải gọi API LLM liên tục 211 lần.
Khi sử dụng API của Zhipu (glm-4-plus), thời gian xử lý mất khoảng 2 - 5 phút (tùy thuộc vào độ trễ mạng lúc gọi API).
Bước tạo Vector nhúng (Embedding) bằng Ollama nội bộ chạy cực kỳ nhanh, gần như hoàn thành trong vòng vài chục giây nhờ tài nguyên CPU/GPU trực tiếp của Google Colab.
Kết luận: Chi phí tính toán và token để xây dựng GraphRAG lớn hơn so với Flat RAG (vì Flat RAG chỉ băm vector chứ không cần LLM đọc hiểu trích xuất đồ thị). Tuy nhiên, chi phí một lần này hoàn toàn xứng đáng với chất lượng câu trả lời vượt trội mà hệ thống mang lại ở khâu truy vấn.
