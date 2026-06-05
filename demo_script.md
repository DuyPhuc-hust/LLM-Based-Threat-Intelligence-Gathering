# 🎤 Kịch Bản Demo - Thu Thập Tin Tức Tình Báo An Ninh Mạng Bằng LLM

> **Dự án**: OSINT Cyber Threat Intelligence Pipeline  
> **Môn học**: Penetration Testing  
> **Thời lượng ước tính**: 15–20 phút  
> **Điều kiện**: Backend API (`uvicorn` port 8000) và Frontend (`npm run dev` port 5173) đã chạy sẵn.

---

## 📋 Tóm Tắt Dự Án (dành cho người trình bày)

Dự án xây dựng một **hệ thống tự động thu thập và phân tích Threat Intelligence** từ nhiều nguồn OSINT công khai, bao gồm:

| Tầng | Chức năng | Công nghệ |
|------|-----------|-----------|
| **Collection (Thu thập)** | Thu thập dữ liệu từ 11+ nguồn OSINT | Bộ thu thập Python, REST APIs, RSS |
| **Extraction (Trích xuất)** | Tự động trích xuất CVE, IP, domain, hash, mã ATT&CK | NLP dựa trên Regex |
| **Enrichment (Làm giàu)** | Làm giàu thông tin CVE từ NVD, MITRE ATT&CK, FIRST EPSS | Làm giàu dữ liệu qua API |
| **Analysis (Phân tích)** | Chấm điểm ưu tiên, phân tích độ bao phủ OSINT | Thuật toán chấm điểm |
| **Visualization (Trực quan)** | Giao diện React Command Center | React + Recharts |
| **Reporting (Báo cáo)** | Báo cáo Markdown, Sigma rules, ATT&CK layer, STIX bundle | Xuất báo cáo đa định dạng |
| **LLM Chat (Trò chuyện AI)** | Trợ lý ảo AI trả lời dựa trên bằng chứng thực tế | Gemini API + RAG |
| **Graph (Đồ thị)** | Đồ thị mạng tri thức ngữ nghĩa | Động cơ đồ thị ngữ nghĩa |

---

## PHẦN 1: GIỚI THIỆU TỔNG QUAN (2–3 phút)

### 🖥️ Thao tác
- Mở các slide thuyết trình hoặc file README.md trên IDE.
- Chỉ vào sơ đồ kiến trúc hệ thống (nếu có).

### 🎙️ Lời nói

> "Xin chào thầy cô và các bạn. Hôm nay em xin đại diện nhóm trình bày dự án: **LLM-Based Threat Intelligence Gathering** (Thu thập tin tức tình báo mối đe dọa bằng LLM) — một hệ thống pipeline tự động thu thập, trích xuất, phân tích và trực quan hóa dữ liệu Cyber Threat Intelligence từ các nguồn OSINT công khai."
>
> "Dự án gồm các tầng chính sau:
> - **Tầng Collection (Thu thập)**: Thu thập dữ liệu mối đe dọa từ **11 nguồn OSINT** công khai bao gồm CISA KEV, Reddit, X (Twitter), PhishTank, AlienVault OTX, URLhaus, ThreatFox, GitHub Security Advisories, các nguồn RSS tin tức bảo mật (The Hacker News, BleepingComputer, KrebsOnSecurity), cũng như các CERT quốc tế (CERT-FR, JPCERT/CC, NCSC UK, NCSC NL).
> - **Tầng Extraction (Trích xuất)**: Tự động phát hiện và trích xuất các thực thể bảo mật như mã CVE, IP, domain, URL, mã hash (MD5/SHA1/SHA256), và mã kỹ thuật tấn công MITRE ATT&CK bằng các mẫu regex chuẩn hóa.
> - **Tầng Enrichment (Làm giàu)**: Làm giàu dữ liệu CVE với siêu dữ liệu từ NVD (điểm CVSS, mức độ nghiêm trọng), ánh xạ tên kỹ thuật ATT&CK và cập nhật điểm xác suất khai thác FIRST EPSS.
> - **Tầng Analysis (Phân tích)**: Chấm điểm độ ưu tiên của mối đe dọa bằng thuật toán đa yếu tố (lượt đề cập, số lượng nguồn, trạng thái kiểm chứng chéo, mức nghiêm trọng từ NVD, tính cập nhật, độ tin cậy của nguồn) và đánh giá độ bao phủ dữ liệu OSINT.
> - **Tầng Visualization (Trực quan hóa)**: Giao diện Command Center bằng React với 4 phân hệ chính.
> - **Tầng Reporting (Báo cáo & Phản ứng)**: Xuất báo cáo dưới nhiều định dạng bao gồm Markdown, luật phát hiện Sigma, bản đồ MITRE ATT&CK Navigator, gói STIX 2.1 phục vụ chia sẻ thông tin, và Intelligence Pack JSON.
> - **LLM Chat (Trợ lý AI)**: Chatbot AI sử dụng RAG tích hợp Gemini API để trả lời các truy vấn dựa trên bằng chứng thực tế trong database và tự động xác thực trích dẫn để chống hiện tượng AI bịa đặt thông tin."
>
> "Hệ thống của chúng em được thiết kế hoàn toàn phục vụ mục đích **phòng thủ và giáo dục** (defensive & educational) — chỉ thu thập siêu dữ liệu (metadata) và Chỉ số nhận diện tấn công (IOCs), tuyệt đối không tải về hay thực thi bất kỳ mã độc nào."

---

## PHẦN 2: PIPELINE THU THẬP DỮ LIỆU TIN TỨC (1 phút)

