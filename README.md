# Báo Cáo Thực Hành: Quản Lý Nhánh Và Giải Quyết Xung Đột (Merge Conflict)

- **Bài tập**: Bài 2 - Quản lý nhánh và Giải quyết xung đột (Session 04 / Git Version Control)
- **Đường dẫn nộp bài**: `SS04/Bai02/README.md`
- **GitHub Remote Repository**: `https://github.com/DevOpsPTIT/SS04_Bai02.git`

---

## 1. Bối cảnh & Mục tiêu

### Bối cảnh
Trong môi trường phát triển phần mềm thực tế, nhiều thành viên nhóm DevOps làm việc song song trên các nhánh (branch) riêng biệt. Khi tiến hành gộp nhánh tính năng (`feature-update`) vào nhánh chính (`main`), nếu cùng một khu vực dòng code trong tệp bị chỉnh sửa theo các cách khác nhau ở cả hai nhánh kể từ commit gốc chung (Base Commit), Git sẽ không thể tự động quyết định đoạn code nào chuẩn xác hơn. Khi đó, Git phát ra thông báo **Merge Conflict** và yêu cầu kỹ sư xử lý thủ công.

### Mục tiêu
- Tạo mới và di chuyển linh hoạt giữa nhánh chính `main` và nhánh tính năng `feature-update`.
- Chủ động tạo tình huống xung đột gộp nhánh trên tệp `README.md`.
- Phát hiện, đọc hiểu và loại bỏ các ký hiệu đánh dấu xung đột thủ công (`<<<<<<< HEAD`, `=======`, `>>>>>>> feature-update`).
- Hiểu rõ cơ chế gộp 3 vùng (3-Way Merge) và thực hiện commit gộp nhánh thành công.
- Trích xuất đồ thị nhánh thông qua lệnh `git log --graph --oneline`.

---

## 2. Kịch Bản Tạo Xung Đột & Xử Lý

```
       A---B (feature-update: Cập nhật nội dung tính năng)
      /     \
  O--A-------C (main: Cập nhật nội dung trực tiếp trên main -> Merge Conflict & Resolve)
```

- **Commit O (`eb2dc50`)**: Commit ban đầu trên nhánh `main`.
- **Commit A (`a31f9b5`)**: Nhánh `feature-update` thay đổi dòng 4 của tệp `README.md`.
- **Commit B (`498d208`)**: Nhánh `main` cũng thay đổi đúng dòng 4 của tệp `README.md`.
- **Commit C (`Merge Commit`)**: Tiến hành `git merge feature-update`, phát sinh xung đột tại dòng 4, xử lý thủ công và commit hoàn tất.

---

## 3. Các Bước Thực Hiện Chi Tiết

### Bước 1: Khởi tạo repository & commit ban đầu trên `main`

```bash
cd SS04/Bai02
git init
git branch -M main
git config --local user.name "DevOps Student"
git config --local user.email "devops.student@example.com"

# Tạo tệp README.md ban đầu
echo "# Dự Án Quản Lý Mã Nguồn - Bài 2" > README.md
git add README.md
git commit -m "feat: initial README.md on main branch"
```

---

### Bước 2: Tạo và chuyển sang nhánh `feature-update`

```bash
git checkout -b feature-update
```

Chỉnh sửa dòng 4 trong `README.md` trên nhánh `feature-update`:
```markdown
Phiên bản được cập nhật từ nhánh feature-update với tính năng bổ sung.
```

Tiến hành lưu vào Staging Area và commit:
```bash
git add README.md
git commit -m "feat(feature-update): update introduction on feature-update branch"
```

---

### Bước 3: Quay lại nhánh `main` và tạo chỉnh sửa gây xung đột

```bash
git checkout main
```

Chỉnh sửa đúng dòng 4 trong `README.md` trên nhánh `main`:
```markdown
Phiên bản được cập nhật trực tiếp trên nhánh main bởi quản trị viên.
```

Tiến hành commit trên `main`:
```bash
git add README.md
git commit -m "feat(main): update introduction directly on main branch"
```

---

### Bước 4: Thực hiện gộp nhánh và ghi nhận xung đột

Chạy lệnh gộp nhánh `feature-update` vào `main`:

```bash
git merge feature-update
```

*Kết quả từ Terminal*:
```console
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

---

### Bước 5: Phân tích & Xử lý xung đột thủ công

Mở tệp `README.md`, Git tự động chèn các đánh dấu xung đột:

```markdown
<<<<<<< HEAD
Phiên bản được cập nhật trực tiếp trên nhánh main bởi quản trị viên.
=======
Phiên bản được cập nhật từ nhánh feature-update với tính năng bổ sung.
>>>>>>> feature-update
```

**Giải thích các ký hiệu**:
- `<<<<<<< HEAD`: Bắt đầu đoạn code thuộc về nhánh hiện tại (`main`).
- `=======`: Ranh giới phân cách giữa 2 sự thay đổi.
- `>>>>>>> feature-update`: Kết thúc đoạn code thuộc về nhánh đang được gộp vào (`feature-update`).

**Thao tác giải quyết**: Xóa bỏ các dòng đánh dấu `<<<<<<<`, `=======`, `>>>>>>>` và kết hợp nội dung cả 2 nhánh một cách hài hòa.

*Nội dung tệp `README.md` sau khi đã xử lý xong xung đột*:
```markdown
# Báo Cáo Thực Hành: Quản Lý Nhánh Và Giải Quyết Xung Đột (Merge Conflict)

## 1. Giới thiệu
Phiên bản tích hợp hoàn chỉnh: Bao gồm cập nhật từ quản trị viên trên nhánh main và bổ sung tính năng từ nhánh feature-update.
```

---

### Bước 6: Hoàn tất Merge Commit

Sau khi chỉnh sửa xong tệp `README.md`:

```bash
# 1. Đưa tệp đã xử lý vào Staging Area
git add README.md

# 2. Thực hiện commit gộp nhánh
git commit -m "fix(merge): resolve merge conflict between main and feature-update"
```

---

## 4. Phân Tích Cơ Chế Gộp 3 Vùng (3-Way Merge)

Khi hai nhánh có sự thay đổi rẽ nhánh (diverged history), Git sử dụng thuật toán **3-Way Merge** dựa trên 3 mốc commit:
1. **Base Commit (Commit chung gần nhất)**: Commit gốc trước khi rẽ nhánh (`eb2dc50`).
2. **Ours Commit (HEAD)**: Commit mới nhất của nhánh hiện tại `main` (`498d208`).
3. **Theirs Commit**: Commit mới nhất của nhánh được gộp `feature-update` (`a31f9b5`).

Nếu một dòng chỉ thay đổi ở nhánh *Ours* hoặc *Theirs* so với *Base*, Git sẽ tự động lấy thay đổi đó. Tuy nhiên, nếu dòng đó thay đổi ở **cả 2 nhánh** so với *Base*, Git không thể tự quyết định và sẽ tạo cảnh báo xung đột (Merge Conflict).

---

## 5. Trích Xuất Log Terminal Chứng Minh (Verification Logs)

### 1. Trạng thái xung đột ban đầu

```console
$ git merge feature-update
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.

$ git status
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   README.md
```

---

### 2. Đồ thị lịch sử commit (`git log --graph --oneline`)

```console
$ git log --graph --oneline --all
*   f8c92e1 (HEAD -> main) fix(merge): resolve merge conflict between main and feature-update
|\  
| * a31f9b5 (feature-update) feat(feature-update): update introduction on feature-update branch
* | 498d208 feat(main): update introduction directly on main branch
|/  
* eb2dc50 feat: initial README.md on main branch
```

---

### 3. Trạng thái kho lưu trữ sau khi xử lý thành công

```console
$ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

---

## 6. Kết Luận

- Đã thực hành thành công quy trình quản lý nhánh và cố ý tạo tình huống Merge Conflict trên tệp `README.md`.
- Đã đọc hiểu và xóa bỏ các dấu vết xung đột (`<<<<<<<`, `=======`, `>>>>>>>`) theo đúng yêu cầu bài tập.
- Hoàn thành Merge Commit ghi nhận cả 2 nhánh tổ tiên (`main` & `feature-update`).
- Đồ thị `git log --graph --oneline` thể hiện rõ nét nhánh rẽ ra và gộp lại chuẩn xác.
