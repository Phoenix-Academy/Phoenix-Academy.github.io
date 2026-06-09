---
title: "امنیت GitHub از روز اول: قبل از Push کردن، فکر کن!"
summary: "راهنمای عملی امنیت GitHub برای دانشجوها و توسعه‌دهنده‌ها؛ از امن‌سازی محیط محلی تا Branch Protection، Push Protection و مدیریت نشت Secret."
date: 2026-06-09
authors:
  - admin
tags:
  - GitHub
  - Git
  - Security
  - DevSecOps
  - Secrets
  - Dependabot
categories:
  - هوش مصنوعی عامل‌محور
  - آموزش رباتیک
lang: fa
image:
  filename: Git-security.png   
---

اگر تازه وارد دنیای برنامه‌نویسی، رباتیک، هوش مصنوعی یا پروژه‌های تیمی شده باشید، احتمالاً <bdi dir="ltr">GitHub</bdi> برایتان شبیه یک دفترچه منظم برای نگهداری کدهاست. اما واقعیت این است که <bdi dir="ltr">GitHub</bdi> فقط یک فضای ذخیره‌سازی نیست؛ بخشی از زنجیره امنیتی پروژه شماست.

خیلی از نشت‌های اطلاعاتی از یک اشتباه ساده شروع می‌شوند: یک فایل <bdi dir="ltr">.env</bdi>، یک <bdi dir="ltr">API Token</bdi>، یک <bdi dir="ltr">Private Key</bdi> یا یک کانفیگ تستی که اشتباهی وارد <bdi dir="ltr">Git</bdi> شده و بعد به سرور <bdi dir="ltr">remote</bdi> <bdi dir="ltr">push</bdi> می‌شود.

نکته کلیدی اینجاست: <bdi dir="ltr">Git</bdi> تاریخچه را نگه می‌دارد. یعنی اگر یک <bdi dir="ltr">secret</bdi> را <bdi dir="ltr">commit</bdi> کنید، پاک‌کردن آن از نسخه فعلی فایل کافی نیست؛ چون ممکن است همچنان در تاریخچه <bdi dir="ltr">commit</bdi>ها، <bdi dir="ltr">fork</bdi>ها، کش‌ها یا <bdi dir="ltr">clone</bdi>های دیگران باقی بماند.

در این مقاله، امنیت <bdi dir="ltr">GitHub</bdi> را در سه لایه ساده و عملی بررسی می‌کنیم:

1. امن‌سازی محیط محلی توسعه
2. کنترل دسترسی به <bdi dir="ltr">Repository</bdi>
3. فعال‌سازی <bdi dir="ltr">Guardrail</bdi>ها روی <bdi dir="ltr">GitHub</bdi>

---

## ۱. امن‌سازی محیط محلی توسعه

امنیت <bdi dir="ltr">GitHub</bdi> از خود <bdi dir="ltr">GitHub</bdi> شروع نمی‌شود؛ از لپ‌تاپ شما شروع می‌شود. قبل از اینکه چیزی <bdi dir="ltr">push</bdi> شود، باید مطمئن شویم اطلاعات حساس اصلا وارد <bdi dir="ltr">commit</bdi> نمی‌شوند.

### ۱.۱ از `.gitignore` جدی استفاده کنید

فایل <bdi dir="ltr">.gitignore</bdi> مشخص می‌کند چه فایل‌هایی نباید توسط <bdi dir="ltr">Git</bdi> ردیابی شوند. این فایل یکی از ساده‌ترین و مهم‌ترین ابزارهای امنیتی پروژه است.

برای مثال، در پروژه‌های <bdi dir="ltr">Python</bdi>، <bdi dir="ltr">Node.js</bdi>، <bdi dir="ltr">ROS</bdi>، رباتیک یا هوش مصنوعی، معمولا باید این موارد را <bdi dir="ltr">ignore</bdi> کنید:

```gitignore
# Environment files
.env
.env.*
*.env

# Secrets and keys
*.pem
*.key
*.p12
*.crt
id_rsa
id_ed25519

# Local config files
config/local.yaml
config/secrets.yaml
settings.local.json

# Python
__pycache__/
*.pyc
.venv/
venv/

# Node.js
node_modules/

# Logs and temporary files
*.log
/tmp/
.cache/

# OS and editor files
.DS_Store
.vscode/
.idea/
```