### 🖥️ Thao tác
- Di chuột chỉ vào nút **"▶ Run Live Update"** ở góc trên bên phải thanh tiêu đề (header) trên giao diện web, nhưng **KHÔNG** click vào nút này.
- Giới thiệu ngắn gọn tùy chọn chạy lệnh qua giao diện dòng lệnh CLI.

### 🎙️ Lời nói

> "Để nạp dữ liệu cho trang Command Center, hệ thống vận hành một pipeline thu thập thông tin tình báo. Pipeline này có thể được kích hoạt trực tiếp bằng cách nhấn nút **Run Live Update** trên giao diện web, hoặc chạy thông qua dòng lệnh CLI.
>
> Để tiết kiệm thời gian demo, em đã chạy trước pipeline này vì việc hoàn thành toàn bộ chu kỳ thu thập, trích xuất và làm giàu dữ liệu thực tế từ internet sẽ mất khoảng 2 đến 3 phút để truy vấn và tải tất cả các feed trực tiếp.
>
> Khi vận hành, pipeline sẽ thực hiện tuần tự các bước sau:
> 1. **Collect (Thu thập)** — Gửi yêu cầu API hoặc cào các RSS feed từ hơn 11 nguồn OSINT để lấy về các tài liệu mới nhất.
> 2. **Extract (Trích xuất)** — Phân tích nội dung văn bản để trích xuất thực thể bảo mật (như CVE, IP, và domain).
> 3. **Enrich (Làm giàu)** — Truy vấn lấy điểm CVSS từ NVD và điểm xác suất bị khai thác thực tế từ FIRST EPSS.
> 4. **Report (Báo cáo)** — Tự động biên soạn báo cáo cục bộ dưới dạng Markdown, sinh luật phát hiện Sigma, và tạo các gói pack dữ liệu tình báo.
>
> Tất cả bộ thu thập đều có dữ liệu dự phòng (fallback data) cấu hình sẵn nên demo vẫn hoạt động mượt mà ngay cả khi offline, nhưng khi truy vấn live API sẽ mang về các mối đe dọa thời gian thực. Bây giờ dữ liệu đã sẵn sàng, chúng ta cùng khám phá giao diện Command Center."

---

## PHẦN 3: MISSION CONTROL — DASHBOARD TỔNG QUAN (3–4 phút)

### 🖥️ Thao tác
1. Click vào menu bên trái: **"Mission Control"**
2. Đảm bảo bạn đang ở tab **"Dashboard"** — chỉ vào các thẻ số liệu (metric cards) ở trên cùng.
3. Cuộn xuống dưới để hiển thị các bảng: **Top Priority Signals**, **Corroboration Matrix**, **OSINT Coverage Posture**, **Source Type Mix**, và **Trending Entities**.
4. Click chuyển sang tab **"Priority Queue"**.
5. Click vào một lỗ hổng CVE trong danh sách bên trái.
6. Chỉ vào panel thông tin **"Evidence And Actions"** ở phía bên phải.
7. Click vào tab **"Pipeline Status"**.

### 🎙️ Lời nói

> "Đây là trang **Mission Control** — bảng điều khiển trung tâm dành cho các nhà phân tích bảo mật."

#### Tab Dashboard:

> "Ở hàng số liệu tổng quan trên cùng, chúng ta thấy:
> - **Documents (Tài liệu)**: Tổng số bài báo, tin tức và báo cáo bảo mật đã thu thập.
> - **Entities (Thực thể)**: Tổng số thực thể bảo mật đã được bóc tách thành công.
> - **Sources (Nguồn tin)**: Số nguồn OSINT đang hoạt động ổn định.
> - **Languages (Ngôn ngữ)**: Các ngôn ngữ được hỗ trợ (Tiếng Anh, Tiếng Pháp, Tiếng Nhật, Tiếng Hà Lan).
> - **Critical / High**: Số lượng phát hiện có mức độ rủi ro Nguy cấp và Cao."
>
> *(Chỉ vào Top Priority Signals)*
>
> "Bảng **Top Priority Signals** liệt kê các phát hiện quan trọng nhất được xếp hạng dựa trên điểm ưu tiên tự động. Ví dụ, CVE này có điểm số 85 — mức Nguy cấp — vì nó được nhắc đến trên nhiều nguồn độc lập, có điểm CVSS từ NVD rất cao, và đã được xác nhận chéo bởi cả nguồn cộng đồng lẫn nguồn chính thức."
>
> *(Chỉ vào Corroboration Matrix)*
>
> "**Corroboration Matrix** (Ma trận xác nhận chéo) thể hiện trạng thái kiểm chứng của dữ liệu: có bao nhiêu thực thể được xác nhận bởi cả cộng đồng và chính thống ('social + corroborated'), bao nhiêu chỉ có trên nguồn chính thức ('official/structured only'), hoặc chỉ có trên mạng xã hội ('social only'). Sự phân bố này ảnh hưởng trực tiếp đến điểm ưu tiên mối đe dọa."
>
> *(Chỉ vào OSINT Coverage Posture)*
>
> "Điểm số **OSINT Coverage Score** đánh giá độ phủ và chất lượng thu thập tin tức tình báo dựa trên số tài liệu, chủng loại nguồn, sự đa dạng ngôn ngữ, tính cân bằng giữa mạng xã hội và nguồn chính thống, và các feed tin cậy (trusted feeds)."
>
> *(Chỉ vào Source Type Mix)*
>
> "Bảng **Source Type Mix** phân tích tỷ lệ các loại nguồn dữ liệu trong hệ thống — hiển thị phần trăm của các feed mã độc, structured feed (dữ liệu có cấu trúc), cảnh báo từ CERT, tin tức bảo mật và dữ liệu mạng xã hội. Điều này giúp đảm bảo chúng ta duy trì một nguồn cấp thông tin cân bằng."
>
> *(Chỉ vào Trending Entities)*
>
> "Cuối cùng, biểu đồ **Trending Entities** nêu bật các chỉ số bảo mật hoạt động tích cực nhất trong hệ thống, so sánh tổng số lượt đề cập với số lượng nguồn duy nhất nhắc đến chúng, giúp phân tích viên nhanh chóng phát hiện mối đe dọa nào đang bùng phát."

