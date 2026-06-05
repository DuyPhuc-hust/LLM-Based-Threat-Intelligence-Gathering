# Kịch bản Trình bày Demo

## Chuẩn bị trước khi Demo

Chạy các lệnh sau:

```bash
python3 -m cti_pipeline.cli run-pipeline --source all --days 3650
python3 -m streamlit run dashboard/app.py
```

Nếu Neo4j là một phần của buổi trình diễn demo:

```bash
docker compose up -d neo4j
python3 -m cti_pipeline.cli sync-neo4j
```

## Luồng Trình bày

1. Mở giao diện Dashboard.
2. Hiển thị mục **Threat Feed** (Nguồn cấp Dữ liệu Đe dọa) để giải thích cách thu thập dữ liệu OSINT công khai.
3. Hiển thị mục **Priorities** (Độ ưu tiên) để giải thích về cách tính điểm ưu tiên, lý do đánh giá, bằng chứng cụ thể (evidence) và các hành động khuyến nghị.
4. Hiển thị mục **Trends** (Xu hướng) và chỉ ra sự khác biệt giữa các nguồn tin đồn `social only` (chỉ trên mạng xã hội) so với tin tức đã được kiểm chứng `social + corroborated`.
5. Hiển thị **Entity Explorer** (Khám phá Thực thể) cho mã lỗi bảo mật `CVE-2024-3400`.
6. Chỉ ra các bằng chứng nguồn gốc (source evidence), thực thể đồng xuất hiện (co-mentioned entities) và dữ liệu được làm giàu (enrichment data).
7. Hiển thị giao diện **Report** (Báo cáo) và giải thích rằng mọi thông tin/kết luận phân tích đều có bằng chứng cụ thể hỗ trợ phía sau.
8. Nếu khoá API của mô hình ngôn ngữ lớn (LLM) đã được định cấu hình, nhấp vào **Generate LLM report** (Tạo báo cáo bằng LLM).

## Các Ý chính Cần Trình bày

- Hệ thống xử lý (pipeline) lưu trữ các tài liệu thô tách biệt hoàn toàn so với các thực thể được trích xuất.
- Việc trích xuất bằng biểu thức chính quy (Regex) xử lý tốt các chỉ số mang tính xác thực cụ thể như mã CVE, URL, mã hash, địa chỉ IP và mã định danh kỹ thuật ATT&CK ID.
- Quá trình làm giàu dữ liệu (enrichment) giúp bổ sung mức độ nghiêm trọng từ NVD và ngữ cảnh cụ thể của MITRE ATT&CK.
- Phân tích xu hướng giúp tách biệt tin đồn nhiễu trên mạng xã hội với nguồn dữ liệu tình báo đã được kiểm chứng thực tế.
- Việc chấm điểm ưu tiên được tính toán theo công thức có thể giải thích rõ ràng, không phải là thuật toán đóng (hộp đen).
- Dữ liệu đầu ra từ LLM bị ràng buộc hoàn toàn bởi bằng chứng nguồn và yêu cầu chỉ định rõ mã định danh tài liệu (document IDs).

## Phương án Dự phòng

Nếu các kết nối mạng đến nguồn dữ liệu thực tế bị chặn, dự án có sẵn các mẫu dữ liệu mô phỏng cục bộ cho CISA KEV, Reddit, NVD và MITRE ATT&CK. Thiết kế này giúp đảm bảo buổi demo luôn khả thi và có thể tái hiện được trên các mạng nội bộ của trường học.
