# Hướng Dẫn Deploy Lên Vercel Với CI/CD

Tài liệu này hướng dẫn cách cấu hình và deploy dự án Hotel Booking Frontend lên Vercel sử dụng GitHub Actions.

## 📋 Yêu Cầu

- Tài khoản GitHub
- Tài khoản Vercel (đăng ký miễn phí tại [vercel.com](https://vercel.com))
- Dự án đã được push lên GitHub repository

## 🚀 Bước 1: Cài Đặt Vercel CLI (Tùy Chọn)

```bash
npm install -g vercel
```

## 🔑 Bước 2: Lấy Thông Tin Vercel

### 2.1. Lấy Vercel Token

1. Đăng nhập vào [Vercel Dashboard](https://vercel.com/dashboard)
2. Vào **Settings** → **Tokens**
3. Tạo token mới với tên `GITHUB_ACTIONS_TOKEN`
4. Copy token này (chỉ hiển thị 1 lần)

### 2.2. Lấy Organization ID và Project ID

#### Cách 1: Sử dụng Vercel CLI

```bash
# Đăng nhập vào Vercel
vercel login

# Liên kết project (chạy trong thư mục dự án)
vercel link

# Sau khi link, file .vercel/project.json sẽ chứa org ID và project ID
```

Mở file `.vercel/project.json`:
```json
{
  "orgId": "your-org-id-here",
  "projectId": "your-project-id-here"
}
```

#### Cách 2: Từ Vercel Dashboard

1. Tạo project mới trên Vercel Dashboard
2. Import repository từ GitHub
3. Trong **Settings** → **General**, bạn sẽ thấy:
   - **Project ID** trong URL hoặc settings
   - **Team/Organization ID** trong settings

## 🔐 Bước 3: Cấu Hình GitHub Secrets

1. Vào repository GitHub của bạn
2. Chọn **Settings** → **Secrets and variables** → **Actions**
3. Thêm các secrets sau:

| Secret Name | Mô Tả | Cách Lấy |
|-------------|-------|----------|
| `VERCEL_TOKEN` | Token xác thực Vercel | Từ Vercel Dashboard → Settings → Tokens |
| `VERCEL_ORG_ID` | Organization ID | Từ file `.vercel/project.json` hoặc Vercel Dashboard |
| `VERCEL_PROJECT_ID` | Project ID | Từ file `.vercel/project.json` hoặc Vercel Dashboard |

**Lưu ý:** Các giá trị này phải được giữ bí mật!

## 🌍 Bước 4: Cấu Hình Environment Variables (Nếu Có)

Nếu dự án có biến môi trường (ví dụ: API URL):

### Trên Vercel Dashboard:

1. Vào project → **Settings** → **Environment Variables**
2. Thêm các biến cần thiết:
   - `REACT_APP_API_URL`: URL của backend API
   - Các biến khác nếu cần

### Trong GitHub Actions:

File workflow đã được cấu hình sẵn để build với `CI=false` để tránh lỗi warnings.

## 📁 Cấu Trúc Files Đã Tạo

```
hotel-booking-FE/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions workflow
├── vercel.json                 # Cấu hình Vercel
├── .vercelignore              # Files bỏ qua khi deploy
└── DEPLOYMENT.md              # File này
```

## 🔄 Cách Hoạt Động

### Deployment Tự Động:

1. **Push to main/master branch**: 
   - Tự động chạy tests
   - Build project
   - Deploy lên Vercel (Production)

2. **Pull Request**:
   - Tự động chạy tests
   - Build project
   - Deploy preview deployment (có URL riêng để test)

### Workflow Steps:

```yaml
1. Checkout code
2. Setup Node.js v16
3. Install dependencies
4. Run tests
5. Build project
6. Deploy to Vercel
```

## 🧪 Test Deployment

### Deployment Thủ Công (Lần Đầu):

```bash
# Đảm bảo code đã được commit
git add .
git commit -m "Add Vercel CI/CD configuration"

# Push lên GitHub
git push origin main
```

### Kiểm Tra Deployment:

1. Vào tab **Actions** trong GitHub repository
2. Xem workflow đang chạy
3. Sau khi hoàn thành, check Vercel Dashboard để xem deployment

## 🔧 Troubleshooting

### Lỗi: "Missing required secrets"

**Giải pháp:** Đảm bảo đã thêm đủ 3 secrets vào GitHub:
- VERCEL_TOKEN
- VERCEL_ORG_ID  
- VERCEL_PROJECT_ID

### Lỗi: "Build failed"

**Giải pháp:**
1. Kiểm tra logs trong GitHub Actions
2. Đảm bảo project build thành công local: `npm run build`
3. Kiểm tra environment variables

### Lỗi: "Invalid project or organization ID"

**Giải pháp:**
1. Xóa secrets cũ và tạo lại
2. Chạy lại `vercel link` để lấy ID mới
3. Cập nhật secrets trong GitHub

### Build Warnings as Errors

Workflow đã được cấu hình với `CI=false` để warnings không làm fail build. Nếu muốn strict mode, xóa dòng này trong `deploy.yml`.

## 📊 Monitoring

### Vercel Dashboard:

- **Deployments**: Xem lịch sử deployments
- **Analytics**: Xem traffic và performance
- **Logs**: Debug runtime issues

### GitHub Actions:

- **Actions Tab**: Xem workflow runs
- **Email notifications**: Nhận thông báo khi deployment fail

## 🎯 Best Practices

1. **Branch Protection**: 
   - Bật branch protection cho main/master
   - Require status checks trước khi merge

2. **Environment Variables**:
   - Không commit file `.env` vào git
   - Sử dụng Vercel Environment Variables

3. **Testing**:
   - Luôn test trên preview deployment trước khi merge PR
   - Viết tests để CI/CD có thể chạy

4. **Monitoring**:
   - Theo dõi Vercel Analytics
   - Setup error tracking (Sentry, etc.)

## 🔗 Links Hữu Ích

- [Vercel Documentation](https://vercel.com/docs)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Vercel CLI Documentation](https://vercel.com/docs/cli)

## 📝 Notes

- Vercel miễn phí cho personal projects
- Deployment tự động mỗi khi push code
- Preview deployments cho mỗi PR
- Custom domains có thể được cấu hình trong Vercel Dashboard

## 🆘 Support

Nếu gặp vấn đề:
1. Kiểm tra logs trong GitHub Actions
2. Kiểm tra Vercel deployment logs
3. Tham khảo Vercel Discord community
4. Check GitHub Issues của vercel-action

---

**Chúc bạn deploy thành công! 🎉**