#### Tab Priority Queue (Hàng đợi Ưu tiên):

> *(Click tab "Priority Queue")*
>
> "Tab **Priority Queue** là nơi các nhà phân tích phân loại và xử lý sự cố. Em sẽ click thử vào một mã CVE..."
>
> *(Click vào một dòng CVE)*
>
> "Panel **Evidence And Actions** bên phải sẽ hiển thị:
> - **Điểm số và Mức độ ưu tiên** (Nguy cấp, Cao, Trung bình, Thấp).
> - **Analyst Verdict (Khuyến nghị phân tích)**: Đưa ra hành động tự động đề xuất (ví dụ: 'Vá/Khắc phục ngay' cho các lỗ hổng nguy cấp).
> - **Source Reliability**: Độ tin cậy tính toán của các nguồn tin.
> - **Rationale (Căn cứ chấm điểm)**: Giải thích chi tiết **tại sao** lỗ hổng đạt điểm số này (hiển thị trọng số đóng góp từ lượt đề cập, số nguồn, điểm NVD, thời gian phát hiện...).
> - **Recommended Actions**: Các bước hành động khắc phục cụ thể cho đội kỹ thuật.
> - **Evidence Documents**: Đường link trực tiếp đến các tài liệu gốc làm bằng chứng."
>
> *(Chỉ vào Recommended Actions)*
>
> "Ở mục **Recommended Actions**, hệ thống vạch ra các bước xử lý cụ thể. Ví dụ với một lỗ hổng Nguy cấp, nó khuyến nghị cài đặt bản vá ngay lập tức, phân vùng mạng cách ly, hoặc thiết lập luật phát hiện cho các chỉ số nhận diện liên quan."
>
> *(Chỉ vào Evidence Documents)*
>
> "Bên dưới cùng, phần **Evidence** liệt kê tất cả các tài liệu OSINT thực tế đề cập đến mối đe dọa này. Khi click vào, phân tích viên có thể mở trực tiếp bài viết gốc để tự xác minh thông tin với một quy trình kiểm toán hoàn chỉnh."
>
> "Điều này làm cho hệ thống của chúng em có tính **giải thích cao** (explainable) — mọi khuyến nghị và điểm số đều có lý do rõ ràng và có bằng chứng đối chiếu về nguồn gốc."

#### Tab Pipeline Status:

> *(Click tab "Pipeline Status")*
>
> "Tại đây, chúng em có thể kiểm tra các thông số kỹ thuật của hệ thống như đường dẫn cơ sở dữ liệu, cấu hình LLM đang sử dụng và thực hiện cập nhật thủ công."

---

## PHẦN 4: INTELLIGENCE EXPLORER (3–4 phút)

### 🖥️ Thao tác
1. Click vào menu bên trái: **"Intelligence Explorer"**
2. Ở tab **"Threat Feed"**, cuộn qua danh sách tài liệu và thử gõ tìm kiếm.
3. Click sang tab **"Entity Workbench"**.
4. Click vào một thực thể (ví dụ: một CVE) trong danh sách bên trái.
5. Chỉ vào mục **Entity Profile** bên phải: biểu đồ dòng thời gian, ngữ cảnh làm giàu, các thực thể đồng xuất hiện (co-mentions), và danh sách tài liệu bằng chứng.
6. Click sang tab **"Source Coverage"**.
7. Chỉ vào bảng Source Reliability Matrix, biểu đồ Language Diversity, các thế mạnh (strengths), lỗ hổng (gaps) và các khuyến nghị.

### 🎙️ Lời nói

#### Tab Threat Feed:

> "**Intelligence Explorer** là công cụ giúp các nhà phân tích đào sâu vào cơ sở dữ liệu của chúng em."
>
> *(Tab Threat Feed)*
>
> "Tab **Threat Feed** hiển thị tất cả các tài liệu đã thu thập. Chúng ta có thể tìm kiếm hoặc lọc theo tên nguồn, tiêu đề hoặc nội dung. Mỗi bản ghi đều hiển thị rõ tên nguồn, tiêu đề (click để mở link gốc), loại nguồn (structured feed, tin tức, mạng xã hội, CERT, vendor...) và ngày xuất bản."
>
> *(Gõ từ khóa 'phishing' hoặc 'CVE' vào thanh search)*
>
> "Ví dụ, khi gõ từ khóa 'phishing', hệ thống lập tức lọc ra các cảnh báo lừa đảo trực tuyến và cảnh báo từ cộng đồng từ nguồn PhishTank và các nền tảng khác."

#### Tab Entity Workbench (Bàn làm việc Thực thể):

