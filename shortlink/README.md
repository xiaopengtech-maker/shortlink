# URL Shortener - Không cần Database

Rút gọn link đơn giản sử dụng Base64 encoding, không cần database.

## Đặc điểm

✅ **Không cần Database** - Sử dụng Base64 encoding  
✅ **Deploy lên Vercel miễn phí**  
✅ **Tối ưu cho link MoMo** - Chỉ lưu parameters `t` và `s`  
✅ **Serverless** - Chạy trên Vercel Functions  
✅ **CORS enabled** - Có thể gọi từ localhost  

## Cài đặt

```bash
cd shortlink
npm install -g vercel
```

## Deploy lên Vercel

### Bước 1: Login
```bash
vercel login
```

### Bước 2: Deploy
```bash
vercel
```

### Bước 3: Deploy Production
```bash
vercel --prod
```

## Sử dụng

### Web Interface
Truy cập: `https://your-project.vercel.app`

### API

#### Tạo short link
```bash
POST https://your-project.vercel.app/api/shorten
Content-Type: application/json

{
  "url": "https://payment.momo.vn/v2/gateway/pay?t=xxx&s=yyy"
}

Response:
{
  "shortUrl": "https://your-project.vercel.app/s/abc123",
  "shortCode": "abc123",
  "originalUrl": "https://payment.momo.vn/v2/gateway/pay?t=xxx&s=yyy"
}
```

#### Redirect
```bash
GET https://your-project.vercel.app/s/abc123
# Tự động redirect đến URL gốc
```

## Cách hoạt động

### Encoding
1. Nhận URL MoMo: `https://payment.momo.vn/v2/gateway/pay?t=xxx&s=yyy`
2. Trích xuất parameters `t` và `s`
3. Tạo JSON: `{"t":"xxx","s":"yyy"}`
4. Base64 encode: `eyJ0IjoieHh4IiwicyI6Inl5eSJ9`
5. URL-safe: `eyJ0IjoieHh4IiwicyI6Inl5eSJ9` (replace +, /, =)
6. Short URL: `https://your-domain.vercel.app/s/eyJ0IjoieHh4IiwicyI6Inl5eSJ9`

### Decoding
1. Nhận shortcode: `eyJ0IjoieHh4IiwicyI6Inl5eSJ9`
2. Restore Base64 format
3. Decode: `{"t":"xxx","s":"yyy"}`
4. Rebuild URL: `https://payment.momo.vn/v2/gateway/pay?t=xxx&s=yyy`
5. Redirect

## Tích hợp với Lazada MoMo Tool

Tool Lazada MoMo (chạy localhost) có thể gọi API shortlink từ Vercel:

```javascript
// Trong tool Lazada MoMo
const SHORTLINK_API = 'https://your-project.vercel.app/api/shorten';

async function shortenMomoLink(momoUrl) {
    const response = await fetch(SHORTLINK_API, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ url: momoUrl })
    });
    const data = await response.json();
    return data.shortUrl;
}
```

## Cấu trúc Project

```
shortlink/
├── api/
│   ├── shorten.js      # API tạo short link
│   └── redirect.js     # API redirect
├── public/
│   └── index.html      # Web interface
├── vercel.json         # Vercel config
├── package.json
└── README.md
```

## Routes

- `/` → `public/index.html` (Web interface)
- `/api/shorten` → Tạo short link
- `/s/{code}` → Redirect đến URL gốc

## Lưu ý

⚠️ Link được encode trong URL, không lưu database  
⚠️ Link không có thời gian hết hạn  
⚠️ Không theo dõi số lượt click  
⚠️ Phù hợp cho link tạm thời như MoMo payment  

## Troubleshooting

### Lỗi 404
- Kiểm tra `vercel.json` routes
- Đảm bảo deploy thành công

### Lỗi CORS
- API đã enable CORS cho tất cả origins
- Kiểm tra browser console

### Link không decode được
- Kiểm tra shortcode có đúng format không
- Thử decode thủ công bằng Base64 decoder
