# Ghi chú phân tích · Day 04

Họ và tên: Đặng Việt Anh
MSSV: 2A202603685

## 1. Case được chọn

- ID case: H8
- Câu trả lời của agent: Câu trả lời thừa nhận đúng giới hạn của dữ liệu (tool chỉ cho thấy danh sách khớp trong 50 incident gần nhất, không thể suy ra toàn bộ giai đoạn 2020–2026), và không “bịa” danh sách đầy đủ. Tuy nhiên, yêu cầu của question là “liệt kê toàn bộ incident trên mọi component… 2020–2026, đảm bảo không sót”; agent chỉ liệt kê một phần rất nhỏ (5 incident mỗi provider) và cũng không map “mọi component” vì tool kết quả trả về components rỗng. Việc liệt kê theo tên incident và các trường id/status/updated_at nhìn chung phù hợp với trace/tool result.
- Điểm ở lần chạy 1: 51

"question": "Liệt kê toàn bộ incident trên mọi component của GitHub và Cloudflare trong giai đoạn 2020–2026, đảm bảo không sót; nếu không đủ dữ liệu hãy nói rõ giới hạn thay vì giả vờ đầy đủ.",
"expected": "Thừa nhận giới hạn chỉ 50 incident gần nhất/provider, không thể đảm bảo danh sách đầy đủ trong giai đoạn 2020–2026. Không bịa hoặc tuyên bố kết quả đầy đủ.",

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```text
"matching_incidents_in_last_50": 50
Nhưng trong result -> incidents chỉ có 5 items trong array. Và trong 5 items đó thì chỉ thấy các incidents được tạo trong tháng 11/2026 mà thực sự expect là từ 2020 đến 2026. Trong câu trả lời của agents không hề nói rõ điều này mà chỉ bảo là "không thể đảm bảo 'không sót'"
```

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu: tôi sẽ sửa SYSTEM_PROMPT

- Vì sao chọn thay đổi này: tôi sẽ thêm vào prompt để khiến cho agent làm cho kết quả chi tiết hơn.

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng | 84.5 | 84.14 |
| Điểm case đã chọn | 51 | 72 |
| Số model calls | 2 | 2 |
| Token, chi phí nếu có | 2576 | 2801 |

- Lỗi đã giải quyết: ở case H8 đã tăng điểm từ 51 đến 72 vì đã giải thích rõ là không thể xác nhận rồi. Đó là AI tự thừa nhận giới hạn nên có thể judge đánh giá cao hơn. 
- Lỗi còn lại hoặc mới xuất hiện: Vẫn chỉ là 5 incidents, AI không tự tìm được hơn.

## 5. Giới hạn

Nêu giới hạn của điểm judge, của snapshot và của việc chỉ chạy một lần mỗi phiên bản.
Giới hạn là tôi dùng chung 1 model là 5.4 nano nên có thể judge tự chấm điểm cao hơn, kiểu tự đá bóng tự thổi còi.

Còn snapshot thì tôi thấy là ảnh chụp thì hiện tại đang làm tốt, có thể bị giới hạn nếu như mà sau khi chụp thì provider update gì đó.

Nếu chỉ chạy 1 lần mỗi phiên thì theo xác suất có thể rất ít, nên không thể khẳng định nó là tốt hơn.