> *(Click tab "Entity Workbench")*
>
> *(Gõ thử một mã CVE như 'CVE-2024' vào thanh tìm kiếm và chọn loại thực thể là 'CVE' trong bộ lọc)*
>
> "Tab **Entity Workbench** được thiết kế để phân tích sâu từng thực thể. Cột bên trái là danh mục **Entity Index** liệt kê tất cả các thực thể đã trích xuất: CVE, domain, IP, hash, và kỹ thuật ATT&CK. Để hỗ trợ tìm kiếm nhanh giữa hàng nghìn thực thể bảo mật, chúng em đã tích hợp thêm một thanh tìm kiếm trực tiếp và bộ lọc theo loại thực thể (như CVE, IP, Domain, Hash...) giúp thu hẹp danh sách ngay lập tức."
>
> *(Click chọn một mã CVE cụ thể từ danh sách đã lọc)*
>
> "Khi chọn một thực thể, thông tin **Entity Profile** chi tiết sẽ hiển thị bên phải:
> - **Biểu đồ Dòng thời gian**: Theo dõi tần suất xuất hiện theo thời gian, cho biết mối đe dọa này đang có xu hướng tăng hay giảm.
> - **Ngữ cảnh làm giàu (Enrichment)**: Hiển thị điểm số CVSS và mô tả từ NVD, cùng với điểm xác suất bị khai thác thực tế EPSS.
> - **Co-Mentions (Đồng xuất hiện)**: Liệt kê các thực thể khác thường được nhắc chung với thực thể này — giúp xác định các nhà cung cấp, sản phẩm bị ảnh hưởng và kỹ thuật tấn công liên quan.
> - **Evidence Documents**: Danh sách toàn bộ tài liệu nguồn đề cập đến thực thể này."
>
> "Nhà phân tích có thể click vào bất kỳ thực thể đồng xuất hiện nào để nhảy thẳng sang profile của thực thể đó, tạo ra một **quy trình điều tra liền mạch**."

#### Tab Source Coverage (Độ bao phủ Nguồn):

> *(Click tab "Source Coverage")*
>
> "Tab **Source Coverage** phân tích chất lượng của các nguồn tin tình báo:
> - **Ma trận Độ tin cậy Nguồn**: Hiển thị thông số của từng nguồn: phân loại, ngôn ngữ, số tài liệu đóng góp, mức độ tin cậy (Cao, Trung bình, Cộng đồng) và thời gian cập nhật cuối.
> - **Đa dạng Ngôn ngữ**: Trực quan hóa tỷ lệ ngôn ngữ của dữ liệu (Tiếng Anh, Tiếng Pháp, Tiếng Nhật, Tiếng Hà Lan).
> - **Phân bổ Danh mục Nguồn**: Thể hiện tỷ lệ các loại nguồn thu thập.
> - **Thế mạnh, Điểm yếu và Khuyến nghị**: Chỉ ra các hành động chiến lược, như mở rộng nguồn mạng xã hội hoặc bổ sung các nguồn phi tiếng Anh để nâng cao năng lực thu thập của hệ thống."

---

## PHẦN 5: KNOWLEDGE GRAPH (2–3 phút)

### 🖥️ Thao tác
1. Click vào menu bên trái: **"Knowledge Graph"**
2. Chọn một thực thể từ dropdown/selector (ví dụ: một CVE hoặc domain độc hại).
3. Chỉ vào sơ đồ trực quan **Semantic Graph** — gọi tên các node (nguồn tin, tài liệu, thực thể đang chọn, thực thể liên quan) và các liên kết edge (published, mentions, co_occurs, affects vendor, listed in KEV...).
4. Chỉ ra các phân hệ thông tin: **Analyst Summary**, **Evidence Trail**, và bảng **Triples** bên dưới.
5. Chuyển đổi qua lại giữa các kính lọc (lenses): **Risk**, **Evidence**, **Intel**, và **Triples**.
6. Thử bật tắt các bộ lọc: **Hide Noise**, lọc **Entity Types**, hoặc lọc **Risk Levels**.
7. Click vào một node trên đồ thị để hiển thị thông tin chi tiết.

### 🎙️ Lời nói

> "**Knowledge Graph** (Đồ thị tri thức) là một trong những tính năng nổi bật của dự án. Hệ thống tự động dựng lên một đồ thị ngữ nghĩa tương tác ánh xạ tất cả các mối quan hệ xung quanh thực thể được chọn."
>
> *(Chọn một lỗ hổng CVE)*
>
> "Khi chọn một thực thể, hệ thống sẽ dựng đồ thị gồm:
> - **Các Node (Nút)**: Đại diện cho nguồn tin, tài liệu bằng chứng, thực thể trọng tâm đang điều tra và các thực thể liên quan (vendor, sản phẩm, kỹ thuật ATT&CK, IP, domain, danh mục KEV).
> - **Các Edge (Cạnh liên kết)**: Thể hiện quan hệ ngữ nghĩa như 'xuất bản', 'nhắc đến', 'ảnh hưởng đến vendor', 'ảnh hưởng đến sản phẩm', 'nằm trong KEV', 'có hành vi tống tiền', và 'đồng xuất hiện'."
>
> *(Chỉ vào Analyst Summary)*
>
> "Phần **Analyst Summary** cung cấp kết luận nhanh cho quản lý: *'CVE-2024-XXXX là lỗ hổng đã bị khai thác trong thực tế, ảnh hưởng đến nhà cung cấp ABC, sản phẩm XYZ, với mức độ rủi ro NGUY CẤP — nằm trong danh mục CISA KEV và được xác nhận bởi 5 nguồn tin.'*"
>
> *(Chuyển sang kính lọc Evidence)*
>
> "Khi chuyển sang **Evidence Lens** (Kính lọc bằng chứng), hệ thống sẽ lọc bỏ các nút siêu dữ liệu, chỉ hiển thị nguồn tin và tài liệu gốc để nhà phân tích dễ dàng truy vết nguồn gốc thông tin."
>
> *(Bật công tắc "Hide Noise")*
>
> "Bộ lọc **Hide Noise** giúp làm sạch đồ thị bằng cách ẩn đi các domain tham chiếu phổ biến (như mitre.org hay cisa.gov) để làm nổi bật các kết nối thực tế."
>
> *(Lọc theo Risk Level: Critical, High)*
>
> "Lọc theo **Risk Level** cho phép nhà phân tích cô lập các mối đe dọa nghiêm trọng nhất ngay lập tức."
>
> *(Click vào một Node trên đồ thị)*
>
> "Khi em click vào một nút bất kỳ trên đồ thị, bảng **Node Inspector** bên phải sẽ hiển thị các thông tin chi tiết: phân loại thực thể, mức rủi ro, mô tả, và một nút để chuyển trọng tâm điều tra sang thực thể mới này."
>
> *(Click vào một đường liên kết edge trên đồ thị)*
>
> "Tương tự, click vào đường nối giữa các nút sẽ mở **Relationship Inspector** (Thanh tra mối quan hệ). Mục này hiển thị quan hệ ngữ nghĩa, điểm tin cậy, lý do logic, và danh sách các ID tài liệu làm bằng chứng chứng minh."
>
> *(Cuộn xuống bảng Semantic Triples Feed hoặc chọn lens Triples)*
>
> "Bên dưới đồ thị canvas, bảng **Semantic Triples Feed** chuyển đổi các mối quan hệ đồ thị này thành một dạng bảng bộ ba ngữ nghĩa Subject-Predicate-Object có thể tìm kiếm. Phân tích viên có thể lọc theo từ khóa để nhanh chóng xác định các mối liên kết quan trọng, ví dụ như ánh xạ lỗ hổng nào đang ảnh hưởng đến phần mềm nào."

