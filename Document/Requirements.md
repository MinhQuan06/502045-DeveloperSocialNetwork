# TÀI LIỆU YÊU CẦU DỰ ÁN MẠNG XÃ HỘI DEV

---

## THÔNG TIN DỰ ÁN

**Tên dự án:** Mạng xã hội Dev
**Mục tiêu:** Xây dựng nền tảng cho developer quản lý team, repository và file với hệ thống phân quyền  
**Thời gian:** ~3 tháng
**Nhóm thực hiện:** 3 người  
**Ngôn ngữ:** C# .NET  
**Kiến trúc:** 3-layer (GUI - BUS - DAL)  
**Database:** SQL Server  

---

## I. YÊU CẦU CHỨC NĂNG 

###  F1. QUẢN LÝ NGƯỜI DÙNG (4 chức năng)

#### F1.1 Đăng ký tài khoản
**Mô tả:** Người dùng mới có thể tạo tài khoản trong hệ thống

**Input:**
- Username (3-20 ký tự, duy nhất)
- Email (format email hợp lệ, duy nhất)
- Password (tối thiểu 6 ký tự)
- Họ tên
- Số điện thoại (optional)

**Output:**
- Thành công: Tài khoản được tạo, chuyển đến màn hình đăng nhập
- Thất bại: Thông báo lỗi (username/email đã tồn tại)

**Validation:**
- Username không chứa ký tự đặc biệt
- Email đúng format (có @ và domain)
- Password phải có ít nhất 6 ký tự

---

#### F1.2 Đăng nhập / Đăng xuất
**Mô tả:** Người dùng đăng nhập để sử dụng hệ thống

**Input:**
- Username hoặc Email
- Password

**Output:**
- Thành công: Lưu session, chuyển đến màn hình chính
- Thất bại: Thông báo "Sai tên đăng nhập hoặc mật khẩu"

**Đăng xuất:**
- Xóa session
- Quay về màn hình đăng nhập

---

#### F1.3 Xem và cập nhật thông tin cá nhân
**Mô tả:** User xem và chỉnh sửa profile của mình

**Thông tin hiển thị:**
- Username (không cho sửa)
- Email
- Họ tên
- Số điện thoại
- Ngày đăng ký (read-only)

**Cho phép cập nhật:**
- Email
- Họ tên  
- Số điện thoại

---

#### F1.4 Đổi mật khẩu
**Mô tả:** User thay đổi mật khẩu

**Input:**
- Mật khẩu cũ
- Mật khẩu mới
- Xác nhận mật khẩu mới

**Validation:**
- Mật khẩu cũ phải đúng
- Mật khẩu mới khác mật khẩu cũ
- Mật khẩu mới = Xác nhận mật khẩu

---

###  F2. QUẢN LÝ TEAM (5 chức năng)

**LƯU Ý QUAN TRỌNG:** 
- không có phân cấp cha-con
- Mỗi team độc lập với nhau
- User có thể tham gia nhiều team

---

#### F2.1 Tạo team mới
**Mô tả:** User tạo team mới, tự động trở thành Leader

**Input:**
- Tên team (duy nhất, 3-50 ký tự)
- Mô tả team (optional, tối đa 500 ký tự)

**Output:**
- Team được tạo
- User tạo tự động là Leader
- Chuyển đến màn hình quản lý team

**Business Rule:**
- Mỗi team phải có ít nhất 1 Leader
- Người tạo team = Leader đầu tiên

---

#### F2.2 Cập nhật thông tin team
**Mô tả:** Leader cập nhật tên, mô tả của team

**Quyền:** Chỉ Leader mới được phép

**Cho phép cập nhật:**
- Tên team
- Mô tả team

---

#### F2.3 Thêm thành viên vào team
**Mô tả:** Leader mời user khác vào team

**Quyền:** Chỉ Leader

**Input:**
- Username hoặc Email của user cần thêm
- Vai trò: Leader hoặc Member

**Output:**
- Thành công: User được thêm vào team
- Thất bại: User không tồn tại / Đã trong team

**Lưu ý:**
- 1 team có thể có nhiều Leader
- User có thể là Leader của team này, Member của team khác

---

#### F2.4 Xóa thành viên khỏi team
**Mô tả:** Leader xóa thành viên ra khỏi team

**Quyền:** Chỉ Leader

**Business Rule:**
- Không xóa được Leader cuối cùng (team phải có ít nhất 1 Leader)
- Khi xóa thành viên → User mất quyền từ team đó

---

