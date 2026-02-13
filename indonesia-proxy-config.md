# Konfigurasi Proxy Indonesia untuk Camofox-browser

## Proxy Indonesia untuk Situs Lokal

### Environment Variables untuk Indonesia:
```bash
# Proxy Indonesia (contoh)
export PROXY_HOST=103.123.45.67
export PROXY_PORT=8080
export PROXY_USERNAME=user_id
export PROXY_PASSWORD=pass_id

# Atau tanpa auth
export PROXY_HOST=203.190.113.123
export PROXY_PORT=3128

# Start server
npm start
```

### Docker dengan Proxy Indonesia:
```bash
docker run -p 9377:9377 \
  -e PROXY_HOST=103.123.45.67 \
  -e PROXY_PORT=8080 \
  -e PROXY_USERNAME=user_id \
  -e PROXY_PASSWORD=pass_id \
  camofox-browser
```

## Cookie Import untuk Situs Indonesia

### 1. Export Cookies dari Browser:
- Install ekstensi "cookies.txt" untuk Chrome/Firefox
- Login ke situs target (Jobstreet, Tokopedia, Shopee)
- Export cookies dalam format Netscape (.txt)

### 2. Simpan Cookie Files:
```bash
mkdir -p ~/.camofox/cookies
cp ~/Downloads/jobstreet_cookies.txt ~/.camofox/cookies/jobstreet.txt
cp ~/Downloads/tokopedia_cookies.txt ~/.camofox/cookies/tokopedia.txt
cp ~/Downloads/shopee_cookies.txt ~/.camofox/cookies/shopee.txt
```

### 3. Generate API Key:
```bash
openssl rand -hex 32
# Contoh: 5f4dcc3b5aa765d61d8327deb882cf99
```

### 4. Set Environment:
```bash
export CAMOFOX_API_KEY="5f4dcc3b5aa765d61d8327deb882cf99"
export CAMOFOX_COOKIES_DIR="$HOME/.camofox/cookies"
```

### 5. Import Cookies via API:
```bash
# Import cookies Jobstreet
curl -X POST http://localhost:9377/sessions/agent1/cookies \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_CAMOFOX_API_KEY' \
  -d '{"cookiesPath": "jobstreet.txt"}'

# Import cookies Tokopedia  
curl -X POST http://localhost:9377/sessions/agent1/cookies \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_CAMOFOX_API_KEY' \
  -d '{"cookiesPath": "tokopedia.txt"}'
```

## Search Macros Baru untuk Indonesia:

### 1. Jobstreet:
```bash
# Search jobs
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@jobstreet_search",
  "query": "software engineer jakarta"
}

# Hasil: https://www.jobstreet.co.id/id/job-search/software%20engineer%20jakarta-jobs/
```

### 2. Tokopedia:
```bash
# Search produk
POST /tabs/:tabId/navigate
{
  "userId": "agent1", 
  "macro": "@tokopedia_search",
  "query": "laptop asus rog"
}

# Hasil: https://www.tokopedia.com/search?st=product&q=laptop%20asus%20rog
```

### 3. Shopee:
```bash
# Search produk
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@shopee_search", 
  "query": "xiaomi redmi note 13"
}

# Hasil: https://shopee.co.id/search?keyword=xiaomi%20redmi%20note%2013
```

### 4. Bukalapak:
```bash
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@bukalapak_search",
  "query": "sepatu nike"
}
```

### 5. Blibli:
```bash
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@blibli_search",
  "query": "tv led 32 inch"
}
```

### 6. Lazada:
```bash
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@lazada_search",
  "query": "kulkas 2 pintu"
}
```

## Contoh Workflow Lengkap:

### Scenario: Cari Laptop di Tokopedia
```bash
# 1. Create tab
POST /tabs
{
  "userId": "agent1",
  "sessionKey": "tokopedia_search",
  "url": "https://www.tokopedia.com"
}

# 2. Navigate dengan macro search
POST /tabs/:tabId/navigate
{
  "userId": "agent1",
  "macro": "@tokopedia_search",
  "query": "laptop gaming murah"
}

# 3. Get snapshot untuk lihat hasil
GET /tabs/:tabId/snapshot?userId=agent1

# 4. Click produk pertama
POST /tabs/:tabId/click
{
  "userId": "agent1",
  "ref": "e1"
}

# 5. Get detail produk
GET /tabs/:tabId/snapshot?userId=agent1
```

## Tips untuk Situs Indonesia:

1. **Proxy Lokal**: Gunakan proxy Indonesia untuk akses lebih cepat
2. **User-Agent**: Camoufox sudah otomatis spoof fingerprint
3. **Rate Limiting**: Tambah delay antar request untuk hindari blokir
4. **Session Management**: Gunakan `userId` berbeda untuk tiap akun
5. **Cookie Refresh**: Update cookies secara berkala (setiap 7-14 hari)

## Troubleshooting:

### Issue: Situs blokir akses
**Solusi:**
- Ganti proxy IP
- Clear cookies dan login ulang
- Tambah delay antar request
- Gunakan `sessionKey` berbeda

### Issue: Login gagal
**Solusi:**
- Export cookies ulang setelah login sukses
- Pastikan format cookies Netscape
- Cek domain cookies sesuai situs target

### Issue: Slow performance  
**Solusi:**
- Gunakan proxy dengan bandwidth tinggi
- Kurangi jumlah tab simultan
- Enable caching jika tersedia