---

## PHẦN 6: REPORTS & DETECTIONS (3–4 phút)

### 🖥️ Thao tác
1. Click vào menu bên trái: **"Reports & Detections"**
2. Đang ở tab **"Analyst Reports"**:
   - Chỉ vào panel **"Focused Report Builder"** bên trái. Chọn một phạm vi báo cáo (ví dụ: "Malware, Ransomware & Indicators") và click **"Generate Focused Report"**. Hiển thị báo cáo Markdown được biên soạn ở panel bên phải.
   - Chỉ vào panel **"Gemini Analyst Report"** bên trái. Click **"Generate"** để gọi API Gemini và kết xuất nội dung tóm tắt của AI.
3. Click chuyển sang tab **"ATT&CK Coverage"** và chỉ vào bảng kỹ thuật.
4. Click chuyển sang tab **"Sigma Detections"** và cuộn qua các quy tắc phát hiện.
5. Click chuyển sang tab **"Export Center"** và giới thiệu các tùy chọn tải về.

### 🎙️ Lời nói

#### Analyst Reports Tab:

> "Phân hệ **Reports & Detections** giúp chuyển đổi cơ sở dữ liệu CTI của chúng em thành các báo cáo phục vụ quản lý và vận hành."
>
> *(Chỉ vào Focused Report Builder)*
>
> "Tại tab **Analyst Reports**, chúng em cung cấp hai bộ tạo báo cáo riêng biệt. Đầu tiên là **Focused Report Builder** hoạt động dựa trên quy tắc. Công cụ này truy vấn trực tiếp cơ sở dữ liệu SQLite tại local để lắp ghép tức thì một báo cáo dạng Markdown. Chúng em có thể giới hạn phạm vi theo danh mục cụ thể — như Malware hay Vulnerability — hoặc thậm chí cô lập báo cáo cho duy nhất một thực thể. Tính năng này chạy offline hoàn toàn và không tốn chi phí gọi API."
>
> *(Click nút Generate Focused Report)*
>
> "Báo cáo Markdown sau khi sinh sẽ hiển thị chi tiết phần tóm tắt điều hành, cơ cấu nguồn dữ liệu, các phát hiện ưu tiên và lịch sử bằng chứng."
>
> *(Chỉ vào Gemini Analyst Report và click Generate)*
>
> "Thứ hai là bộ báo cáo **Gemini Analyst Report**. Khi chạy, hệ thống đóng gói các thông số tình báo của database và gửi sang API Gemini làm context. Gemini sẽ đóng vai trò như một chuyên gia phân tích an ninh mạng để viết một báo cáo thuyết minh tự nhiên, cung cấp các phân tích xu hướng và khuyến nghị chiến lược sâu sắc mà số liệu cứng trong database không tự giải thích được."

#### ATT&CK Coverage Tab:

