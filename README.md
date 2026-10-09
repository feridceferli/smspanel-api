# SMS Panel API — Render quraşdırması

Orijinal layihə: Arslan-MD. Bu versiya şəxsi hesab üçün cookie əsaslı Flask API-ni giriş açarı ilə qoruyur.

## Render
- Repository: https://github.com/feridceferli/smspanel-api
- Build: `pip install -r requirements.txt`
- Start: `gunicorn --bind 0.0.0.0:$PORT --workers 1 --threads 2 --timeout 120 app:app`
- Health: `/health`
- Plan: Free (boşdayanmada yuxuya gedə bilər)

## Environment
- `PRIVATE_API_KEY`: öz API-nə giriş üçün uzun təsadüfi açar.
- `COOKIES_JSON`: öz IVAS hesabının sessiyası:
```json
{"ivas_sms_session":"SESSION_VALUE","XSRF-TOKEN":"XSRF_VALUE"}
```
Cookie-ləri yalnız Render Environment-də saxlayın. `cookies.json` faylından giriş artıq oxunmur. Real dəyərləri GitHub-a yazmayın.

## Yoxlama
Server:
```bash
curl https://YOUR_SERVICE.onrender.com/health
```

Sessiya:
```bash
curl -X POST https://YOUR_SERVICE.onrender.com/session/check -H "X-API-Key: YOUR_PRIVATE_API_KEY"
```

Öz hesabının məlumatları:
```bash
curl "https://YOUR_SERVICE.onrender.com/sms?date=09/10/2026&limit=1" -H "X-API-Key: YOUR_PRIVATE_API_KEY"
```

Açarsız şəxsi endpoint-lər 401, açar konfiqurasiya edilməyibsə 503 qaytarır.
Cookie və provider giriş problemi 502 qaytarır. Bu serverin mütləq işləməməsi demək deyil.
Bu tətbiq IVAS giriş yoxlamasını keçmir; HTTP 403 olduqda provayderin giriş icazəsi və ya rəsmi API-si tələb oluna bilər.
Sessiya yoxlaması cookie-ləri yenidən yükləyir; cookie dəyişdikdən sonra Render deploy-u tamamlanmalıdır.
Endpoint-lərə yalnız öz hesabınız üçün giriş verin.

## Dəyişikliklər
- SMS və sessiya endpoint-ləri `X-API-Key` ilə qorunur.
- Cookie, CSRF, SMS və HTML məzmunu loglara yazılmır.
- Adi `requests.Session` istifadə olunur; challenge həll edən kitabxana yoxdur.
- Providerə import zamanı sorğu göndərilmir.
- Cavab avtomatik açılır; əlavə gzip/brotli açılması yoxdur.
- Standart limit 10, maksimum 100 mesajdır.
- Qlobal sessiyaya paralel giriş kilidlə idarə olunur; bir Gunicorn worker istifadə edin.