#### F2.5 Xóa team
**Mô tả:** Leader xóa toàn bộ team

**Quyền:** Chỉ Leader

**Business Rule:**
- Xóa team → Xóa tất cả phân quyền của team
- Xóa team → Không xóa repository (repository có thể thuộc nhiều team)
- Cần xác nhận trước khi xóa

---

###  F3. QUẢN LÝ REPOSITORY (6 chức năng)

#### F3.1 Tạo repository
**Mô tả:** User tạo repository mới

**Input:**
- Tên repository (duy nhất, 3-50 ký tự)
- Mô tả (optional)
- Trạng thái: Public / Private
- Chọn team sở hữu (trong danh sách team mà user là Leader)

**Output:**
- Repository được tạo
- Team được chọn tự động có quyền WRITE

**Business Rule:**
- Chỉ Leader của team mới tạo được repository cho team đó
- Repository phải thuộc ít nhất 1 team

---

#### F3.2 Gán repository cho team khác
**Mô tả:** Cho phép team khác cũng quản lý repository này

**Quyền:** Leader của team đang sở hữu repository

**Input:**
- Chọn team muốn gán
- Chọn quyền: READ hoặc WRITE

**Lưu ý:**
- 1 repository có thể được gán cho nhiều team
- Mỗi team có quyền riêng (READ hoặc WRITE)

---

#### F3.3 Thiết lập quyền truy cập repository
**Mô tả:** Thay đổi quyền của team đối với repository

**Quyền:** Leader của team sở hữu chính

**Có thể thay đổi:**
- READ → WRITE
- WRITE → READ

**Lưu ý:** Áp dụng ngay lập tức cho tất cả thành viên của team

---

#### F3.4 Thay đổi trạng thái repository (Public / Private)
**Mô tả:** Chuyển repository giữa Public và Private

**Quyền:** Leader của team sở hữu chính

**Public:**
- Mọi user trong hệ thống có quyền READ
- Chỉ user có quyền WRITE mới được upload/xóa file

**Private:**
- Chỉ user có quyền (qua team) mới được truy cập
- User không có quyền sẽ không thấy repository này

---

#### F3.5 Xóa repository
**Mô tả:** Xóa hoàn toàn repository

**Quyền:** Chỉ Leader của team sở hữu chính

**Business Rule:**
- Nếu repository có file → Yêu cầu xác nhận
- Xóa repository → Xóa tất cả file bên trong
- Xóa repository → Xóa tất cả phân quyền liên quan

**Lưu ý:** Nên có confirmation dialog trước khi xóa

---

#### F3.6 Xem danh sách repository
**Mô tả:** User xem repository mà mình có quyền truy cập

**Hiển thị:**
- Repository Public (tất cả)
- Repository Private mà user có quyền (qua team)

**Thông tin hiển thị:**
- Tên repository
- Trạng thái (Public/Private)
- Quyền của user (READ/WRITE)
- Số lượng file
- Team sở hữu

**Filter:**
- Tất cả repository
- Repository mà tôi là Leader
- Repository chỉ có quyền READ
- Repository có quyền WRITE

---

###  F4. QUẢN LÝ FILE (5 chức năng)

#### F4.1 Upload file vào repository
**Mô tả:** Upload file vào repository

**Quyền:** Chỉ user có quyền WRITE

**Input:**
- Chọn file từ máy tính
- Repository đích

**Validation:**
- File size tối đa: 50MB
- Kiểm tra định dạng file (optional: chặn .exe, .bat)
- Không upload file trùng tên (hoặc auto-rename)

**Output:**
- File được lưu vào database hoặc folder
- Metadata được lưu (tên, size, người upload, ngày upload)

---

#### F4.2 Xem danh sách file trong repository
**Mô tả:** Hiển thị tất cả file trong repository

**Quyền:** User có quyền READ hoặc WRITE

**Thông tin hiển thị:**
| Tên file | Loại | Kích thước | Người upload | Ngày upload |
|----------|------|------------|--------------|-------------|

**Actions:**
- View (nếu là text file)
- Download
- Delete (nếu có quyền WRITE)

---

#### F4.3 Xem nội dung file
**Mô tả:** Xem nội dung file ngay trong ứng dụng

**Quyền:** User có quyền READ hoặc WRITE

**Hỗ trợ:**
- Text files (.txt, .md, .json, .xml, .cs)
- Hiển thị trong TextBox hoặc RichTextBox

**Không hỗ trợ:**
- Binary files (.pdf, .docx, .xlsx, .jpg, .png)
- → Chỉ cho phép Download

---

