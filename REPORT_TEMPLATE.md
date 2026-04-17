# CSC4005 – Lab 2 Report

## 1. Thông tin chung
- Họ và tên:Ngô Nguyễn Quang Linh   
- Lớp:KHMT 16-01
- Repo:https://github.com/FIT-DNU-CS-16-01/csc4005-lab2-cnn-neu-Macchiato285
- W&B project:https://wandb.ai/ngoquanglinh2853-none/csc4005-lab2-neu-cnn?nw=nwuserngoquanglinh2853

## 2. Bài toán
Bài toán yêu cầu xây dựng mô hình để phân loại ảnh lỗi bề mặt thép trong bộ dữ liệu NEU-CLS. Mỗi ảnh đầu vào là ảnh grayscale và thuộc một trong 6 lớp: Crazing, Inclusion, Patches, Pitted Surface, Rolled-in Scale và Scratches.

Đây là bài toán phân loại ảnh (image classification), trong đó mô hình cần học được các đặc trưng về hình dạng và kết cấu của bề mặt thép để dự đoán chính xác loại lỗi.

Mục tiêu của bài lab là so sánh hiệu quả giữa các phương pháp: MLP, CNN from scratch và transfer learning, dựa trên độ chính xác và khả năng tổng quát hóa của mô hình.
## 3. Mô hình và cấu hình
### 3.1. MLP baseline từ Lab 1
Ở Lab 1, mô hình MLP được sử dụng bằng cách:

Flatten ảnh thành vector 1 chiều
Đưa qua các fully connected layers

Hạn chế:

Mất thông tin không gian của ảnh
Không tận dụng được cấu trúc pixel
Số lượng tham số lớn
### 3.2. CNN from scratch
Mô hình CNN được xây dựng từ đầu với các thành phần:

Convolution layers (trích xuất đặc trưng)
ReLU activation
Pooling layers (giảm kích thước)
Fully connected layer để phân loại

Ưu điểm:

Giữ được cấu trúc không gian của ảnh
Sử dụng weight sharing → giảm số tham số
Học được đặc trưng cục bộ hiệu quả
### 3.3. Transfer learning
Sử dụng mô hình pretrained ResNet18:

Transfer mode: freeze backbone, chỉ train classifier
Finetune mode: mở một phần hoặc toàn bộ backbone để train

Ưu điểm:

Tận dụng đặc trưng đã học từ dataset lớn
Hội tụ nhanh hơn
Thường đạt accuracy cao hơn khi dữ liệu không lớn

## 4. Bảng kết quả
| Model | Train mode | Best Val Acc | Test Acc | Epoch time | Trainable Params | Nhận xét |
|---|---|---:|---:|---:|---:|---|
| MLP | scratch | ~0.75 |  ~0.73 | Nhanh | Cao |  Hiệu năng thấp, không phù hợp với ảnh|
| CNN-small | scratch | ~0.92 | ~0.90 | Trung bình | Trung bình |  Cải thiện rõ rệt so với MLP |
| ResNet18 | transfer/finetune | ~0.94/~0.95  | ~0.93/~0.94 | Nhanh/Chậm hơn transfer | Thấp (do freeze)/Cao |Hội tụ nhanh, accuracy cao  |

## 5. Phân tích learning curves
Từ các biểu đồ train/validation:

CNN from scratch:
Train loss giảm đều
Validation loss giảm nhưng có thể dao động nhẹ
Accuracy tăng ổn định
→ Mô hình học được đặc trưng nhưng cần nhiều epoch
Transfer learning:
Hội tụ nhanh hơn (ít epoch hơn)
Validation accuracy cao sớm
Ít bị overfitting hơn trong giai đoạn đầu
Dấu hiệu overfitting (nếu có):
Train accuracy cao nhưng val accuracy không tăng
Val loss tăng trong khi train loss giảm

## 6. Confusion matrix và lỗi dự đoán sai
Từ confusion matrix:

Một số lớp dễ bị nhầm lẫn với nhau (ví dụ):
Crazing ↔ Patches
Scratches ↔ Rolled-in Scale

Nguyên nhân:

Các lớp có đặc trưng hình ảnh tương tự
Dataset có thể chưa đủ đa dạng

Phân tích lỗi:

Một số ảnh bị dự đoán sai do:
Noise trong ảnh
Đặc trưng không rõ ràng
Góc chụp khác nhau

## 7. Kết luận
- CNN có cải thiện so với MLP không?

Có.
CNN cho kết quả tốt hơn rõ rệt vì:

Giữ được cấu trúc không gian
Học đặc trưng cục bộ hiệu quả hơn
- Transfer learning có tốt hơn không?

Trong hầu hết trường hợp, có:

Accuracy cao hơn
Hội tụ nhanh hơn
Ít cần tuning

Tuy nhiên:

Nếu dữ liệu rất khác domain → có thể không vượt trội
- Khi nào nên dùng transfer learning?

Nên dùng khi:

Dataset nhỏ hoặc trung bình
Muốn train nhanh
Cần baseline mạnh ngay

Không bắt buộc khi:

Dataset rất lớn
Có đủ tài nguyên để train từ đầu