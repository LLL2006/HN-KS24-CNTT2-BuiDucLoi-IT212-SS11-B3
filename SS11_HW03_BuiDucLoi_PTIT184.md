# Bài 3: Tối Ưu Prompt Sinh Docstring & Giải Thích Logic

## 1. Phân tích lỗ hổng của prompt thô sơ hiện tại

- Prompt ban đầu: "Giải thích hàm này làm gì và thêm comment vào code giúp tôi: [Đoạn code]" có nhiều thiếu sót rõ ràng.
- Nó chưa có Vai trò: AI không được hướng dẫn phải làm việc như một Senior Java Developer hay maintainer hệ thống.
- Nó chưa có Mục tiêu cụ thể: người dùng chỉ nói chung chung là "giải thích hàm này làm gì", nhưng không nói rõ là cần sinh Javadoc chuẩn, comment inline, hay giải thích theo ngôn ngữ nghiệp vụ.
- Nó chưa có Ngữ cảnh nghiệp vụ: prompt không cho AI biết hàm này liên quan đến hệ thống thương mại điện tử, chiết khấu khách hàng và voucher freeship.
- Nó chưa có Ràng buộc: không có quy định về việc giữ nguyên logic hiện tại, chỉ được thêm comment/Javadoc, không được sửa hành vi của hàm, và không được bỏ qua các điều kiện phức tạp.
- Nó chưa có Định dạng đầu ra: không nói rõ AI phải trả về mã nguồn Java hoàn chỉnh hay chỉ là giải thích ngắn gọn.
- Vì vậy, kết quả AI thường trả về nội dung quá chung, thiếu tính nghiệp vụ và không đủ độ tin cậy để phục vụ bảo trì.

## 2. Prompt tối ưu mới

- Dưới đây là prompt tối ưu đã áp dụng đầy đủ 5 thành phần: Vai trò, Mục tiêu, Ngữ cảnh, Ràng buộc và Định dạng.

````markdown
Bạn là một Senior Java Developer có kinh nghiệm làm maintainer hệ thống thương mại điện tử.

Nhiệm vụ của bạn là đọc đoạn code Java dưới đây, dịch ngược logic nghiệp vụ và viết lại code sao cho dễ hiểu cho người bảo trì sau này.

Ngữ cảnh:

- Hàm này liên quan đến tính toán chiết khấu cho đơn hàng trong hệ thống E-commerce.
- Biến b có ý nghĩa nghiệp vụ như sau: b = 1 nghĩa là khách hàng hạng Silver, b = 2 nghĩa là khách hàng hạng Gold.
- Biến c là voucher freeship.
- Mục tiêu là giúp team hiện tại hiểu rõ logic mà không cần hỏi lại developer cũ.

Ràng buộc:

- Giữ nguyên logic tính toán hiện tại, không thay đổi hành vi của hàm.
- Chỉ thêm Javadoc cho hàm và comment inline cho các dòng phức tạp.
- Không đổi tên biến một cách tùy tiện nếu không cần thiết; nếu đổi tên thì phải giữ ý nghĩa rõ ràng và phù hợp với nghiệp vụ.
- Cần giải thích rõ ý nghĩa của từng biến và từng nhánh điều kiện.
- Trả về mã nguồn Java hoàn chỉnh, có Javadoc chuẩn và comment inline.

Định dạng đầu ra:

- Chỉ trả về mã nguồn Java trong một code block.
- Không viết lời giải thích bên ngoài code block.

Đoạn code cần xử lý:

```java
public double cD(double a, int b, double c) {
    double d = a;
    if (b == 1) {
        d *= 0.95;
    } else if (b == 2) {
        d *= 0.90;
    }
    if (c > 0) {
        d -= c;
    }
    return Math.max(d, 0);
}
```
````

````

## 3. Kết quả đầu ra mẫu sau khi chạy prompt trên AI

```java
/**
 * Tính toán giá cuối cùng của đơn hàng sau khi áp dụng chiết khấu theo hạng thành viên
 * và voucher freeship.
 *
 * <p>Hàm này giữ nguyên logic cũ nhưng làm cho mục đích sử dụng trở nên rõ ràng hơn
 * cho người bảo trì trong tương lai.</p>
 *
 * @param originalAmount số tiền ban đầu của đơn hàng trước khi áp dụng chiết khấu
 * @param membershipLevel hạng thành viên của khách hàng, trong đó 1 = Silver, 2 = Gold
 * @param freeshipVoucher giá trị voucher freeship được áp dụng cho đơn hàng
 * @return giá trị cuối cùng sau khi đã áp dụng chiết khấu và voucher, tối thiểu bằng 0
 */
public double cD(double originalAmount, int membershipLevel, double freeshipVoucher) {
    // Bắt đầu từ giá tiền ban đầu của đơn hàng.
    double finalAmount = originalAmount;

    // Nếu khách hàng là Silver thì áp dụng chiết khấu 5%.
    if (membershipLevel == 1) {
        finalAmount *= 0.95;
    }
    // Nếu khách hàng là Gold thì áp dụng chiết khấu 10%.
    else if (membershipLevel == 2) {
        finalAmount *= 0.90;
    }

    // Nếu có voucher freeship thì giảm giá trị thanh toán tương ứng.
    if (freeshipVoucher > 0) {
        finalAmount -= freeshipVoucher;
    }

    // Đảm bảo giá trị cuối cùng không âm.
    return Math.max(finalAmount, 0);
}
````

## 4. Nhận xét kết quả

- Kết quả trên đã cải thiện rõ rệt so với prompt ban đầu vì nó:
  - gắn được biến với ngữ cảnh nghiệp vụ,
  - tạo ra Javadoc chuẩn,
  - thêm comment inline cho các nhánh logic phức tạp,
  - và giúp người bảo trì hiểu được ý nghĩa của hàm một cách nhanh chóng.