#### F4.4 Download file
**Mô tả:** Tải file về máy

**Quyền:** User có quyền READ hoặc WRITE

**Flow:**
1. User click Download
2. Hiển thị SaveFileDialog
3. Lưu file vào vị trí user chọn

---

#### F4.5 Xóa file
**Mô tả:** Xóa file khỏi repository

**Quyền:** Chỉ user có quyền WRITE

**Business Rule:**
- Yêu cầu xác nhận trước khi xóa
- Xóa file khỏi storage + database

---

## II. QUY ĐỊNH NGHIỆP VỤ (BUSINESS RULES)

### BR1. Quyền truy cập gián tiếp
- User **KHÔNG** được cấp quyền trực tiếp
- Quyền **CHỈ** được cấp thông qua Team
- User có quyền = User thuộc Team có quyền

**Ví dụ:**
```
User A muốn có quyền READ Repo X
→ KHÔNG cấp trực tiếp cho User A
→ Thêm User A vào Team T
→ Cấp quyền READ Repo X cho Team T
→ User A có quyền READ Repo X
```

---

### BR2. Quyền Repository → File
- User có quyền READ trên Repository
  → User có quyền READ **tất cả** file trong đó

- User có quyền WRITE trên Repository
  → User có quyền WRITE **tất cả** file trong đó

**Lưu ý:** Không có phân quyền riêng cho từng file

---

### BR3. Thu hồi quyền
- Khi thu hồi quyền của Team đối với Repository
  → **Tất cả** thành viên trong Team **mất quyền ngay lập tức**

**Ví dụ:**
```
Team A có 5 members, có quyền WRITE Repo X
Leader thu hồi quyền
→ Cả 5 members đều mất quyền WRITE Repo X
```

---

### BR4. Không trùng quyền
- 1 Team không được cấp cả READ và WRITE cho cùng 1 Repository
- Quyền WRITE tự động bao gồm quyền READ

**Logic:**
- Nếu team chưa có quyền → Cấp READ hoặc WRITE
- Nếu team đã có READ → Có thể nâng lên WRITE
- Nếu team đã có WRITE → Có thể hạ xuống READ
- Không được cấp cả READ và WRITE cùng lúc

---

### BR5. Xóa thành viên khỏi team
- User bị xóa khỏi Team A
  → User **mất toàn bộ** quyền từ Team A
  → Không ảnh hưởng quyền từ Team B, C, D (nếu có)

**Ví dụ:**
```
User X thuộc Team A (WRITE Repo 1) và Team B (READ Repo 2)
User X bị xóa khỏi Team A
→ Mất quyền WRITE Repo 1
→ Vẫn còn quyền READ Repo 2 (từ Team B)
```

---

### BR6. Repository Public vs Private

**Repository Public:**
- **Mọi user** trong hệ thống có quyền **READ**
- Chỉ user có quyền WRITE mới được upload/xóa file

**Repository Private:**
- **Chỉ** user có quyền (qua team) mới truy cập được
- User không có quyền:
  - Không thấy repository trong danh sách
  - Không truy cập được file

---

### BR7. Ưu tiên quyền từ nhiều team (ĐƠN GIẢN)

Khi User thuộc **nhiều team** có quyền khác nhau trên **cùng 1 repository**:

**Nguyên tắc: Quyền cao hơn thắng**
- WRITE > READ
- Nếu có ít nhất 1 team cấp WRITE → User có WRITE

**Ví dụ:**
```
User A thuộc:
- Team X (READ Repo ABC)
- Team Y (WRITE Repo ABC)

→ User A có quyền WRITE Repo ABC (vì WRITE > READ)
```

---

### BR8. Ràng buộc vai trò Leader
- Mỗi team phải có **ít nhất 1 Leader**
- Không xóa được Leader cuối cùng của team
- Nếu muốn rời team mà là Leader duy nhất:
  → Phải bổ nhiệm Leader mới trước

---

### BR9. Giới hạn Upload File
- Kích thước file tối đa: **50MB** / file
- Định dạng cấm (optional): .exe, .bat, .cmd, .sh
- Xử lý file trùng tên:
  - **Option 1:** Từ chối upload, yêu cầu đổi tên
  - **Option 2:** Auto-rename (thêm timestamp)
  - **Option 3:** Ghi đè (cần confirm)

---

## III. BIỂU MẪU VÀ GIAO DIỆN

### BM1: FORM ĐĂNG NHẬP

