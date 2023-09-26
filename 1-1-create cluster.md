دستور `kind create cluster` یک دستور است که توسط ابزار KIND (Kubernetes IN Docker) استفاده می‌شود تا یک کلاستر Kubernetes محلی در Docker ایجاد کند. این کلاستر بر اساس کانتینر‌ها اجرا می‌شود و به عنوان یک محیط توسعه و تست بسیار مفید است.

**مثال:**


```bash
kind create cluster
```

**آپشن‌ها:**


1. `-h` یا `--help`:
   نمایش راهنمای دستور و توضیحات مربوط به آن.

2. `--name`:
   تعیین نام کلاستر که به صورت پیش‌فرض به `kind` اختصاص دارد.

3. `--config`:
   استفاده از یک فایل تنظیمات YAML برای ایجاد کلاستر. این امکان به شما اجازه می‌دهد تا تنظیمات مخصوص خود را اعمال کنید.

4. `--image`:
   مشخص کردن تصویر ابتدایی برای کلیه کنترل پلیرها (control plane nodes) و کارگرها (worker nodes). مقدار پیش‌فرض برای این تصویر `kindest/node` است.

5. `--kubeconfig`:
   مشخص کردن مسیر فایل `kubeconfig` که برای دسترسی به کلاستر استفاده می‌شود.

6. `--wait`:
   انتظار برای اینکه تمامی کمپوننت‌های کلاستر (مانند API Server و Controller Manager) راه‌اندازی شوند.

7. `--loglevel`:
   تعیین سطح لاگ‌گیری (مثلاً debug، info و غیره).

8. `--retain`:
   حفظ کردن کلاستر حتی پس از اجرا کامل شدن دستور.

9. `--config`:
   مشخص کردن یک فایل تنظیمات YAML برای استفاده در ایجاد کلاستر.

10. `--image`:
    مشخص کردن تصویر Docker برای ایجاد کنترل پلیرها و کارگرها.

11. `--kubeconfig`:
    مشخص کردن مسیر فایل kubeconfig که برای دسترسی به کلاستر استفاده می‌شود.

مثال:

```bash
kind create cluster --name my-cluster --image kindest/node:v1.21.1
```

در این مثال، یک کلاستر با نام `my-cluster` و تصویر مشخص شده برای نسخه ۱.۲۱.۱ ایجاد می‌شود.