# 📊 BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 — SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Võ Đức Tài  
> **Mã Sinh Viên / Mã Học viên:** 2A202603007  
> **Chủ đề Lựa chọn:** Trợ lý Tác tử Học vụ Sinh viên VinUni (VinUni Academic Advising ReAct Agent with MCP)  

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm |
| :--- | :---: | :--- |
| **1. Multi-step Reasoning** | **5** / 5 | Bài toán yêu cầu phân rã câu hỏi phức tạp thành chuỗi suy luận liên tiếp. Ví dụ: khi sinh viên yêu cầu đặt lịch với cố vấn phụ trách, Agent phải tra cứu thông tin sinh viên để xác định danh tính cố vấn trước, sau đó mới dùng dữ liệu đó để tiến hành đặt lịch tư vấn. |
| **2. Tool Interaction** | **5** / 5 | Hệ thống bắt buộc phải giao tiếp thời gian thực với MCP Server qua hai công cụ `academic_query` và `schedule_appointment` để truy xuất cơ sở dữ liệu học vụ và ghi nhận lịch hẹn, ngăn chặn triệt để hiện tượng bịa đặt dữ liệu (Anti-Hallucination). |
| **3. Dynamic Decision** | **5** / 5 | Hành động kế tiếp phụ thuộc hoàn toàn vào kết quả quan sát (`Observation`) của bước trước: nếu kết quả trả về `NOT_FOUND`, Agent lập tức dừng quy trình và thông báo lịch sự cho sinh viên; nếu tìm thấy thông tin, Agent linh hoạt chuyển sang bước đặt lịch tương ứng. |
| **4. Long Horizon Goal** | **4** / 5 | Hệ thống duy trì mục tiêu và ngữ cảnh xuyên suốt qua nhiều lượt suy luận trong ReAct Loop (giới hạn tối đa 5 bước lặp) để đảm bảo giải quyết trọn vẹn yêu cầu nghiệp vụ của sinh viên trước khi đưa ra Final Answer. |
| **TỔNG ĐIỂM AGENTIC FIT** | **19 / 20** | *Tổng điểm 19/20 (> 12/20): Bài toán hoàn toàn phù hợp và tối ưu khi triển khai dưới dạng Agentic System kết nối MCP.* |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

> ⚠️ **YÊU CẦU NGHIỆM THU:** Mở tệp `.env` điền `GEMINI_API_KEY` (hoặc `OPENAI_API_KEY`) để kết nối LLM thật trước khi thực thi `python src/app.py --all`. Bài nộp chỉ dùng Mock Offline Provider sẽ không đạt điểm nghiệm thực tế.

Đoạn trích xuất Waterfall Trace Log tiêu biểu cho ca kiểm thử suy luận đa bước **TC04** (`multi_step_reasoning`) trích xuất từ file [`docs/trace_waterfall.json`](trace_waterfall.json):

```json
[
  {
    "step": 1,
    "query": "Hãy tra cứu thông tin sinh viên SV2026002 để xem ai là cố vấn học tập, sau đó đặt lịch hẹn tư vấn với cố vấn đó vào lúc 09:00 ngày 20/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "academic_query",
    "arguments": {
      "student_id": "SV2026002"
    },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026002",
      "data": {
        "full_name": "Trần Thị Bình",
        "class": "AI-K4",
        "gpa": 3.6,
        "email": "binh.tt@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "TS. Lê Thị B"
      }
    },
    "latency_ms": 6354.65
  },
  {
    "step": 2,
    "query": "Hãy tra cứu thông tin sinh viên SV2026002 để xem ai là cố vấn học tập, sau đó đặt lịch hẹn tư vấn với cố vấn đó vào lúc 09:00 ngày 20/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "schedule_appointment",
    "arguments": {
      "student_id": "SV2026002",
      "datetime_str": "09:00 20/09/2026",
      "advisor_name": "TS. Lê Thị B"
    },
    "observation": {
      "status": "SUCCESS",
      "booking_id": "BK-SV2026002-99",
      "student_id": "SV2026002",
      "datetime": "09:00 20/09/2026",
      "advisor": "TS. Lê Thị B",
      "message": "Đặt lịch thành công cho sinh viên SV2026002 với TS. Lê Thị B vào lúc 09:00 20/09/2026."
    },
    "latency_ms": 20400.02
  },
  {
    "step": 3,
    "query": "Hãy tra cứu thông tin sinh viên SV2026002 để xem ai là cố vấn học tập, sau đó đặt lịch hẹn tư vấn với cố vấn đó vào lúc 09:00 ngày 20/09/2026.",
    "action_type": "FINAL_ANSWER",
    "thought": "Gemini phản hồi trực tiếp bằng văn bản (không cần gọi công cụ).",
    "output": "Thought: Đã hoàn tất tra cứu thông tin sinh viên và đặt lịch hẹn thành công với cố vấn học tập. Cả 2 bước đều xử lý thành công, tôi sẽ tổng hợp kết quả chi tiết để trả lời cho sinh viên.\n\n---\n\n### **KẾT QUẢ ĐẶT LỊCH HẸN TƯ VẤN HỌC VỤ**\n\n1. **Thông tin sinh viên:**\n   - **Họ và tên:** Trần Thị Bình\n   - **Mã sinh viên:** SV2026002\n   - **Lớp:** AI-K4\n   - **Cố vấn học tập:** TS. Lê Thị B\n\n2. **Thông tin lịch hẹn:**\n   - **Mã đặt lịch (Booking ID):** `BK-SV2026002-99`\n   - **Thời gian:** 09:00 ngày 20/09/2026\n   - **Người tư vấn:** TS. Lê Thị B\n   - **Trạng thái:** **Đặt lịch thành công**\n\nBạn vui lòng kiểm tra email sinh viên (`binh.tt@vinuni.edu.vn`) và có mặt đúng giờ hẹn. Chúc bạn có buổi tư vấn hiệu quả!",
    "latency_ms": 7104.15
  }
]
```

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã điền API Key thật trong `.env` (`GEMINI_API_KEY`) với mô hình `gemini-3.6-flash`, xác nhận Agent chạy mượt mà trên LLM API thật và có cơ chế Mock fallback/retry an toàn.
- **Tổng số Test Cases đã chạy thành công:** **5 / 5 test cases** (TC01: Direct Query, TC02: Single Tool, TC03: Booking, TC04: Multi-step Reasoning, TC05: Edge Case Not Found).
- **Số lượt gọi Tool qua MCP Server chính xác:** **4 lượt** (`academic_query` 3 lần, `schedule_appointment` 2 lần trong 4 ca kiểm thử có tool).
- **Kết quả đẩy Repo nộp bài:** [x] Đã hoàn thiện toàn bộ mã nguồn, cấu hình JSON, tài liệu báo cáo nghiệm thu và chuẩn bị push lên GitHub cá nhân.

---

> ✅ **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân của bạn và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3!