اما یک نکته مهم وجود دارد: اگر فایلی قبلا توسط <bdi dir="ltr">Git</bdi> <bdi dir="ltr">track</bdi> شده باشد، اضافه‌کردن آن به <bdi dir="ltr">.gitignore</bdi> کافی نیست. باید آن را از <bdi dir="ltr">index</bdi> خارج کنید:

```bash
git rm --cached .env
git commit -m "Remove env file from tracking"
```

بعد از آن، مقدارهای حساس را داخل <bdi dir="ltr">environment variable</bdi>، <bdi dir="ltr">secret manager</bdi> یا تنظیمات امن <bdi dir="ltr">CI/CD</bdi> نگهداری کنید، نه داخل کد.

### ۱.۲ قبل از Commit، کد را برای Secret اسکن کنید

حتی اگر <bdi dir="ltr">.gitignore</bdi> خوبی داشته باشید، باز هم ممکن است یک <bdi dir="ltr">token</bdi> یا <bdi dir="ltr">password</bdi> داخل یک فایل عادی جا بماند. برای همین بهتر است از <bdi dir="ltr">pre-commit hook</bdi> استفاده کنید.

<bdi dir="ltr">Pre-commit hook</bdi> یک مرحله کوچک است که قبل از نهایی‌شدن <bdi dir="ltr">commit</bdi> اجرا می‌شود. اگر ابزار امنیتی چیزی مشکوک پیدا کند، <bdi dir="ltr">commit</bdi> متوقف می‌شود و شما فرصت دارید مشکل را قبل از <bdi dir="ltr">push</bdi> اصلاح کنید.

دو ابزار رایج برای این کار:

- <bdi dir="ltr">Gitleaks</bdi> برای اسکن <bdi dir="ltr">secret</bdi>ها در کد و تاریخچه <bdi dir="ltr">Git</bdi>
- <bdi dir="ltr">GitGuardian / ggshield</bdi> برای تشخیص <bdi dir="ltr">secret</bdi>ها با <bdi dir="ltr">Git hook</bdi> و <bdi dir="ltr">API</bdi>

نمونه ساده برای استفاده از <bdi dir="ltr">Gitleaks</bdi> با <bdi dir="ltr">pre-commit</bdi>:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: <latest-release>
    hooks:
      - id: gitleaks
