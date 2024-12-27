---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
* ![arch](/workshop-aws-card-clash-7/images/arc.png) 

## Ý tưởng kiến trúc: 
* **User sẽ cập nhật code Lambda function thông qua quy trình sau:**
    * User push code lên GitHub repository, và quy trình CI/CD được tạo ra bởi CodePipeline sẽ tự động triển khai.
    * CodePipeline sẽ có 3 giai đoạn: Source, Build, Deploy.
    * Giai đoạn Source sẽ theo dõi thay đổi từ GitHub repository và cập nhật mới nhất.
    * Tiếp theo, giai đoạn Build sẽ lấy mã nguồn và thực hiện quá trình build và test code.
    * Cuối cùng, giai đoạn Deploy sẽ sử dụng một CloudFormation template để triển khai các tài nguyên đã thay đổi.
* **Note**: Do dịch vụ AWS CodeCommit sắp khai tử nên ta thay thế bằng Github.
## Giới thiệu dịch vụ:

**Amazon CodePipeline**: Dịch vụ giúp tự động hóa quá trình triển khai và tích hợp liên tục (CI/CD) với khả năng liên kết các nguồn mã, quy trình build và các công cụ triển khai khác nhau, giúp tăng tốc độ phát triển và triển khai ứng dụng.

**Amazon CodeBuild**: Dịch vụ xây dựng và kiểm thử mã nguồn tự động, cung cấp các môi trường build có thể mở rộng để đáp ứng nhu cầu, cho phép dễ dàng cấu hình tài nguyên và xử lý các quy trình build theo yêu cầu.

**Amazon S3**: Dịch vụ lưu trữ đối tượng có khả năng mở rộng và bền bỉ, được sử dụng để lưu trữ và truy xuất dữ liệu một cách an toàn và linh hoạt, rất phù hợp cho việc lưu trữ các tệp nguồn, artifact hoặc bản ghi trong quá trình CI/CD.

**AWS Lambda**: Dịch vụ điện toán serverless cho phép chạy code mà không cần quản lý máy chủ, giúp đơn giản hóa việc triển khai các chức năng nhỏ gọn mà không phải quan tâm đến cơ sở hạ tầng.

**Amazon API Gateway**: Dịch vụ cho phép xây dựng, triển khai và quản lý API một cách an toàn và có khả năng mở rộng, kết nối với Lambda để xây dựng các RESTful API hoặc WebSocket API.

**AWS CloudFormation**: Dịch vụ cung cấp cách quản lý và triển khai tài nguyên cơ sở hạ tầng dưới dạng code (IaC), giúp dễ dàng tạo, cập nhật và xóa các tài nguyên AWS bằng các template có cấu trúc.

**Amazon DynamoDB**: Dịch vụ cơ sở dữ liệu NoSQL có độ trễ thấp, được quản lý hoàn toàn, phù hợp để lưu trữ dữ liệu có cấu trúc, có thể mở rộng dễ dàng và cung cấp tính sẵn sàng cao.