```
┌─────────────────────────────────────┐
│         ĐĂNG NHẬP HỆ THỐNG          │
├─────────────────────────────────────┤
│                                     │
│  Username/Email: [____________]     │
│                                     │
│  Mật khẩu:       [____________]     │
│                                     │
│  [x] Ghi nhớ đăng nhập              │
│                                     │
│     [  Đăng nhập  ] [  Đăng ký  ]   │
│                                     │
└─────────────────────────────────────┘
```

---

### BM2: FORM ĐĂNG KÝ

```
┌─────────────────────────────────────┐
│          ĐĂNG KÝ TÀI KHOẢN          │
├─────────────────────────────────────┤
│                                     │
│  Username:    [____________]  *     │
│  Email:       [____________]  *     │
│  Họ tên:      [____________]  *     │
│  Số ĐT:       [____________]        │
│  Mật khẩu:    [____________]  *     │
│  Nhập lại:    [____________]  *     │
│                                     │
│     [  Đăng ký  ] [   Hủy   ]       │
│                                     │
└─────────────────────────────────────┘
```

---

### BM3: FORM TRANG CHỦ (MAIN DASHBOARD)

```
┌────────────────────────────────────────────────────┐
│ DEV Network            [Profile] [Logout]          │
├───────────────┬────────────────────────────────────┤
│               │                                    │
│ MENU          │   DANH SÁCH REPOSITORY             │
│               │                                    │
│ > My Repos    │   ┌──────────────────────────┐    │
│   Teams       │   │ Repo A    [Public]  READ │    │
│   Create Repo │   │ 15 files        Team X   │    │
│   Create Team │   └──────────────────────────┘    │
│               │                                    │
│               │   ┌──────────────────────────┐    │
│               │   │ Repo B   [Private] WRITE │    │
│               │   │ 8 files         Team Y   │    │
│               │   └──────────────────────────┘    │
│               │                                    │
│               │   [Filter: All ▼] [Search...]     │
│               │                                    │
└───────────────┴────────────────────────────────────┘
```

---

### BM4: FORM QUẢN LÝ TEAM

```
┌────────────────────────────────────────────────────┐
│ QUẢN LÝ TEAM: Team Alpha                           │
├────────────────────────────────────────────────────┤
│                                                    │
│ Tên Team:  [Team Alpha_______________]             │
│ Mô tả:     [Development team for project X...]    │
│                                                    │
│ [  Cập nhật  ]                                     │
│                                                    │
├────────────────────────────────────────────────────┤
│ DANH SÁCH THÀNH VIÊN:                              │
│                                                    │
│ ┌──────────────────────────────────────────────┐  │
│ │ Username  │  Email         │ Role   │ Action │  │
│ ├──────────────────────────────────────────────┤  │
│ │ john_doe  │ john@dev.com   │ Leader │  ---   │  │
│ │ jane_dev  │ jane@dev.com   │ Member │ [Xóa]  │  │
│ │ bob_code  │ bob@dev.com    │ Member │ [Xóa]  │  │
│ └──────────────────────────────────────────────┘  │
│                                                    │
│ [ + Thêm thành viên ]                              │
│                                                    │
└────────────────────────────────────────────────────┘
```

---

### BM5: FORM QUẢN LÝ REPOSITORY

```
┌────────────────────────────────────────────────────┐
│ REPOSITORY: Project Documentation                  │
├────────────────────────────────────────────────────┤
│                                                    │
│ Tên Repo:    [Project Documentation____________]  │
│ Trạng thái:  ( ) Public  (•) Private               │
│ Team chính:  [Team Alpha ▼]                        │
│ Mô tả:       [All documentation files...]         │
│                                                    │
│ [  Cập nhật  ]  [  Xóa Repo  ]                     │
│                                                    │
├────────────────────────────────────────────────────┤
│ PHÂN QUYỀN CHO CÁC TEAM:                           │
│                                                    │
│ ┌────────────────────────────────────────┐        │
│ │ Team      │ Quyền  │ Action            │        │
│ ├────────────────────────────────────────┤        │
│ │ Team Beta │ READ   │ [Sửa] [Thu hồi]   │        │
│ └────────────────────────────────────────┘        │
│                                                    │
│ [ + Gán quyền cho team khác ]                      │
│                                                    │
├────────────────────────────────────────────────────┤
│ DANH SÁCH FILE:                 [↑Upload File]    │
│                                                    │
│ ┌────────────────────────────────────────────┐    │
│ │ File          │ Size  │ Upload by │ Date   │    │
│ ├────────────────────────────────────────────┤    │
│ │ readme.md     │ 2KB   │ john_doe  │ 12/01  │    │
│ │ design.pdf    │ 5MB   │ jane_dev  │ 15/01  │    │
│ └────────────────────────────────────────────┘    │
│                                                    │
└────────────────────────────────────────────────────┘
```