> *(Click tab "ATT&CK Coverage")*
>
> "Tiếp theo là tab **ATT&CK Coverage**. Trang này tự động ánh xạ các mã kỹ thuật ATT&CK đã trích xuất từ dữ liệu OSINT lên khung ma trận MITRE ATT&CK.
>
> Ở trên cùng, chúng ta thấy 4 chỉ số chính:
> - **Valid Techniques**: 130 kỹ thuật MITRE ATT&CK hợp lệ đã được xác minh trong hệ thống.
> - **Tactics Covered**: 15 chiến thuật MITRE đang được bao phủ trong dữ liệu của chúng ta.
> - **Ignored Invalid IDs**: 12 định dạng mã không hợp chuẩn Enterprise đã bị loại bỏ để làm sạch dữ liệu.
> - **Top Source Count**: 2 nguồn kiểm chứng tối đa cho một kỹ thuật.
>
> Phía dưới là biểu đồ cột **Top 20 ATT&CK Techniques Heat** thể hiện tần suất xuất hiện của các kỹ thuật phổ biến nhất. Sơ đồ nhiệt này giúp nhà quản lý nhanh chóng nhận biết kỹ thuật nào đang bị tin tặc lạm dụng nhiều nhất trong thời gian gần đây.
>
> Bên phải là hộp **Navigator Layer Export** chứa nội dung file cấu hình JSON. Bằng cách nhấn nút **Download Layer**, nhà phân tích có thể tải file này về để import trực tiếp vào công cụ MITRE ATT&CK Navigator trực tuyến phục vụ việc trực quan hóa phòng thủ.
>
> Cuộn xuống dưới là bảng **ATT&CK Technique Inventory** (Danh mục Kỹ thuật). Bảng này cho phép tìm kiếm và hiển thị đầy đủ thông tin: mã kỹ thuật (như `T1562.001` hay `T1078`), tên kỹ thuật, các chiến thuật liên quan (ví dụ: `Stealth`, `Persistence`), số lượt đề cập, điểm số ưu tiên, trạng thái kiểm chứng và khoảng thời gian quan trắc."

#### Sigma Detections Tab:

> *(Click tab "Sigma Detections")*
>
> "Bây giờ, chúng ta sẽ chuyển sang tab **Sigma Detections** với công cụ **Sigma Detection Builder 2.0**. Trang này kết nối khoảng trống giữa thông tin tình báo mối đe dọa (CTI) và phòng thủ vận hành (SecOps) bằng cách tự động sinh các quy tắc phát hiện tấn công cho hệ thống SIEM.
>
> Ở phía bên trái, phân hệ cung cấp các bộ lọc cấu hình thời gian thực:
> - **Target Threat Types**: Chọn lọc loại mối đe dọa để viết luật (CVE, kỹ thuật ATT&CK, Domain, IP, hoặc URL độc hại).
> - **Minimum Severity**: Dropdown giới hạn mức độ nghiêm trọng tối thiểu của luật.
> - **Override Category & Product**: Cho phép tùy chỉnh log nguồn (ví dụ thiết lập ghi đè category là `process_creation` và product là `windows` để khớp với quy chuẩn SIEM nội bộ).
> - **Maximum Rules Limit**: Thanh trượt giới hạn số lượng luật xuất bản.
>
> Ở phía bên phải, bảng **Sigma Hunting Preview** hiển thị nội dung các file cấu hình YAML tương ứng trong thời gian thực. Khi chúng ta thay đổi cấu hình bên trái, các quy tắc bên phải sẽ tự động cập nhật ngay lập tức.
>
> Các quy tắc này tuân thủ chuẩn Sigma quốc tế. Mỗi luật bao gồm: tiêu đề và mô tả, các đường link bằng chứng gốc (`references`) trỏ trực tiếp về nguồn tin cậy ban đầu giúp dễ dàng đối chiếu, cấu hình logsource, logic phát hiện (`detection`) cụ thể (như quét dòng lệnh CommandLine chứa mã CVE), và danh sách các trường hợp dương tính giả (`falsepositives`) thường gặp để kỹ sư SIEM tinh chỉnh luật trước khi đưa vào vận hành."

#### Export Center Tab:

> *(Click tab "Export Center")*
>
> "Tab **Export Center** là nơi tập trung cho phép tải về toàn bộ các định dạng đầu ra chuẩn hóa:
> - 📄 **Markdown Report** — báo cáo đọc cho ban quản lý.
> - 📦 **Intelligence Pack JSON** — chứa toàn bộ dữ liệu thô có cấu trúc của hệ thống.
> - 🔍 **Sigma Detection Rules** — các luật hunting cho hệ thống SIEM.
> - 🗺️ **ATT&CK Navigator Layer** — file layer import vào MITRE Navigator.
> - 📋 **STIX 2.1 Bundle** — định dạng chuẩn quốc tế để chia sẻ threat intel với các tổ chức liên minh."

---

## PHẦN 7: LLM CHAT — AI ANALYST ASSISTANT (2–3 phút)

> [!IMPORTANT]
> Phần này yêu cầu cấu hình LLM (Gemini API key). Nếu chưa cấu hình, hãy giải thích kiến trúc và bỏ qua demo trực tiếp.

### 🖥️ Thao tác
1. Click vào biểu tượng chat widget nổi (hình tròn có icon bộ não Brain) ở góc dưới cùng bên phải màn hình.
2. Nhập câu hỏi mẫu: `"What are the most critical CVEs we should prioritize?"`
3. Chờ phản hồi của AI — chỉ ra các mục trích dẫn (citations), thực thể liên quan (related entities), gợi ý câu hỏi tiếp theo (suggested followups), lưu ý hạn chế (caveats), và **hình động trực quan hóa chuỗi xử lý lỗ hổng (Vulnerability Flow)** xuất hiện sinh động ngay trong tin nhắn trả về.
4. Nhập câu hỏi thứ hai: `"Tell me about phishing threats in our data"`.
5. Chỉ ra **hình động phân tích mối tương quan email lừa đảo (Phishing Link Correlation Analyzer)** tự động kích hoạt.

### 🎙️ Lời nói

