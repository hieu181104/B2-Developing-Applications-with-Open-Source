# SỬ DỤNG DJANGO ĐỂ TẠO WEB QUẢN LÝ TIỆM CẦM ĐỒ
- Môn học: Phát triển ứng dụng với mã nguồn mở - TEE0421
- Họ và tên: Nguyễn Trung Hiếu
- Công nghệ sử dụng: Django, MariaDB, PhpMyAdmin, Cloudflare Tunnel, Docker
---
## 📌 MỤC LỤC
1. [GIỚI THIỆU](#giới-thiệu)
2. [TỔ CHỨC CSDL CHO HỆ THỐNG QUẢN LÝ TIỆM CẦM ĐỒ](#tổ-chức-csdl-cho-hệ-thống-quản-lý-tiệm-cầm-đồ)
3. [XÂY DỰNG VÀ CÀI ĐẶT](#xây-dựng-và-cài-đặt)
4. [KẾT QUẢ](#kết-quả)
---
## 1. GIỚI THIỆU 🧩
### Tính năng chính của hệ thống quản lý tiệm cầm đồ:
- Quản lý khách hàng, hợp đồng cầm đồ, tài sản, lịch sử giao dịch
- Trang admin cho phép thêm / sửa / xóa dữ liệu trong các bảng
- Trang liệt kê các con nợ đến hạn nhưng chưa trả tiền

### Công nghệ: sử dụng Docker trên Ubuntu với 3 service:

| Tên serivce | Chức năng | Cổng |
| :---: | --- | :---: |
| MariaDB | Lưu trữ cơ sở dữ liệu | 0000 |
| PhpMyAdmin | Kiểm tra cơ sở dữ liệu | 0000 |
| Django | Build 1 Docker container (dùng Dockerfile) | 0000 |

---

## 2. TỔ CHỨC CSDL CHO HỆ THỐNG QUẢN LÝ TIỆM CẦM ĐỒ 🗄️
### Sơ đồ thực thể liên kết:
<img width="2562" height="2040" alt="image5" src="https://github.com/user-attachments/assets/3c752972-6deb-414b-b868-a41c34929406" />

### Mô tả chi tiết các bảng:
#### 📌 Bảng khachhang (lưu thông tin khách hàng đến cầm đồ) 
| Tên trường | Kiểu dữ liệu | Mô tả |
|:---| :--- | :--- |
| id | bigint | Mã khách hàng (PK) |
| ho_ten | varchar(100) | Họ tên khách hàng |
| cccd | varchar(12) | Số CCCD |
| sdt | varchar (10) | Số điện thoại |
| dia_chi| longtext | Địa chỉ khách hàng |
| created_at | datetime | Ngày tạo data |

#### 📌 Bảng taisan (lưu thông tin tài sản cầm đồ)
| Tên trường | Kiểu dữ liệu | Mô tả |
|:---| :--- | :--- |
| id | bigint | Mã tài sản (PK) |
| ten_tai_san | varchar(200) | Tên tài sản |
| mo_ta | longtext | Mô tả chi tiết tài sản |
| tinh_trang | varchar(50) | Tình trạng tài sản |

#### 📌 Bảng hopdong (lưu thông tin hợp đồng cầm đồ)
| Tên trường | Kiểu dữ liệu | Mô tả |
|:---| :--- | :--- |
| id | bigint | Mã hợp đồng (PK) |
| ngay_cam_do | date | Ngày cầm đồ |
| ngay_dao_han | date | Ngày đáo hạn |
| so_tien | decimal(15,0) | Số tiền cho vay |
| lai_suat | decimal(5,2) | Lãi suất (%) |
| trang_thai | varchar(50) | Trạng thái hợp đồng |
| ghi_chu | longtext | Ghi chú |
| created_at | datetime | Thời gian tạo hợp đồng |
| id_khachhang | bigint | Mã khách hàng (FK) |
| id_taisan | bigint | Mã tài sản (FK) |

Các trạng thái hợp đồng:
```
dang_cam -> đang cầm
da_chuoc -> đã chuộc
qua_han -> quá hạn
```

#### 📌 Bảng thanhtoan (lưu thông tin thanh toán hợp đồng)
| Tên trường | Kiểu dữ liệu | Mô tả |
|:---| :--- | :--- |
| id | bigint | Mã thanh toán (PK) |
| ngay_thanh_toan | date | Ngày thanh toán |
| so_tien | decimal(15,0) | Số tiền thanh toán |
| ghi_chu | longtext | Ghi chú |
| id_hopdong | bigint | Mã hợp đồng (FK) |

---
## 3. XÂY DỰNG VÀ CÀI ĐẶT ⚙️
### 3.1. Môi trường cài đặt
- Ubuntu Server đã cài Docker & Docker Compose
- SSH vào server

### 3.2. 

---

## 4. KẾT QUẢ 📊