```

سپس:

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

از این به بعد، هر بار که <bdi dir="ltr">commit</bdi> می‌زنید، اسکن امنیتی قبل از <bdi dir="ltr">commit</bdi> اجرا می‌شود.

### ۱.۳ Commitهای خود را Sign کنید

در <bdi dir="ltr">Git</bdi>، نام و ایمیل نویسنده <bdi dir="ltr">commit</bdi> به‌تنهایی اثبات نمی‌کند که واقعا همان شخص آن را ساخته است. با <bdi dir="ltr">commit signing</bdi> می‌توانید <bdi dir="ltr">commit</bdi>های خود را با <bdi dir="ltr">GPG</bdi> یا <bdi dir="ltr">SSH</bdi> امضا کنید تا در <bdi dir="ltr">GitHub</bdi> با وضعیت <bdi dir="ltr">Verified</bdi> نمایش داده شوند.

برای شروع ساده با <bdi dir="ltr">SSH signing</bdi>:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

بعد از اضافه‌کردن کلید امضا به <bdi dir="ltr">GitHub</bdi>، <bdi dir="ltr">commit</bdi>های شما می‌توانند به صورت <bdi dir="ltr">Verified</bdi> نمایش داده شوند.

<bdi dir="ltr">Commit signing</bdi> مخصوصا در پروژه‌های تیمی، متن‌باز و پروژه‌هایی که به <bdi dir="ltr">CI/CD</bdi> وصل هستند اهمیت دارد؛ چون کمک می‌کند تغییرات مشکوک یا جعلی راحت‌تر تشخیص داده شوند.

---

## ۲. کنترل دسترسی به Repository

بعد از امن‌سازی محیط محلی، مرحله بعدی این است که مشخص کنیم چه کسی با چه سطح دسترسی می‌تواند روی پروژه کار کند.

### ۲.۱ احراز هویت چندمرحله‌ای را فعال کنید

برای هر حسابی که به <bdi dir="ltr">repository</bdi> دسترسی دارد، <bdi dir="ltr">MFA</bdi> یا <bdi dir="ltr">2FA</bdi> باید فعال باشد. اگر رمز عبور یک نفر لو برود اما <bdi dir="ltr">2FA</bdi> فعال باشد، مهاجم هنوز برای ورود به حساب به مرحله دوم نیاز دارد.

بهتر است از این گزینه‌ها استفاده کنید:

- <bdi dir="ltr">TOTP app</bdi> مثل <bdi dir="ltr">Google Authenticator</bdi>، <bdi dir="ltr">Authy</bdi> یا <bdi dir="ltr">1Password</bdi>
- <bdi dir="ltr">Security key</bdi> یا <bdi dir="ltr">passkey</bdi>
- <bdi dir="ltr">Recovery code</bdi> که در جای امن ذخیره شده باشد

استفاده از <bdi dir="ltr">SMS</bdi> بهتر از نداشتن <bdi dir="ltr">2FA</bdi> است، اما برای حساب‌های مهم بهتر است <bdi dir="ltr">TOTP</bdi>، <bdi dir="ltr">passkey</bdi> یا <bdi dir="ltr">security key</bdi> را در اولویت بگذارید.

### ۲.۲ اصل حداقل دسترسی را رعایت کنید

در پروژه‌های دانشجویی و استارتاپی معمولا یک اشتباه رایج وجود دارد: همه <bdi dir="ltr">Admin</bdi> می‌شوند.

اما در امنیت، اصل مهمی به نام <bdi dir="ltr">Least Privilege</bdi> داریم. یعنی هر فرد فقط باید همان سطح دسترسی را داشته باشد که برای انجام کارش لازم است.

برای مثال:

| نقش | مناسب برای |
|---|---|
| <bdi dir="ltr">Read</bdi> | افرادی که فقط باید کد را ببینند یا بررسی کنند |
| <bdi dir="ltr">Triage</bdi> | افرادی که <bdi dir="ltr">Issue</bdi> و <bdi dir="ltr">Pull Request</bdi> را مدیریت می‌کنند اما نباید کد <bdi dir="ltr">push</bdi> کنند |
| <bdi dir="ltr">Write</bdi> | توسعه‌دهندگانی که روی <bdi dir="ltr">feature branch</bdi> کار می‌کنند |
| <bdi dir="ltr">Maintain</bdi> | افرادی که پروژه را مدیریت می‌کنند اما نیازی به دسترسی کامل <bdi dir="ltr">Admin</bdi> ندارند |
| <bdi dir="ltr">Admin</bdi> | فقط برای مالک پروژه یا مسئول اصلی امنیت و زیرساخت |

اگر کسی فقط قرار است یک بخش کوچک از پروژه را بررسی کند، نیازی نیست به کل <bdi dir="ltr">organization</bdi> یا همه <bdi dir="ltr">repository</bdi>ها دسترسی داشته باشد.

### ۲.۳ به‌جای Password از SSH Key یا Token امن استفاده کنید

برای ارتباط با <bdi dir="ltr">GitHub</bdi>، بهتر است از <bdi dir="ltr">SSH key</bdi> با <bdi dir="ltr">passphrase</bdi> استفاده کنید. <bdi dir="ltr">Password</bdi> برای عملیات <bdi dir="ltr">GitHub</bdi> مناسب نیست و در بسیاری از سناریوها باید از <bdi dir="ltr">SSH key</bdi> یا <bdi dir="ltr">Personal Access Token</bdi> استفاده شود.

ساخت کلید SSH:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

اضافه کردن کلید به SSH Agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

تغییر <bdi dir="ltr">remote</bdi> از <bdi dir="ltr">HTTPS</bdi> به <bdi dir="ltr">SSH</bdi>:

```bash
git remote set-url origin git@github.com:USERNAME/REPOSITORY.git
```

نکته مهم: کلید خصوصی <bdi dir="ltr">SSH</bdi> مثل رمز عبور است. آن را هیچ‌وقت داخل <bdi dir="ltr">Git</bdi>، پیام‌رسان، ایمیل یا فایل اشتراکی قرار ندهید.

---

## ۳. فعال‌سازی Guardrailها روی GitHub

<bdi dir="ltr">Guardrail</bdi> یعنی حفاظ‌هایی که جلوی اشتباهات خطرناک را می‌گیرند. حتی اگر یک نفر عجله کند یا خسته باشد، تنظیمات <bdi dir="ltr">repository</bdi> باید جلوی آسیب جدی را بگیرد.

### ۳.۱ Branch Protection را برای `main` فعال کنید

شاخه <bdi dir="ltr">main</bdi> یا <bdi dir="ltr">master</bdi> معمولا نسخه اصلی پروژه است. نباید هر کسی بتواند مستقیم روی آن <bdi dir="ltr">push</bdi> کند.

حداقل تنظیمات پیشنهادی برای <bdi dir="ltr">Branch Protection</bdi>:

- Require Pull Request before merging
- Require at least 1 approving review
- Require status checks to pass before merging
- Require signed commits
- Block force pushes
- Block branch deletion
- Restrict who can push to matching branches

با این تنظیمات، تغییرات ابتدا وارد <bdi dir="ltr">feature branch</bdi> می‌شوند، سپس از طریق <bdi dir="ltr">Pull Request</bdi> بررسی می‌شوند و فقط اگر تست‌ها پاس شوند، وارد شاخه اصلی می‌شوند.

برای تیم‌های کوچک، همین یک تنظیم می‌تواند جلوی بسیاری از خرابکاری‌ها و اشتباهات را بگیرد.

### ۳.۲ Push Protection را فعال کنید

<bdi dir="ltr">Secret Scanning</bdi> معمولا زمانی هشدار می‌دهد که یک <bdi dir="ltr">secret</bdi> در <bdi dir="ltr">repository</bdi> پیدا شود. اما <bdi dir="ltr">Push Protection</bdi> یک قدم جلوتر است: قبل از اینکه <bdi dir="ltr">secret</bdi> به <bdi dir="ltr">repository</bdi> برسد، <bdi dir="ltr">push</bdi> را <bdi dir="ltr">block</bdi> می‌کند.

برای مثال اگر کسی اشتباهی یک <bdi dir="ltr">API key</bdi>، <bdi dir="ltr">token</bdi> یا <bdi dir="ltr">credential</bdi> را <bdi dir="ltr">commit</bdi> کند، <bdi dir="ltr">Push Protection</bdi> می‌تواند جلوی <bdi dir="ltr">push</bdi> را بگیرد و از کاربر بخواهد ابتدا <bdi dir="ltr">secret</bdi> را حذف کند.

این ویژگی مخصوصا برای پروژه‌هایی که با <bdi dir="ltr">API</bdi>های خارجی، <bdi dir="ltr">cloud</bdi>، <bdi dir="ltr">database</bdi>، ربات‌ها، پهپادها یا سرورهای دانشگاهی کار می‌کنند بسیار مهم است؛ چون لو رفتن یک <bdi dir="ltr">token</bdi> می‌تواند باعث سوءاستفاده مالی، خرابی سرویس یا دسترسی غیرمجاز به داده‌ها شود.

### ۳.۳ Dependabot را فعال کنید

امنیت فقط مربوط به کدی نیست که خودتان می‌نویسید. بخش بزرگی از پروژه‌های امروزی روی <bdi dir="ltr">package</bdi>ها و <bdi dir="ltr">library</bdi>های دیگر ساخته می‌شوند.

<bdi dir="ltr">Dependabot</bdi> می‌تواند <bdi dir="ltr">dependency</bdi>های آسیب‌پذیر را تشخیص دهد و برای به‌روزرسانی آن‌ها <bdi dir="ltr">Pull Request</bdi> بسازد.

نمونه ساده برای `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

