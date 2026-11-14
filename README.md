
# Interactive Multi-Language Vocabulary System

## 1. Giới thiệu chung (Overview)

**Interactive Multi-Language Vocabulary System** là hệ thống từ điển đa ngôn ngữ (Anh ↔ Việt) kết hợp công cụ học từ vựng thông minh được phát triển bằng ngôn ngữ **C++**. Chương trình áp dụng triệt để các nguyên lý Lập trình Hướng đối tượng (OOP), sử dụng cấu trúc dữ liệu `std::map` từ thư viện STL để tối ưu hóa tốc độ tra cứu và tích hợp thuật toán lặp lại ngắt quãng **Leitner Box System** nhằm nâng cao hiệu quả ghi nhớ từ vựng của người dùng.

---

## 2. Các tính năng chính (Key Features)

1. **Phân loại từ vựng đa dạng (Word Categorization):**

   * Quản lý nhiều nhóm từ vựng khác nhau bao gồm: Từ thông dụng (`GeneralWord`), Thuật ngữ chuyên ngành (`TechnicalTerm`), và Thành ngữ (`Idiom`).
   * Hỗ trợ ánh xạ từ đồng nghĩa (Synonym mapping) và ví dụ ngữ cảnh cho từng loại từ.
2. **Tra cứu hai chiều tốc độ cao (Dual-Direction Lookup):**

   * Hỗ trợ tra cứu tức thời theo cả hai chiều: Tiếng Anh sang Tiếng Việt và Tiếng Việt sang Tiếng Anh.
   * Áp dụng cấu trúc cây đỏ-đen thông qua `std::map<std::string, Word*>` giúp đạt độ phức tạp tìm kiếm $O(\log N)$.
3. **Hệ thống ôn tập ngắt quãng Leitner (Spaced Repetition Study Engine):**

   * Theo dõi mức độ thành thạo (Mastery Level) của từng từ vựng theo thang điểm từ **Box 1** đến **Box 5**.
   * Tự động điều chỉnh tần suất xuất hiện của flashcard: từ trả lời đúng sẽ được thăng cấp lên hộp cao hơn (giảm tần suất ôn), từ trả lời sai sẽ bị đưa về Box 1 (tăng tần suất ôn tập).
4. **Kiểm tra & Tự đánh giá tương tác (Interactive Self-Assessment & Auto-Graded Quiz):**

   * Chế độ học qua Flashcard và bài kiểm tra trắc nghiệm/điền từ tự động chấm điểm.
   * Quản lý phiên học thông qua lớp `QuizSession`, tự động tính toán điểm số và cập nhật trực tiếp trạng thái Leitner Box của từ vựng sau mỗi lượt trả lời.
5. **Lưu trữ & Đồng bộ dữ liệu (File Import/Export Persistence):**

   * Hỗ trợ đọc/ghi cơ sở dữ liệu từ điển thông qua tệp văn bản có cấu trúc (`.txt` / `.csv`).
   * Bảo toàn toàn bộ lịch sử học tập, mức độ thành thạo (Mastery Level) và số lần trả lời đúng/sai giữa các phiên chạy chương trình.
6. **Bảng điều khiển & Phân tích hiệu suất (Progress Dashboard & Performance Metrics):**

   * Thống kê tỷ lệ trả lời chính xác (`Accuracy Percentage`).
   * Phân loại số lượng từ vựng theo từng cấp độ Leitner Box.
   * Tự động nhận diện và lập danh sách "Từ vựng yếu" (`Weak Words`) có tỷ lệ sai cao hoặc kẹt ở Box 1 để ưu tiên ôn luyện.

---

## 3. Kiến trúc kỹ thuật & Thiết kế OOP (System Architecture)

### 3.1. Sơ đồ phân cấp lớp từ vựng (Polymorphism & Inheritance)

* **`Word` (Abstract Base Class):**
  * Thuộc tính chung: `englishWord`, `vietnameseMeaning`, `pronunciation`, `synonyms` (`std::vector<std::string>`), `leitnerBox` (1–5), `correctCount`, `attemptCount`.
  * Phương thức thuần ảo (Pure virtual functions): `displayDetails()`, `getWordType()`, `serialize()`.
* **`GeneralWord` (Derived Class):** Bổ sung thuộc tính từ loại (`partOfSpeech`: Noun, Verb, Adj, Adv) và câu ví dụ giao tiếp.
* **`TechnicalTerm` (Derived Class):** Bổ sung lĩnh vực chuyên ngành (`domain`: IT, Telecommunications, Medical, Finance...) và định nghĩa học thuật.
* **`Idiom` (Derived Class):** Bổ sung nghĩa đen (`literalMeaning`), nghĩa bóng (`figurativeMeaning`) và ngữ cảnh sử dụng.

### 3.2. Các lớp quản lý và nghiệp vụ

* **`DictionaryManager`:** Quản lý bộ nhớ động cho các đối tượng `Word*` và duy trì 2 bảng băm/cây chỉ mục:
  * `std::map<std::string, Word*> enToViIndex;`
  * `std::map<std::string, std::vector<Word*>> viToEnIndex;`
* **`QuizSession`:** Khởi tạo bộ câu hỏi ưu tiên các từ ở Leitner Box thấp, kiểm tra đáp án của người dùng, cập nhật điểm số và điều chỉnh cấp độ Box.
* **`AnalyticsDashboard`:** Tổng hợp dữ liệu từ danh sách từ vựng để xuất báo cáo tiến độ học tập.
* **`FileHandler`:** Đảm nhận việc phân tách chuỗi (parsing) khi nạp dữ liệu đầu vào và định dạng chuẩn hóa khi xuất dữ liệu ra file.

---

## 4. Cấu trúc thư mục:

```text
VocabularySystem/
├── include/
│   ├── Word.h                 # Lớp cơ sở trừu tượng Word
│   ├── GeneralWord.h          # Lớp kế thừa cho từ vựng thông dụng
│   ├── TechnicalTerm.h        # Lớp kế thừa cho thuật ngữ chuyên ngành
│   ├── Idiom.h                # Lớp kế thừa cho thành ngữ
│   ├── DictionaryManager.h    # Quản lý chỉ mục std::map và thao tác CRUD
│   ├── QuizSession.h          # Engine Leitner Box và quản lý phiên kiểm tra
│   ├── AnalyticsDashboard.h   # Tính toán thống kê & nhận diện từ yếu
│   └── FileHandler.h          # Đọc/ghi file dữ liệu có cấu trúc
├── src/
│   ├── main.cpp               # Điểm khởi chạy chương trình & Menu tương tác
│   ├── Word.cpp
│   ├── GeneralWord.cpp
│   ├── TechnicalTerm.cpp
│   ├── Idiom.cpp
│   ├── DictionaryManager.cpp
│   ├── QuizSession.cpp
│   ├── AnalyticsDashboard.cpp
│   └── FileHandler.cpp
├── data/
│   └── dictionary_db.txt      # Cơ sở dữ liệu từ điển và tiến độ học
├── Makefile                   # Kịch bản biên dịch tự động
└── README.md                  # Tài liệu hướng dẫn dự án
```