---

### BM6: DIALOG UPLOAD FILE

```
┌─────────────────────────────────────┐
│         UPLOAD FILE                 │
├─────────────────────────────────────┤
│                                     │
│  Repository: [Project Docs     ▼]  │
│                                     │
│  File:  [C:\...\document.pdf]  [...│
│         (Max 50MB)                  │
│                                     │
│  File size: 3.2 MB                  │
│                                     │
│     [  Upload  ]  [  Cancel  ]      │
│                                     │
└─────────────────────────────────────┘
```

---

### BM7: DIALOG PHÂN QUYỀN

```
┌─────────────────────────────────────┐
│      PHÂN QUYỀN REPOSITORY          │
├─────────────────────────────────────┤
│                                     │
│  Repository: Project Documentation  │
│                                     │
│  Chọn Team:  [Team Beta        ▼]  │
│                                     │
│  Quyền:      (•) READ               │
│              ( ) WRITE              │
│                                     │
│     [   Cấp quyền   ]  [  Hủy  ]    │
│                                     │
└─────────────────────────────────────┘
```
| Mã YC | Chức năng | Mã Biểu mẫu | Quy định NV | Ghi chú |
|-------|-----------|-------------|-------------|---------|
| **F1.1** | Đăng ký tài khoản | BM2 | - | Validation username unique, email format |
| **F1.2** | Đăng nhập / Đăng xuất | BM1 | - | Lưu session, chuyển đến màn hình chính |
| **F1.3** | Xem và cập nhật thông tin cá nhân | BM1 | - | Email, họ tên, SĐT có thể sửa |
| **F1.4** | Đổi mật khẩu | BM1 | - | Validate mật khẩu cũ đúng |
| **F2.1** | Tạo team mới | BM2, BM3 | BR8 | User tạo tự động là Leader |
| **F2.2** | Cập nhật thông tin team | BM2 | - | Chỉ Leader được phép |
| **F2.3** | Thêm thành viên vào team | BM3 | - | Leader thêm User với vai trò Leader/Member |
| **F2.4** | Xóa thành viên khỏi team | BM3 | BR5, BR8 | Không xóa Leader cuối cùng |
| **F2.5** | Xóa team | BM2 | BR8 | Cascade delete phân quyền |
| **F3.1** | Tạo repository | BM4 | - | Team chọn tự động có WRITE |
| **F3.2** | Gán repository cho team khác | BM4, BM6 | BR1 | Gán quyền READ/WRITE cho team |
| **F3.3** | Thiết lập quyền truy cập repository | BM6 | BR1, BR4 | Thay đổi READ ↔ WRITE |
| **F3.4** | Thay đổi trạng thái repository | BM4 | BR6 | Public ↔ Private |
| **F3.5** | Xóa repository | BM4 | - | Confirm nếu có file |
| **F3.6** | Xem danh sách repository | BM4, BM8 | BR6 | Public + Private có quyền |
| **F4.1** | Upload file vào repository | BM5 | BR2, BR9 | Chỉ user có WRITE, max 50MB |
| **F4.2** | Xem danh sách file | BM5 | BR2 | User có quyền READ/WRITE |
| **F4.3** | Xem nội dung file | BM5 | BR2 | Chỉ text files, binary → download |
| **F4.4** | Download file | BM5 | BR2 | User có READ/WRITE |
| **F4.5** | Xóa file | BM5 | BR2 | Chỉ user có WRITE |
| **F6.1** | Gán quyền repository cho team | BM6 | BR1, BR4 | Không trùng quyền (BR4) |
| **F6.2** | Thu hồi quyền đã cấp | BM6 | BR3 | Tất cả members mất quyền ngay |
---
## IV. TÍCH HỢP AI 


#### **1. AI Chatbot Support**

**Tính năng:**
- Chatbot trả lời câu hỏi về cách sử dụng hệ thống
- Hướng dẫn user làm việc với team, repo, file
- Giải thích quyền truy cập

**Cách làm:**
- Sử dụng **Claude API** hoặc **OpenAI API**
- Tạo 1 form chat đơn giản trong GUI
**Ưu điểm:**
-  Dễ implement (chỉ cần gọi API)
-  Thêm "wow factor" cho dự án
-  Không ảnh hưởng core logic
-  Có thể làm sau, không block main features