اگر پروژه شما <bdi dir="ltr">Python</bdi>، <bdi dir="ltr">Node.js</bdi>، <bdi dir="ltr">Docker</bdi>، <bdi dir="ltr">GitHub Actions</bdi> یا <bdi dir="ltr">ROS package</bdi> دارد، بهتر است <bdi dir="ltr">dependency</bdi>ها را مرتب بررسی کنید. در پروژه‌های رباتیکی، یک نسخه آسیب‌پذیر از یک کتابخانه می‌تواند روی سیستم شبیه‌سازی، <bdi dir="ltr">backend</bdi>، داشبورد یا حتی <bdi dir="ltr">pipeline</bdi> دیپلوی اثر بگذارد.

### ۳.۴ دسترسی GitHub Actions را محدود کنید

اگر از <bdi dir="ltr">GitHub Actions</bdi> استفاده می‌کنید، مراقب باشید <bdi dir="ltr">workflow</bdi>ها بیش از حد دسترسی نداشته باشند.

یک شروع امن:

```yaml
permissions:
  contents: read
```

سپس فقط برای <bdi dir="ltr">job</bdi>هایی که واقعا نیاز دارند، دسترسی بیشتر تعریف کنید. برای مثال اگر <bdi dir="ltr">workflow</bdi> فقط تست اجرا می‌کند، معمولا نیازی به <bdi dir="ltr">write access</bdi> ندارد.

