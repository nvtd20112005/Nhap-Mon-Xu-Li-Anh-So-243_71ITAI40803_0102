## Công nghệ sử dụng
Framework: Python với các thư viện hỗ trợ xử lý ảnh và tính toán ma trận.
- PIL (Pillow): thư viện hỗ trợ đọc, hiển thị và xử lý ảnh cơ bản. Trong bài này, PIL được dùng để mở ảnh đầu vào, chuyển ảnh sang ảnh xám, và lưu ảnh đầu ra sau khi xử lý.
- NumPy: được sử dụng để biểu diễn ảnh dưới dạng mảng số và thực hiện các phép toán ma trận. Đây là thư viện nền tảng giúp thao tác nhanh với dữ liệu ảnh dưới dạng số.
- Matplotlib: dùng để hiển thị ảnh trước và sau xử lý. Giúp người dùng quan sát trực quan kết quả sau từng bước thực hiện thuật toán.
  
Các công cụ này nhẹ, dễ cài đặt, hoạt động tốt trong môi trường Jupyter Notebook hoặc Google Colab, phù hợp cho các bài tập xử lý ảnh cơ bản.
## Thuật toán sử dụng

Trong bài này có 2 nhóm thuật toán chính.

Đầu tiên là các kỹ thuật xử lý cơ bản trên ảnh, gồm 4 phần nhỏ là: chuyển ảnh sang grayscale, biến đổi âm bản, nhị phân hóa (thresholding), và log transform.
Tiếp theo là các thuật toán xử lý ảnh bằng bộ lọc, gồm: lọc trung bình (mean filter), lọc trung vị (median filter), phát hiện biên bằng Sobel, và cân bằng histogram.
## Giải thích cách hoạt động
Grayscale: thuật toán này dùng để chuyển ảnh màu (RGB) thành ảnh xám. Việc này giúp đơn giản hóa dữ liệu vì ảnh xám chỉ có một kênh độ sáng thay vì ba kênh màu. Trong bài này, ảnh gốc được chuyển sang ảnh xám trước khi áp dụng các bước xử lý tiếp theo.
# chuyển ảnh sang ảnh xám
```python
from PIL import Image
img = Image.open("input.jpg").convert("L")
```
# nhị phân hóa ảnh theo ngưỡng
Thresholding: dùng để nhị phân hóa ảnh xám, biến ảnh thành trắng đen dựa trên một ngưỡng cố định. Nếu giá trị pixel lớn hơn ngưỡng thì chuyển thành 255 (trắng), ngược lại là 0 (đen). Thuật toán này thường dùng để tách vật thể ra khỏi nền.
```python
import numpy as np
def threshold(img_array, T=128):
    return np.where(img_array > T, 255, 0).astype(np.uint8)
```
# lọc trung bình
Mean Filter: dùng để làm mịn ảnh, bằng cách thay mỗi điểm ảnh bằng giá trị trung bình của các pixel xung quanh. Trong bài này, em dùng kernel 3x3 để tính trung bình đều, giúp làm mờ nhẹ và giảm nhiễu.
```python
def mean_filter(img):
    kernel = np.ones((3, 3)) / 9
    h, w = img.shape
    padded = np.pad(img, 1, mode='constant')
    output = np.zeros_like(img)

    for i in range(h):
        for j in range(w):
            region = padded[i:i+3, j:j+3]
            output[i, j] = np.sum(region * kernel)
    return output
```
# lọc trung vị 3x3
Median Filter: dùng để loại bỏ nhiễu muối tiêu bằng cách thay mỗi pixel bằng giá trị trung vị trong vùng 3x3 lân cận. Lọc này giữ biên ảnh tốt hơn so với lọc trung bình.
```python
def median_filter(img):
    h, w = img.shape
    padded = np.pad(img, 1, mode='constant')
    output = np.zeros_like(img)

    for i in range(h):
        for j in range(w):
            region = padded[i:i+3, j:j+3].flatten()
            output[i, j] = np.median(region)
    return output
```
# phát hiện biên bằng Sobel
Sobel: là thuật toán dùng để phát hiện biên, bằng cách tính đạo hàm theo hai hướng ngang và dọc. Trong bài này, em dùng bộ lọc Sobel để làm nổi bật các cạnh có thay đổi độ sáng lớn.
```python
# phát hiện biên bằng Sobel
def sobel_edge_detection(img):
    Kx = np.array([[1, 0, -1], [2, 0, -2], [1, 0, -1]])
    Ky = np.array([[1, 2, 1], [0, 0, 0], [-1, -2, -1]])
    h, w = img.shape
    padded = np.pad(img, 1, mode='constant')
    output = np.zeros_like(img)

    for i in range(h):
        for j in range(w):
            region = padded[i:i+3, j:j+3]
            gx = np.sum(region * Kx)
            gy = np.sum(region * Ky)
            output[i, j] = min(255, np.sqrt(gx**2 + gy**2))
    return output
```
