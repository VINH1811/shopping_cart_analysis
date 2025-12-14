# Blog Chủ đề 2: Product Hub – Tìm sản phẩm trung tâm để tối ưu chiến lược cross-selling trong siêu thị

**Nhóm tác giả:** Nhóm 3  
**Lớp:** CNTT17-13  
**Thành viên:** Nguyễn Văn Vinh – Đỗ Văn Vinh – Bạch Ngọc Lương – Lại Thành Đoàn  
**Ngày:** 13/12/2025

---

## 1. Thuật toán Apriori

### 1.1. Khái niệm
Thuật toán **Apriori** được công bố bởi *R. Agrawal và R. Srikant (1994)* nhằm tìm ra các **tập phổ biến (frequent itemsets)** trong cơ sở dữ liệu giao dịch lớn. Tên gọi Apriori xuất phát từ việc thuật toán sử dụng **kiến thức có trước (prior knowledge)** về các tập mục thường xuyên xuất hiện để giảm không gian tìm kiếm.

Nguyên lý cốt lõi:
> **Nếu một tập sản phẩm không phổ biến, thì mọi tập con mở rộng từ nó cũng không phổ biến.**

### 1.2. Chiến lược chính
- **Xây dựng từ dưới lên (Bottom-up)**  
  Bắt đầu từ 1-itemset → 2-itemset → 3-itemset …
  ![Xây dựng từ dưới lên](image/anh1.png)
- **Tìm kiếm theo chiều rộng (Breadth-first search – BFS)**  
  Duyệt toàn bộ các itemset cùng kích thước trước khi tăng cấp.
![Tìm kiếm theo chiều rộng](image/anh2.png)
### 1.3. Mục tiêu của Apriori
- Phát hiện các **mối quan hệ mua kèm** giữa các sản phẩm
- Hỗ trợ:
  - Hiểu hành vi khách hàng
  - Tối ưu cross-selling
  - Sắp xếp hàng hóa hiệu quả

Ví dụ:
> Nếu nhiều khách hàng mua *điện thoại* kèm *kính cường lực* → sinh ra luật kết hợp có giá trị cho bán chéo.

---

## 2. Các chỉ số cốt lõi trong Apriori

### 2.1. Support (Độ hỗ trợ)
- Đo tần suất xuất hiện của sản phẩm hoặc tập sản phẩm
- Công thức:
```
Support = Số giao dịch chứa sản phẩm / Tổng số giao dịch
```
- Vai trò: Lọc các sản phẩm ít xuất hiện (nhiễu)

### 2.2. Confidence (Độ tin cậy)
- Đo xác suất mua B khi đã mua A
- Luật dạng: `A → B`
- Confidence cao → khả năng mua kèm cao

### 2.3. Lift (Độ nâng)
- Đo mức độ mua kèm **vượt trội so với ngẫu nhiên**
- Ý nghĩa:
  - Lift = 1 → Ngẫu nhiên
  - Lift > 1 → Liên quan tích cực
  - Lift < 1 → Liên quan tiêu cực

> **Trong bài toán Product Hub, Lift là chỉ số quan trọng nhất để đánh giá giá trị thực của luật.**

---

## 3. Product Hub – Sản phẩm trung tâm

### 3.1. Product Hub là gì?
**Product Hub không phải sản phẩm bán chạy nhất**, mà là:
> Sản phẩm xuất hiện nhiều nhất trong các **luật kết hợp mạnh (Lift và Confidence cao)**.

Một sản phẩm được xem là Product Hub khi:
- Xuất hiện thường xuyên trong cả **antecedent** và **consequent**
- Tham gia vào nhiều luật có Lift cao
- Đóng vai trò kết nối hành vi mua sắm

### 3.2. Vì sao cần tìm Product Hub?
- Khách hàng thường mua **theo nhóm sản phẩm liên quan**
- Product Hub giúp:
  - Tối ưu cross-selling
  - Thiết kế combo
  - Quy hoạch bố trí kệ hàng

---

## 4. Quy trình thực hiện của nhóm

