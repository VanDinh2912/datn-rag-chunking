
# Luồng làm việc Colab - Git

Đây là tài liệu hướng dẫn cách tích hợp và sử dụng Git/GitHub với Google Colab để quản lý dự án mã nguồn của bạn.

## Mục lục
1.  [Thiết lập ban đầu](#1-thiết-lập-ban-đầu)
2.  [Clone Repository](#2-clone-repository)
3.  [Làm việc và thay đổi mã nguồn](#3-làm-việc-và-thay-đổi-mã-nguồn)
4.  [Lưu và Đẩy (Push) lên GitHub](#4-lưu-và-đẩy-push-lên-github)

## 1. Thiết lập ban đầu

Để tương tác với GitHub từ Colab, bạn cần: 
- Một tài khoản GitHub.
- Một [Personal Access Token (PAT)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) với quyền truy cập phù hợp (thường là `repo`).

**Trong Colab:**
1. Lưu PAT của bạn vào Colab Secrets với tên `GITHUB_TOKEN`. (Nhấp vào biểu tượng chìa khóa ở thanh bên trái).
2. Cấu hình Git để sử dụng `GITHUB_TOKEN` làm trình trợ giúp xác thực credential.

    ```python
    import os
    from google.colab import userdata

    # Lấy token từ Colab Secrets
    GITHUB_TOKEN = userdata.get('GITHUB_TOKEN')

    # Cấu hình Git để sử dụng token
    !git config --global credential.helper store
    TOKEN_FILE = '/root/.git-credentials'
    with open(TOKEN_FILE, 'w') as f:
        f.write(f'https://oauth2:{GITHUB_TOKEN}@github.com')
    !git config --global credential.helper "store --file {TOKEN_FILE}"
    print("Đã cấu hình Git credential helper.")
    ```

## 2. Clone Repository

Sử dụng lệnh `git clone` để tải repository của bạn về môi trường Colab. Đảm bảo thay thế URL repository bằng của bạn.

```python
repo_url = "https://github.com/VanDinh2912/datn-rag-chunking.git"
repo_name = repo_url.split('/')[-1].replace('.git', '')

# Xóa thư mục cũ nếu tồn tại và clone lại
import os
if os.path.exists(repo_name):
    !rm -rf {repo_name}
!git clone https://$GITHUB_TOKEN@github.com/VanDinh2912/datn-rag-chunking.git

# Di chuyển vào thư mục repo
%cd {repo_name}
!git status
!git remote -v
```

## 3. Làm việc và thay đổi mã nguồn

Bạn có thể chỉnh sửa tệp tin bằng cách sử dụng các lệnh shell hoặc các hàm Python để đọc/ghi tệp.

```python
# Ví dụ: Tạo hoặc chỉnh sửa một tệp tin mới
file_to_change = 'new_feature.py'
code_content = 