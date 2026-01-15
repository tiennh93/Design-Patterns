# Singleton Pattern Example

Thư mục này minh họa quá trình phát triển Singleton Pattern từ cơ bản đến hoàn thiện, giúp bạn hiểu rõ **tại sao** chúng ta cần các kỹ thuật phức tạp như `synchronized` hay `volatile`.

## 1. Cơ bản: `BasicSingleton` (Naive Lazy Initialization)
Đây là cách cài đặt sơ khai nhất (như đã nói ở phần "Làm khi cần"):
- Nguyên lý: Chỉ tạo instance khi `getInstance()` được gọi lần đầu.
- **Vấn đề:** Không an toàn (Non Thread-safe). Code này sẽ "vỡ trận" ngay lập tức trong môi trường đa luồng.

### Tại sao lỗi? (Race Condition)
Khi 2 luồng (Thread A và Thread B) cùng chạy đến dòng kiểm tra `if (instance == null)` **cùng một thời điểm**:
1. Thread A thấy `null`, lọt vào trong để tạo mới.
2. Thread B (chưa kịp thấy A tạo xong) cũng thấy `null`, và cũng lao vào tạo mới.
3. -> **Kết quả**: 2 đối tượng khác nhau được tạo ra. Singleton thất bại!

![Race Condition Diagram](singleton_race_condition.png)

**Minh chứng từ Demo (`SingletonDemo.java`):**
```text
==========================================
   DEMO 1: Basic Singleton (Cố tình làm lỗi)   
==========================================
Thread-Basic-2 nhận được: ... | Hash: 572391714
Thread-Basic-1 nhận được: ... | Hash: 1149800592
=> HashCode khác nhau chứng tỏ đây là 2 object riêng biệt!
```

## 2. Nâng cao: `ThreadSafeSingleton` (Double-Checked Locking)
Đây là giải pháp "Chuẩn chỉ" cho môi trường Production:
1. **Double-Check:** Kiểm tra `null` 2 lần (trước và sau khi lock) để hạn chế việc lock không cần thiết, tăng hiệu năng.
2. **Keyword `volatile`:** Đảm bảo tính "Atomic", ngăn không cho các luồng nhìn thấy một object đang khởi tạo dở dang.

**Kết quả sau khi fix:**
```text
==========================================
   DEMO 2: Thread-Safe Singleton (Đa luồng)  
==========================================
Thread 1 nhận được: ... | Hash: 987654321
Thread 2 nhận được: ... | Hash: 987654321
=> Cả 2 luồng đều nhận về cùng một instance duy nhất.
```

## 3. Biến thể: `DatabaseConnection` (Synchronized Method)
Đây là một cách làm Thread-safe khác đơn giản hơn:
- Dùng `synchronized` ngay trên method `getInstance()`.
- **Ưu điểm:** Code gọn, dễ hiểu.
- **Nhược điểm:** Hiệu năng thấp hơn Double-Checked Locking một chút vì mỗi lần gọi đều phải chờ khóa, dù instance đã được tạo rồi.
- Thường dùng cho các tài nguyên nặng như Database Connection, nơi mà độ trễ khởi tạo không quá quan trọng bằng sự an toàn.

## Cách chạy demo

Bạn có thể chạy file `SingletonDemo.java` trực tiếp trong IntelliJ IDEA hoặc dùng lệnh Gradle:

```bash
./gradlew run
```