### 4.1. Pipeline phân tích
1. Load và làm sạch dữ liệu
2. Tạo ma trận giỏ hàng (Basket – Boolean / One-hot)
3. Áp dụng Apriori để tìm frequent itemsets
4. Sinh luật kết hợp
5. Trực quan hóa bằng Network Graph
6. Phân tích insight và đề xuất kinh doanh

### 4.2. Tiền xử lý dữ liệu
- Loại bỏ giao dịch hủy (`InvoiceNo` bắt đầu bằng `C`)
- Loại bỏ dòng có:
  - Quantity ≤ 0
  - UnitPrice ≤ 0
- Chỉ giữ dữ liệu tại **United Kingdom**

**Sau lọc:**
- 485,123 giao dịch
- 4,007 sản phẩm

---

## 5. Áp dụng Apriori

### 5.1. Chuẩn bị dữ liệu
- Dòng: Hóa đơn (InvoiceNo)
- Cột: Sản phẩm (Description)
- Giá trị:
  - `True` nếu sản phẩm xuất hiện
  - `False` nếu không xuất hiện

### 5.2. Tham số sử dụng
- `min_support = 0.01`
- `min_confidence = 0.3`
- `min_lift = 1.2`

```python
from mlxtend.frequent_patterns import apriori, association_rules

frequent_itemsets = apriori(
    basket_bool,
    min_support=0.01,
    use_colnames=True
)

rules = association_rules(
    frequent_itemsets,
    metric="lift",
    min_threshold=1.0
)

rules = rules.sort_values(["lift", "confidence"], ascending=False)
```

---

## 6. Kết quả & Trực quan hóa

### 6.1. Cụm sản phẩm HERB MARKER
Các sản phẩm hình thành một cụm liên kết chặt chẽ:
- HERB MARKER PARSLEY
- HERB MARKER THYME
- HERB MARKER ROSEMARY
- HERB MARKER BASIL
- HERB MARKER MINT
- HERB MARKER CHIVES

### 6.2. Product Hub trung tâm
- **HERB MARKER THYME**
- **HERB MARKER PARSLEY**

Hai sản phẩm này có:
- Nhiều liên kết vào/ra nhất
- Khả năng kéo theo các sản phẩm khác trong giỏ hàng

---

## 7. Insight chính

1. Nhóm HERB MARKER tạo thành **product family**, không nên bán rời
2. Thyme & Parsley là **Product Hub trung tâm**
3. Lift rất cao (≈ 60–70) → mua kèm có chủ đích
4. Mối quan hệ mua sắm mang tính **hai chiều**
5. Phù hợp tối ưu cross-selling và bố trí theo cụm

---

## 8. Đề xuất kinh doanh

### 8.1. Bố trí kệ hàng
- Gộp toàn bộ nhóm HERB vào một khu vực
- Đặt Thyme & Parsley ở vị trí trung tâm, ngang tầm mắt

### 8.2. Combo sản phẩm
- Combo gia vị Ý
- Combo herb cho người mới nấu ăn

### 8.3. Chiến lược khuyến mãi
- Giảm giá nhẹ Product Hub
- Gợi ý mua kèm thay vì giảm giá tràn lan

### 8.4. Ứng dụng Online & POS
- Khi thêm Thyme/Parsley → gợi ý ngay các sản phẩm herb liên quan

---

## 9. Kết luận
Thuật toán Apriori không chỉ giúp tìm luật kết hợp, mà còn là **công cụ ra quyết định kinh doanh**. Việc xác định Product Hub cho phép doanh nghiệp tối ưu cross-selling, bố trí kệ hàng và nâng cao giá trị trung bình mỗi giỏ hàng.

Nhóm HERB MARKER với Thyme và Parsley đóng vai trò trung tâm là minh chứng rõ ràng cho giá trị thực tiễn của Apriori trong bán lẻ.

---

## 10. Tài nguyên
- Notebook: `preprocessing_and_eda.ipynb`, `basket_preparation.ipynb`, `apriori_modelling.ipynb`
- Repository & Slide: theo link nhóm cung cấp