> "Tính năng cuối cùng em muốn giới thiệu là **LLM Chat** — trợ lý phân tích ảo AI tích hợp Gemini."
>
> *(Click vào biểu tượng bong bóng chat)*
>
> "Chatbot này vận hành trên kiến trúc **RAG (Retrieval-Augmented Generation)**. Khi chúng ta đặt câu hỏi:
> 1. Động cơ sẽ bóc tách các thực thể và từ khóa cốt lõi từ câu hỏi của người dùng.
> 2. Truy vấn cơ sở dữ liệu SQLite để tìm các tài liệu liên quan nhất.
> 3. Đóng gói các tài liệu này cùng với các thông số đo lường, chỉ số ưu tiên hệ thống thành một gói ngữ cảnh (Context).
> 4. Gửi gói ngữ cảnh này kèm theo System Prompt yêu cầu nghiêm ngặt cho LLM.
> 5. Phía máy chủ sẽ kiểm chứng câu trả lời của AI và loại bỏ bất kỳ trích dẫn nào không có thực trong database gốc."
>
> *(Nhập câu hỏi: "What are the most critical CVEs we should prioritize?")*
>
> "Kết quả trả về rất có cấu trúc, bao gồm:
> - **Câu trả lời**: Giải thích chi tiết và trích dẫn trực tiếp Document ID.
> - **Citations (Trích dẫn)**: Các link tương tác dẫn thẳng đến tài liệu bằng chứng.
> - **Related Entities (Thực thể liên quan)**: Gợi ý các thực thể liên đới để phân tích tiếp.
> - **Suggested Followups**: Các câu hỏi gợi ý nghiên cứu tiếp theo.
> - **Caveats (Lưu ý hạn chế)**: Cảnh báo tự động về chất lượng dữ liệu (ví dụ: dữ liệu chỉ lấy từ mạng xã hội chưa được xác thực chéo).
> - **Contextual Animations (Hình động ngữ cảnh)**: Hệ thống tự động nhận diện chủ đề trong câu hỏi/câu trả lời để kết xuất các hình động mô phỏng trực quan tương tác (như sơ đồ Triage Flow cho CVE, sơ đồ mối liên kết cho Phishing, màn hình Hunting Log cho Sigma, hoặc vòng quét Radar cho các mối đe dọa chung). Điều này giúp phân tích viên nắm bắt nhanh bản chất sự cố và làm cho hệ thống trông thông minh, trực quan hơn."
>
> "Kiến trúc này giúp ngăn chặn hiện tượng AI bịa đặt thông tin (hallucination): AI **chỉ được phép trả lời dựa trên những gì có trong database** và mọi trích dẫn đều được xác thực phía server."

---

## PHẦN 8: KẾT LUẬN & CHI TIẾT KỸ THUẬT (1–2 phút)

### 🖥️ Thao tác
- Quay lại slide thuyết trình hoặc hiển thị cấu trúc thư mục dự án trên IDE.
- (Tùy chọn) Chạy test suite trên terminal: `python -m pytest tests/ -v`

### 🎙️ Lời nói

> "Để kết luận, em xin tóm tắt cấu trúc kỹ thuật của dự án:
> 
> **Backend (Python)**:
> - `cti_pipeline/collectors/` — Gồm 9 module thu thập cho 11+ nguồn OSINT.
> - `cti_pipeline/extractors/` — Trích xuất thực thể bằng Regex và NLP.
> - `cti_pipeline/enrichment/` — Module làm giàu dữ liệu từ NVD, ATT&CK, và FIRST EPSS.
> - `cti_pipeline/analysis/` — Logic chấm điểm ưu tiên và phân tích độ bao phủ nguồn.
> - `cti_pipeline/reports/` — Động cơ kết xuất báo cáo cho 5 định dạng đầu ra.
> - `cti_pipeline/graph/` — Động cơ dựng đồ thị tri thức ngữ nghĩa (hơn 1.500 dòng code).
> - `cti_pipeline/llm/` và `cti_pipeline/chat/` — Xử lý RAG và tích hợp LLM.
> - `cti_pipeline/storage/` — Trừu tượng hóa cơ sở dữ liệu SQLite.
> - `cti_pipeline/api/` — API REST sử dụng FastAPI (gồm 22 endpoints).
> 
> **Frontend (React + TypeScript)**:
> - 4 bảng điều khiển chính tương tác cao.
> - Widget chat nổi thông minh.
> - Trực quan hóa biểu đồ sử dụng thư viện Recharts.
> 
> **Tính An toàn & Toàn vẹn (Safety & Integrity)**:
> - Thiết kế thuần phòng thủ (chỉ thu thập siêu dữ liệu chỉ số IOC, không tải hay thực thi mã độc).
> - Chỉ sử dụng các API chính thức và các RSS feed công khai.
> - Khả năng xác minh cao với đường truy vết bằng chứng rõ ràng.
> 
> Em xin phép kết thúc phần trình bày tại đây. Em rất sẵn lòng trả lời các câu hỏi từ thầy cô và các bạn. Em xin chân thành cảm ơn!"

---

## 📝 CHECKLIST TRƯỚC KHI DEMO

- [ ] Backend đang chạy: `python3 -m uvicorn cti_pipeline.api.main:app --host 127.0.0.1 --port 8000`
- [ ] Frontend đang chạy: `cd frontend && npm run dev -- --port 5173`
- [ ] Cơ sở dữ liệu đã có dữ liệu (chạy pipeline ít nhất một lần trước đó)
- [ ] Trình duyệt mở sẵn tại `http://127.0.0.1:5173`
- [ ] (Tùy chọn) Cấu hình biến môi trường LLM phục vụ demo chat trực tiếp:
  ```bash
  export LLM_PROVIDER=openai_compatible
  export LLM_MODEL=gemini-2.5-flash
  export LLM_BASE_URL=https://generativelanguage.googleapis.com/v1beta/openai/chat/completions
  export LLM_API_KEY=<your-key>
  ```
