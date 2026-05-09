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

Kiểm tra phiên bản:
```
docker --version
docker compose version
```
<img width="1068" height="153" alt="image" src="https://github.com/user-attachments/assets/c8fd0035-5066-46a7-892b-ad6454a64bf1" />

### 3.2. Tạo project
Cấu trúc thư mục:
```
camdo_project/                  
│
├── docker-compose.yml              ← định nghĩa 3 service
├── .env                            ← chứa password, secret key
├── .gitignore                      ← bỏ qua .env, __pycache__, ...
│
└── django/                         ← thư mục django
   ├── Dockerfile                  ← build image Python + Django
   ├── requirements.txt            ← danh sách thư viện pip
   │
   └── myshop/                   
       ├── manage.py
       ├── config/               
       │   ├── settings.py         
       │   ├── urls.py
       │   └── wsgi.py│
       │
       └── core/                   ← app chính
           ├── models.py           ← định nghĩa bảng CSDL
           ├── admin.py            ← trang admin
           ├── views.py            ← view home_page con nợ đến hạn nhưng chưa trả tiền
           ├── urls.py
           └── templates/
               └── core/
                   └── home.html   ← template Jinja2
```

- Tạo thư mục `mkdir camdo_project`
- Chuyển vào thư mục `cd camdo_project`
- Tạo thư mục django `mkdir django` trong `camdo_project`

<img width="1322" height="182" alt="image" src="https://github.com/user-attachments/assets/7e007390-22ab-49e9-9372-b5ef4c87d0ba" />

### 3.3. Tạo file `Dockerfile`
- Vào thư mục django `cd django`
- Tạo file `nano Dockerfile`
- Thêm nội dung cho file
- Lưu lại `Ctrl + O` -> `Enter` -> `Ctrl + X `

<img width="2315" height="1124" alt="image" src="https://github.com/user-attachments/assets/ead2a1e5-38d2-47b1-a90d-fdbb35fa39cb" />

Nội dung file `Dockerfile`:
```
# Dùng Python 3.11 bản slim (nhẹ, không có các package thừa)
FROM python:3.11-slim

# Đặt thư mục làm việc bên trong container
WORKDIR /app

# Cài các gói hệ thống cần thiết:
#   - gcc, pkg-config: để biên dịch thư viện C
#   - default-libmysqlclient-dev: header để cài mysqlclient (kết nối MariaDB)
#   - curl: tiện ích mạng (debug)
#   - && rm -rf /var/lib/apt/list/* : xóa cache apt để giảm kích thước image
RUN apt-get update && apt-get install -y \
    gcc \
    pkg-config \
    default-libmysqlclient-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*    

# Copy file requirements.txt vào container trước
# (tách riêng để Docker cache layer này, không rebuild khi chỉ sửa code)
COPY requirements.txt .

# Cài toàn bộ thư viện Python từ requirements.txt
# --no-cache-dir: không lưu cache pip giúp giảm kích thước image
RUN pip install --no-cache-dir -r requirements.txt

# Copy toàn bộ source code vào container
COPY myshop/ .

# Mở port 8000 để bên ngoài container có thể kết nối
EXPOSE 8000

# Lệnh mặc định khi container khởi động
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```
### 3.4. Tạo file `requirements.txt`
- Tạo file `nano requirements.txt`
- Thêm nội dung cho file và lưu lại

<img width="2289" height="1129" alt="image" src="https://github.com/user-attachments/assets/b33c9edb-de4c-4a23-8bf8-546146e75205" />

### 3.5. Tạo file `docker-compose.yml`
- Quay lại thư mục gốc `cd..`
- Tạo file `nano docker-compose.yml`
- Thêm nội dung cho file và lưu lại

```
version: '3.8'

services:

  # 1. MariaDB: lưu toàn bộ CSDL 
  mariadb:
    image: mariadb:10.11       
    container_name: camdo_mariadb
    restart: always
    env_file:
      - .env
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - mariadb_data:/var/lib/mysql   # lưu data ra ngoài, không mất khi restart
    ports:
      - "3307:3306"
    networks:
      - camdo_net
    healthcheck:                  
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 10s
      timeout: 5s
      retries: 5

  # 2. phpMyAdmin: giao diện web xem CSDL 
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: camdo_phpmyadmin
    restart: always
    env_file:
      - .env
    environment:
      PMA_HOST: mariadb        
      PMA_PORT: 3306
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    ports:
      - "8088:80"             
    depends_on:
      mariadb:
        condition: service_healthy  # chỉ khởi động sau khi mariadb healthy
    networks:
      - camdo_net

  # 3. Django: build từ Dockerfile
  django:
    build:
      context: ./django             # thư mục chứa Dockerfile
      dockerfile: Dockerfile
    container_name: camdo_django
    restart: always
    env_file:
      - .env                        # nạp biến từ file .env
    environment:
      DB_HOST: mariadb
      DB_PORT: 3306
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - ./django/myshop:/app        # mount thư mục
    ports:
      - "8000:8000"             
    depends_on:
      mariadb:
        condition: service_healthy
    networks:
      - camdo_net
    command: >
      sh -c "python manage.py migrate &&
             python manage.py collectstatic --noinput &&
             python manage.py runserver 0.0.0.0:8000"

# ── Volumes dùng chung 
volumes:
  mariadb_data:

# ── Network nội bộ giữa các container 
networks:
  camdo_net:
    driver: bridge
```