### ۳.۵ یک ارزیابی سریع قبل از اولین Push

اگر هنوز مطمئن نیستید وضعیت مخزن امن است یا نه، این بررسی سریع را قبل از اولین <bdi dir="ltr">push</bdi> انجام دهید:

```bash
# 1) فایل‌های حساس احتمالی که Track شده‌اند
git ls-files | grep -E '(\.env|\.pem|\.key|id_rsa|id_ed25519|secrets?\.ya?ml)$'

# 2) جست‌وجوی الگوهای خطرناک در فایل‌های متنی
git grep -n -E '(AKIA[0-9A-Z]{16}|ghp_[A-Za-z0-9]{36}|BEGIN (RSA|OPENSSH) PRIVATE KEY)'

# 3) مرور آخرین commit قبل از push
git show --name-only --stat --oneline -1
```

اگر خروجی مشکوک دیدید، اول مشکل را رفع کنید و بعد <bdi dir="ltr">push</bdi> بزنید.

---

## اگر Secret قبلاً لو رفته باشد چه کنیم؟

اگر یک <bdi dir="ltr">API key</bdi>، <bdi dir="ltr">token</bdi>، <bdi dir="ltr">password</bdi> یا <bdi dir="ltr">private key</bdi> را <bdi dir="ltr">commit</bdi> کرده‌اید، فقط پاک‌کردن فایل کافی نیست.

مراحل پیشنهادی:

1. فورا <bdi dir="ltr">secret</bdi> را از سرویس اصلی <bdi dir="ltr">revoke</bdi> یا <bdi dir="ltr">rotate</bdi> کنید.
2. <bdi dir="ltr">secret</bdi> جدید بسازید و آن را در محیط امن ذخیره کنید.
3. فایل حساس را از <bdi dir="ltr">repository</bdi> حذف کنید.
4. تاریخچه <bdi dir="ltr">Git</bdi> را با ابزارهایی مثل <bdi dir="ltr">git filter-repo</bdi> یا <bdi dir="ltr">BFG</bdi> پاک‌سازی کنید.
5. همه اعضای تیم را مطلع کنید تا نسخه‌های <bdi dir="ltr">clone</bdi>شده را به‌روزرسانی کنند.
6. لاگ‌های سرویس مربوطه را بررسی کنید تا مطمئن شوید از <bdi dir="ltr">secret</bdi> لو‌رفته سوءاستفاده نشده است.

مهم‌ترین بخش همین مرحله اول است: اگر <bdi dir="ltr">secret</bdi> واقعا به بیرون رفته، آن را مرده فرض کنید.

---

## چک‌لیست سریع برای شروع

اگر می‌خواهید همین امروز <bdi dir="ltr">repository</bdi> خود را امن‌تر کنید، از این چک‌لیست شروع کنید:

- [ ] فایل <bdi dir="ltr">.gitignore</bdi> را کامل کنید.
- [ ] فایل‌های <bdi dir="ltr">.env</bdi>، کلیدها و کانفیگ‌های محلی را از <bdi dir="ltr">Git</bdi> خارج کنید.
- [ ] <bdi dir="ltr">Pre-commit hook</bdi> برای <bdi dir="ltr">secret scanning</bdi> نصب کنید.
- [ ] <bdi dir="ltr">2FA</bdi> را برای حساب <bdi dir="ltr">GitHub</bdi> فعال کنید.
- [ ] <bdi dir="ltr">SSH key</bdi> امن با <bdi dir="ltr">passphrase</bdi> بسازید.
- [ ] سطح دسترسی اعضای تیم را بازبینی کنید.
- [ ] <bdi dir="ltr">Branch Protection</bdi> را برای <bdi dir="ltr">main</bdi> فعال کنید.
- [ ] <bdi dir="ltr">Push Protection</bdi> یا <bdi dir="ltr">Secret Scanning</bdi> را فعال کنید.
- [ ] <bdi dir="ltr">Dependabot Alerts</bdi> و <bdi dir="ltr">Security Updates</bdi> را فعال کنید.
- [ ] دسترسی <bdi dir="ltr">GitHub Actions</bdi> را محدود کنید.

---

## جمع‌بندی

امنیت <bdi dir="ltr">GitHub</bdi> پیچیده نیست، اما باید از روز اول جدی گرفته شود. بسیاری از مشکلات امنیتی بزرگ از اشتباهات کوچک شروع می‌شوند: یک <bdi dir="ltr">.env</bdi> فراموش‌شده، یک <bdi dir="ltr">token</bdi> داخل کد، یک <bdi dir="ltr">Admin</bdi> اضافه، یا یک <bdi dir="ltr">push</bdi> مستقیم به <bdi dir="ltr">main</bdi>.

اگر تازه‌کار هستید، لازم نیست همه مفاهیم امنیت نرم‌افزار را یک‌باره یاد بگیرید. کافی است از چند عادت ساده شروع کنید:

کد حساس را <bdi dir="ltr">commit</bdi> نکنید، قبل از <bdi dir="ltr">commit</bdi> اسکن کنید، دسترسی‌ها را محدود کنید، روی شاخه اصلی <bdi dir="ltr">guardrail</bdi> بگذارید، و <bdi dir="ltr">dependency</bdi>ها را مرتب بررسی کنید.

همین چند قدم ساده می‌تواند <bdi dir="ltr">repository</bdi> شما را از یک پروژه آسیب‌پذیر به یک پروژه قابل اعتمادتر تبدیل کند.

---

## پیشنهاد تصویر برای بلاگ

برای <bdi dir="ltr">thumbnail</bdi> یا تصویر اصلی بلاگ می‌توانید از چنین پرامپتی استفاده کنید:

> A clean modern cybersecurity illustration for a GitHub best practices blog, showing a developer laptop, a Git branch graph, a shield icon, locked API keys, and a secure cloud repository, dark blue and orange color palette, professional tech blog style, minimal and educational.

برای تصویر داخل مقاله:

> A simple educational diagram showing three layers of GitHub security: local developer environment, repository access control, and remote security guardrails, clean vector style, Persian tech blog aesthetic, dark background with orange highlights.

---

## منابع پیشنهادی

- [GitHub Docs — Best practices for preventing data leaks](https://docs.github.com/en/code-security/tutorials/secure-your-organization/best-practices-for-preventing-data-leaks-in-your-organization)
- [GitHub Docs — About push protection](https://docs.github.com/en/code-security/concepts/secret-security/about-push-protection)
- [GitHub Docs — Managing a branch protection rule](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule)
- [GitHub Docs — About commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)
- [GitHub Docs — Configuring two-factor authentication](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication)
- [GitHub Docs — Dependabot alerts](https://docs.github.com/code-security/dependabot/dependabot-alerts/about-dependabot-alerts)
- [Gitleaks GitHub Repository](https://github.com/gitleaks/gitleaks)
- [GitGuardian ggshield Pre-commit Docs](https://docs.gitguardian.com/ggshield-docs/integrations/git-hooks/pre-commit)
