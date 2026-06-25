---
name: tech-solution-research
description: Use this skill before committing to a technical implementation approach — choosing a library/SDK/framework, designing an architecture, integrating an external service, or whenever an implementation starts feeling unexpectedly complex or "fighting the tool." Also trigger when the user says things like "is there a better way", "this feels too complicated", "what's the right approach for X", or describes being stuck mid-implementation. The skill forces a check of the problem space and the architecture/paradigm space BEFORE searching for tools, to avoid tunnel-visioning on the first SDK/library that came to mind and missing a fundamentally simpler approach (e.g. client-side vs server-side, library vs platform feature, custom build vs known pattern).
---

# Tech Solution Research

Mục tiêu của skill này: tránh tình trạng đào sâu 1-2 giờ vào một hướng implementation phức tạp, trong khi có một hướng khác đơn giản hơn nhiều chỉ cách một câu hỏi reframe đúng.

Bài học gốc: khi research SDK để làm tính năng agent thao tác web, việc search theo tên SDK đã biết (Claude Agent SDK, Vercel AI SDK — đều server-side) dẫn vào một nhánh phức tạp. Câu hỏi "sao không chạy trên browser?" đã mở ra một paradigm khác hẳn (client-side in-page agent, ví dụ Alibaba's page-agent) — đơn giản hơn nhiều cho đúng use case. Khác biệt không phải là biết nhiều SDK hơn, mà là **hỏi đúng câu hỏi ở đúng layer trước khi search tên công cụ.**

## Quy trình bắt buộc trước khi chốt giải pháp

### Bước 0 — Phát hiện tín hiệu cần dừng lại

Tự kiểm tra các dấu hiệu sau. Nếu có ≥1 dấu hiệu, KHÔNG tiếp tục đào sâu vào hướng hiện tại — quay lại Bước 1 trước:

- Đang phải viết nhiều code "chống đỡ"/workaround hơn là code giải quyết bài toán chính
- Setup/luồng phức tạp hơn cảm giác trực quan của bài toán
- Đã hơn ~20-30 phút mà giải pháp vẫn "gượng gạo"
- Search query gần nhất chỉ là biến thể của search trước (đang đào sâu thêm vào CÙNG một hướng, không phải kiểm tra hướng khác)

### Bước 1 — Định nghĩa lại vấn đề ở mức gốc, tách khỏi giải pháp đang nghĩ tới

Viết ra (ngắn gọn) hai thứ riêng biệt:
1. **Bài toán thật sự cần giải** — diễn đạt không nhắc tên SDK/tool nào cả. Ví dụ: "cho user prompt agent để thao tác hộ trên 1 trang web" — KHÔNG phải "làm sao dùng Claude Agent SDK cho việc X".
2. **Giả định ngầm đang mang theo** — những constraint mình tự đặt ra mà chưa kiểm tra lại. Ví dụ: "agent phải chạy server-side", "phải dùng SDK chính thức", "phải tự build từ đầu".

Với mỗi giả định ở (2), tự hỏi: *giả định này có thật không, hay chỉ là vì đó là hướng đầu tiên mình nghĩ tới?*

### Bước 2 — Quét không gian kiến trúc (paradigm) trước khi quét không gian công cụ (tool)

Trước khi search "X library nào tốt nhất", search để trả lời: *bài toán này có những cách tiếp cận/kiến trúc khác nhau nào?* Một bài toán thường có 2-4 paradigm khác nhau, mỗi paradigm có nhiều tool riêng. Nếu chỉ so sánh tool trong cùng 1 paradigm, dễ bỏ lỡ paradigm đơn giản hơn.

Câu hỏi để liệt kê paradigm khả dĩ:
- Việc này có thể chạy ở đâu khác không (client vs server, build-time vs runtime, trong process hiện tại vs service riêng)?
- Đã có ai định nghĩa "tên gọi chuẩn" cho loại bài toán này chưa (design pattern, kiến trúc đã có tên)?
- Có cách nào KHÔNG cần build gì cả (feature có sẵn của platform/framework đang dùng) không?

Search term ở bước này nên là **mô tả kiến trúc**, không phải tên SDK đã biết:
- ❌ "Claude Agent SDK vs Vercel AI SDK"
- ✅ "in-browser AI agent web interaction", "client-side vs server-side AI agent architecture"

### Bước 3 — Tìm "đã có giải pháp chuẩn chưa" trước khi tự build

Với mỗi paradigm tìm được ở Bước 2, kiểm tra nhanh: có pattern/library/service phổ biến nào cộng đồng đã dùng cho đúng việc này chưa? Ưu tiên theo thứ tự:
1. Tính năng có sẵn của platform/framework hiện tại
2. Library/SDK được nhiều dự án lớn dùng, có docs chính thức
3. Pattern được viết thành blog/RFC nhiều lần (nghĩa là bài toán phổ biến, đã có "câu trả lời chuẩn")
4. Tự build custom — chỉ chọn nếu (1)-(3) đều không phù hợp

### Bước 4 — Search bằng tiếng Anh, đúng thuật ngữ kỹ thuật

Ưu tiên search tiếng Anh trước (docs chính thức, GitHub issues, RFC đa số ở tiếng Anh, chất lượng cao hơn). Nếu chưa biết tên thuật ngữ chuẩn của bài toán, search mô tả triệu chứng trước để tìm ra tên gọi, rồi search lại bằng tên gọi đó.

### Bước 5 — So sánh ít nhất 2 hướng trước khi chốt

Không dừng lại ở giải pháp đầu tiên "chạy được". Trước khi implement, viết ra bảng so sánh ngắn (trade-off) giữa ít nhất 2 hướng khác paradigm, gồm:
- Độ phức tạp setup
- Có cần backend/infra riêng không
- Maintenance cost (ai phải maintain phần gì)
- Có khớp đúng với UX mong muốn không (ví dụ: cần con người xác nhận hành động hay chạy hoàn toàn tự động?)

## Output khi áp dụng skill này

Khi user nhờ research một giải pháp kỹ thuật, đừng trả lời ngay bằng 1 giải pháp. Trả lời theo cấu trúc:

1. Bài toán gốc (tách khỏi solution đang nghĩ tới)
2. Các paradigm/hướng kiến trúc khả dĩ (2-4 hướng)
3. Với mỗi hướng: tool/pattern tiêu biểu + trade-off ngắn
4. Đề xuất hướng phù hợp nhất với constraint cụ thể của user, kèm lý do

## Red flags cần tự cảnh báo ngay trong lúc research

- Đang search lần thứ 3+ chỉ xoay quanh 1 tên SDK/tool duy nhất → dừng, quay Bước 2
- Tool đang xem xét yêu cầu refactor lớn hệ thống hiện tại để dùng được → có khả năng sai paradigm, không phải sai tool
- Không tìm thấy ai khác làm việc tương tự bằng đúng hướng này → có thể đây không phải pattern chuẩn cho bài toán, cần quay lại Bước 3