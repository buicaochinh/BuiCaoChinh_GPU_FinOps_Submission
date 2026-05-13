# BÁO CÁO PHÂN TÍCH: GPU FINOPS & COST OPTIMIZATION

## 1. Giới thiệu
- **Mục tiêu của bài lab:** Thực hành các phương pháp quản lý, theo dõi và tối ưu hóa chi phí khi sử dụng tài nguyên GPU trên môi trường Cloud (FinOps). Nắm vững cách thức cân bằng giữa hiệu suất tính toán (Performance) và chi phí tài chính (Cost), từ đó thiết kế được các chiến lược huấn luyện mô hình AI/ML với mức ngân sách tối ưu nhất.
- **Tổng quan về GPU FinOps:** Trong kỷ nguyên AI, việc vận hành các mô hình đòi hỏi chi phí GPU khổng lồ. FinOps (Cloud Financial Management) cung cấp một khung quản trị (framework) giúp các kỹ sư và đội ngũ tài chính hợp tác để minh bạch hóa chi phí, phát hiện lãng phí (waste), và áp dụng các kỹ thuật cắt giảm ngân sách như sử dụng Spot Instances, tối ưu cấp phát động (Auto-scaling) hay giảm độ chính xác (Mixed Precision).

## 2. Phân tích từng phần

### Part 1-7: Phân tích kết quả từ Mock Cluster
- **Cluster monitoring insights:** Hệ thống mock cluster theo dõi hiệu quả thời gian thực các chỉ số quan trọng (Utilization, Memory, Power) của các GPU (T4, A100). Việc hiển thị rõ trạng thái Idle/Busy giúp người quản trị lập tức phát hiện các tài nguyên đang không được tận dụng.
- **Cost tracking observations:** Hệ thống ghi nhận chi phí rành mạch theo từng Workload. Việc theo dõi chi phí theo thời gian (Time-series) giúp đánh giá tốc độ "đốt tiền" (burn rate) và cảnh báo sớm nếu nguy cơ vượt Budget.
- **Spot instance savings analysis:** Việc đấu thầu Spot Instances cho thấy khả năng tiết kiệm chi phí vượt trội (tiết kiệm từ 50-70% so với On-demand). Các mô phỏng Preemption (thu hồi đột ngột) cho thấy các tác vụ dạng batch job hoàn toàn có thể sử dụng Spot nếu có cơ chế checkpointing tốt.
- **Autoscaling behavior:** Cấu hình KEDA-like Autoscaler hoạt động nhạy bén với tải. Khi Utilization vượt ngưỡng 70%, hệ thống lập tức bổ sung GPU node để đáp ứng; khi rảnh rỗi (dưới 25%), hệ thống hạ scale để chống lãng phí.
- **Waste analysis và recommendations:** Báo cáo Waste Report chỉ điểm thành công số tiền lãng phí do GPU Idle. Các recommendations đưa ra (như dùng Right-sizing hoặc thiết lập Shutdown rules) rất sát với thực tiễn tối ưu hạ tầng.

### Part 8: Phân tích Real GPU Training
- **FP32 vs Mixed Precision (AMP) comparison:** Thử nghiệm huấn luyện ResNet-18 trên CIFAR-10 với Real GPU cho thấy: Mixed Precision (AMP) giảm bớt mức tiêu thụ VRAM và rút ngắn thời gian tính toán cho mỗi epoch một cách đáng kể, trong khi chất lượng mô hình (Accuracy) vẫn được duy trì ngang bằng với FP32.
- **Cost savings achieved:** Bằng việc rút ngắn thời gian huấn luyện tổng thể nhờ AMP và tăng Batch Size, tổng chi phí tính theo giờ thuê GPU thực tế đã giảm xuống rõ rệt.
- **GPU utilization patterns:** Đồ thị Telemetry cho thấy quá trình huấn luyện sử dụng AMP giúp GPU duy trì Utilization ở mức cao, ít bị thắt cổ chai ở khâu truyền tải bộ nhớ so với quá trình chạy thuần FP32.

### Part 8.5: Advanced analysis
- **Multi-GPU scaling efficiency:** Phân tích cho thấy hiệu suất mở rộng (Scaling efficiency) là phi tuyến tính (Sub-linear). Ví dụ: 8 GPU không mang lại tốc độ gấp đúng 8 lần do hao phí giao tiếp (communication overhead). Do đó, tìm ra "điểm ngọt" (Sweet spot) về số lượng GPU là chìa khóa để tối ưu Cost/Performance.
- **Project cost forecasting:** Việc sử dụng Contingency buffer và Confidence Intervals (±10% hoặc 20%) giúp dự báo chi phí sát với thực tế, đảm bảo dự án ML nhiều giai đoạn (Data Prep, Training, Tuning) không bị vỡ trận về ngân sách.
- **Optimization strategy prioritization:** Bảng phân tích cơ hội tối ưu đã xếp hạng các chiến lược theo tiêu chí "Tiết kiệm cao / Công sức thấp". Theo đó, việc dùng AMP và Optimize Batch Size được xếp độ ưu tiên cao nhất vì tiết kiệm chi phí lập tức (Quick-wins) với rủi ro cực thấp.

## 3. Kết luận và học hỏi
- **Những kỹ năng FinOps đã học:** Có khả năng đọc hiểu và xây dựng các báo cáo phân bổ chi phí, theo dõi sự lãng phí (Waste Tracking), tính toán Scaling Efficiency, và thiết lập biểu đồ dự báo chi phí tích hợp (Integrated Dashboard) cho dự án ML.
- **Các chiến lược cost optimization hiệu quả:** Tận dụng triệt để Mixed Precision (AMP) cho các mô hình lớn, sử dụng Spot Instances cho quy trình có độ dung lỗi cao, và thiết lập Autoscaling chặt chẽ theo nhịp điệu của Data Pipeline.
- **Ứng dụng thực tế trong projects:** Bộ khung FinOps Analysis và bộ code dự báo vừa thực hành hoàn toàn có thể được ứng dụng để lập ngân sách và bảo vệ dự án (Pitching) khi có nhu cầu triển khai huấn luyện các LLMs hay Computer Vision tại doanh nghiệp trong tương lai.