- [ ] Tắt các thông báo hệ thống trên màn hình.
- [ ] Chuyển trình duyệt sang chế độ toàn màn hình (F11 hoặc Ctrl+Shift+F).

---

## 🎯 CÂU HỎI THƯỜNG GẶP & GỢI Ý TRẢ LỜI

### Q: "Tại sao nhóm chọn sử dụng SQLite thay vì một hệ quản trị cơ sở dữ liệu lớn hơn?"
> "SQLite được chọn vì nó phù hợp hoàn hảo với quy mô học tập và nghiên cứu của đồ án: cơ chế hoạt động không cần máy chủ (serverless), cấu hình bằng 0, tính di động cực cao, và hiệu năng đọc dữ liệu xuất sắc đối với hàng nghìn tài liệu. Kiến trúc backend đã được thiết kế sẵn theo mẫu Repository/Adapter, giúp việc chuyển đổi sang PostgreSQL hay Neo4j trong tương lai rất dễ dàng và tốn ít công sức chỉnh sửa code."

### Q: "Hạn chế của việc trích xuất thực thể dựa trên Regex là gì?"
> "Regex có hiệu năng xử lý cực cao và độ chính xác tuyệt đối với các định dạng chuỗi có cấu trúc cố định (như mã CVE, địa chỉ IP, mã hash, mã kỹ thuật ATT&CK). Tuy nhiên, hạn chế của nó là không bắt được các thực thể phụ thuộc vào ngữ cảnh xung quanh. Để khắc phục điều này, pipeline của chúng em trích xuất trực tiếp siêu dữ liệu có cấu trúc từ API response của các nguồn tin cậy (như tên vendor/sản phẩm từ CISA KEV hay thương hiệu mục tiêu từ PhishTank) để bổ sung cho bộ parser Regex."

### Q: "Thuật toán chấm điểm hoạt động và phân cấp ưu tiên như thế nào?"
> "Điểm ưu tiên (0–100) được tính tổng hợp từ các trọng số: tần suất đề cập (tối đa 20 điểm), độ đa dạng nguồn tin (tối đa 20 điểm), trạng thái kiểm chứng chéo (từ 4 đến 20 điểm), mức CVSS của NVD (tối đa 25 điểm), độ tin cậy của nguồn tin (tối đa 15 điểm), và tính cập nhật thời gian (tối đa 10 điểm). Sau đó hệ thống phân cấp: Nguy cấp ($\ge 75$), Cao ($\ge 55$), Trung bình ($\ge 35$), và Thấp ($< 35$)."

### Q: "Knowledge Graph mang lại giá trị gì so với một danh sách thực thể phẳng?"
> "Đồ thị giúp làm nổi bật **các mối quan hệ liên đới** mà danh sách phẳng không thể biểu diễn. Nó liên kết trực tiếp một lỗ hổng CVE ảnh hưởng đến sản phẩm/nhà cung cấp nào, ánh xạ sang kỹ thuật tấn công MITRE nào, và kết nối với các bài phân tích cộng đồng nào. Nhờ đó, nhà phân tích có thể đi từ một chỉ báo đơn lẻ để nắm bắt toàn bộ diện tích bề mặt tấn công liên quan."

### Q: "Làm thế nào để hệ thống ngăn chặn hiện tượng LLM bịa đặt thông tin (hallucination)?"
> "Chúng em thiết lập nhiều lớp bảo vệ: ràng buộc System Prompt nghiêm ngặt, giới hạn dữ liệu đầu vào chỉ chứa các tài liệu khớp chính xác từ SQLite (RAG), chạy trình xác thực trích dẫn phía máy chủ để loại bỏ các trích dẫn ID giả mạo, và tự động đính kèm cảnh báo lưu ý cho nhà phân tích nếu dữ liệu đầu vào có độ tin cậy thấp."

### Q: "Công cụ này phục vụ mục đích tấn công hay phòng thủ?"
> "Hoàn toàn phục vụ mục đích **phòng thủ**. Hệ thống chỉ thu thập siêu dữ liệu nguồn mở, mô tả lỗ hổng và Chỉ số nhận diện tấn công (IOC). Nó tuyệt đối không tải về mã khai thác, không quét cổng mục tiêu, không tải payload độc hại. Mục tiêu của nó là tăng cường năng lực mô hình hóa mối đe dọa và nhận thức tình huống cho đội ngũ phòng thủ (Blue Team)."

---

## ⏱️ TIẾN ĐỘ THỜI GIAN THUYẾT TRÌNH

| Phần | Nội dung | Thời gian dự kiến |
|------|----------|-----------|
| 1 | Giới thiệu Tổng quan & Kiến trúc | 2–3 phút |
| 2 | Pipeline Thu thập Thông tin | 1 phút |
| 3 | Dashboard Mission Control | 3–4 phút |
| 4 | Phân hệ Intelligence Explorer | 3–4 phút |
| 5 | Đồ thị tri thức Knowledge Graph | 2–3 phút |
| 6 | Xuất bản Báo cáo & Luật phát hiện | 3–4 phút |
| 7 | Trò chuyện Trợ lý AI (LLM Chat) | 2–3 phút |
| 8 | Kết luận & Tổng kết Kỹ thuật | 1–2 phút |
| | **Tổng** | **17–24 phút** |

> [!TIP]
> Nếu thời gian thuyết trình bị rút ngắn, bạn có thể bỏ qua Phần 2 (giới thiệu pipeline) và Phần 7 (demo chat AI). Hãy tập trung thuyết trình sâu vào Phần 3, 4, 5, và 6 vì đây là những tính năng thể hiện rõ rệt nhất giá trị phân tích của Command Center.
