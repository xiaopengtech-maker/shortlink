# Fix Lỗi 404 NOT_FOUND

## Vấn đề
Khi truy cập `/api` bị lỗi 404 NOT_FOUND

## Nguyên nhân
- `vercel.json` config sai routes
- Vercel không tự động detect API routes khi có `builds` config

## Giải pháp

### Đã sửa `vercel.json`:
```json
{
  "version": 2,
  "routes": [
    {
      "src": "/s/([^/]+)",
      "dest": "/api/redirect?code=$1"
    },
    {
      "src": "/(.*\\.(html|css|js|png|jpg|jpeg|gif|svg|ico))",
      "dest": "/public/$1"
    },
    {
      "src": "/",
      "dest": "/public/index.html"
    }
  ]
}
```

### Deploy lại:
```bash
cd shortlink
vercel --prod
```

## Test sau khi deploy

### 1. Test trang chủ
```
https://your-project.vercel.app/
```
Phải hiển thị giao diện rút gọn link

### 2. Test API shorten
```bash
curl -X POST https://your-project.vercel.app/api/shorten \
  -H "Content-Type: application/json" \
  -d '{"url":"https://payment.momo.vn/v2/gateway/pay?t=test&s=test"}'
```

Kết quả mong đợi:
```json
{
  "shortUrl": "https://your-project.vercel.app/s/xxx",
  "shortCode": "xxx",
  "originalUrl": "https://payment.momo.vn/v2/gateway/pay?t=test&s=test"
}
```

### 3. Test redirect
```
https://your-project.vercel.app/s/[shortcode]
```
Phải redirect đến URL gốc

## Lưu ý

- Vercel tự động detect files trong `/api` folder
- Không cần config `builds` nếu dùng structure chuẩn
- Routes phải match đúng pattern
