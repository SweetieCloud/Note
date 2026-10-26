Cách verify thành công toàn bộ Gate G0:

---

### Bước 1: Cài đặt công cụ nền tảng trên WSL2 (Ubuntu)

Mở cmd và gõ
```bash
wsl
```
Sau khi WSL2 tải và cài đặt xong, restart máy, mở lại cmd và gõ
```bash
wsl --install -d Ubuntu
```
Chờ tải xong, Terminal sẽ yêu cầu nhập Username và Password cho Ubuntu. Sau đó ở Terminal WSL (Ubuntu) và cài đặt bộ biên dịch C++ cùng môi trường Python:

```bash
sudo apt update && sudo apt install -y build-essential python3 python3-pip python3-venv

```

---

### Bước 2: Biên dịch & Kiểm tra Golden C++ Model

Di chuyển vào thư mục `dv/golden_c` để build thư viện C++ và chạy selftest bit-exact:

```bash
cd dv/golden_c

# 1. Biên dịch ra Shared Library (.so)
g++ -std=c++17 -Wall -Wextra -O3 -shared -fPIC -o libgemm_ref.so gemm_ref.cpp

# 2. Kiểm tra Selftest
python3 -c "import ctypes as c; L=c.CDLL('./libgemm_ref.so'); print('Selftest result:', L.gemm_ref_selftest())"

```

> **Kỳ vọng:** Console in ra **`Selftest result: 0`**.
> 
> 

---

### Bước 3: Thiết lập Virtual Environment cho Python AI

Di chuyển sang thư mục `sw/ai`, tạo và kích hoạt môi trường ảo `venv`:

```bash
cd ../../sw/ai
python3 -m venv venv
source venv/bin/activate

# Cài đặt PyTorch (Bản CPU gọn nhẹ) và Ultralytics
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
pip install ultralytics

```

---

### Bước 4: Tải Trọng số `best.pt` & Kiểm tra Model AI

1. **Tải file trọng số:** Truy cập vào mục [Releases](https://github.com/SweetieCloud/Reconfig-GEMM-UltraScale/releases/tag/gate-g0-yolov8n) trên GitHub Repo, tải file `best.pt` về.


2. **Đặt đúng đường dẫn:** Đặt file `best.pt` vào cấu trúc thư mục sau trong project:
`sw/ai/runs/train/yolov8n_voc_fp32/weights/best.pt` *(nếu chưa có thư mục thì dùng lệnh `mkdir -p runs/train/yolov8n_voc_fp32/weights`)*.


3. **Kiểm tra load model:**
```bash
python3 -c 'from ultralytics import YOLO; model = YOLO("runs/train/yolov8n_voc_fp32/weights/best.pt"); print("Model loaded successfully")'

```
> **Kỳ vọng:** Console in ra **`Model loaded successfully`**.

---
 