### 3.6. Chạy Docker Compose

- Chạy lệnh `docker compose up -d --build` để build image và khởi động 3 container
- Kiểm tra bằng lệnh `docker ps`

<img width="2339" height="596" alt="image" src="https://github.com/user-attachments/assets/925350b4-3be4-4f6b-bed0-d6b10de2c659" />


- kết quả thấy 3 container đã hoạt động

### 3.7. Khởi tạo project Django
- Chạy lệnh `docker compose exec django django-admin startproject config .`
- Tạo app core `docker compose exec django python manage.py startapp core`

<img width="2172" height="78" alt="image" src="https://github.com/user-attachments/assets/41c8f456-4168-40b6-bb2d-7a427156643c" />

- Kết quả xuất hiện các thư mục và tệp: `config`, `core`, `manage.py`:

<img width="1575" height="109" alt="image" src="https://github.com/user-attachments/assets/7cc9f924-c019-4658-b2f4-81ed307f87c6" />

### 3.8. Chỉnh sửa file `settings.py`
- Thực hiện `nano django/config/settings.py`
- Tìm kiếm và chỉnh sửa một số thông tin trong file:

```
import os

# Lấy giá trị từ biến môi trường .env, nếu không thấy thì dùng một chuỗi tạm
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', 'django-insecure-default-key-123')

# Debug: True khi dev, False khi production
DEBUG = os.environ.get('DJANGO_DEBUG', 'True') == 'True'

# Cho phép mọi host truy cập (phù hợp khi dùng Cloudflare Tunnel)
ALLOWED_HOSTS = ['*']

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'core',                  # app chính
    'widget_tweaks',        
]

ROOT_URLCONF = 'config.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # Thư mục templates toàn cục
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,            # Tìm templates trong thư mục mỗi app
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'config.wsgi.application'

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',    # dùng MySQL
        'NAME': os.environ.get('MYSQL_DATABASE', 'camdo_db'),
        'USER': os.environ.get('MYSQL_USER', 'admin'),
        'PASSWORD': os.environ.get('MYSQL_PASSWORD', '123456'),
        'HOST': os.environ.get('DB_HOST', 'mariadb'),  # tên service
        'PORT': os.environ.get('DB_PORT', '3306'),
        'OPTIONS': {
            'charset': 'utf8mb4',                # hỗ trợ tiếng Việt
        },
    }
}

# ── Xác thực mật khẩu 
AUTH_PASSWORD_VALIDATORS = [
    {'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator'},
    {'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator'},
    {'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator'},
    {'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator'},
]

LANGUAGE_CODE = 'vi'           
TIME_ZONE = 'Asia/Ho_Chi_Minh'
USE_I18N = True
USE_TZ = True

# ── Static files 
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'   

```
### 3.9. Định nghĩa models và admin
- Sửa file `nano django/myshop/core/models.py`

<img width="2291" height="1126" alt="image" src="https://github.com/user-attachments/assets/4f38c9bf-bbe3-4be2-8583-a7e46a7e1471" />


- Chạy lệnh `docker compose exec django python manage.py makemigrations core` để tạo file makemigrations

<img width="2057" height="326" alt="image" src="https://github.com/user-attachments/assets/e78207ae-7952-48c2-984a-8f5eeba5318e" />

- Chạy lệnh `docker compose exec django python manage.py migrate` để thực thi thay đổi.

<img width="1844" height="193" alt="image" src="https://github.com/user-attachments/assets/09c911cd-1740-40e2-8396-f584885834ee" />

- Tạo tài khoản admin, chạy lệnh : `docker compose exec django python manage.py createsuperuser`

<img width="2219" height="177" alt="image" src="https://github.com/user-attachments/assets/5a4f579a-5085-4d41-b87c-7650bda520f3" />

- Đăng ký các bảng vào trang admin: `nano django/myshop/core/admin.py`

<img width="2341" height="1211" alt="image" src="https://github.com/user-attachments/assets/0f152490-37aa-4c78-a130-112e5613857c" />

#### Kiểm tra:
- Mở PhpMyAmin `192.168.164.129:8088` và đăng nhập
- Mở database `camdo_db` thấy các bảng đã được tạo là OK. 

<img width="3071" height="1840" alt="image" src="https://github.com/user-attachments/assets/9d68327c-9ecd-4788-a305-fe5f62dd936f" />

- Truy cập `192.168.164.129:8000/admin` để mở trang quản trị django

<img width="3066" height="1740" alt="image" src="https://github.com/user-attachments/assets/10e0560a-ab1a-4dda-b819-f02c63207439" />




---

## 4. KẾT QUẢ 📊

