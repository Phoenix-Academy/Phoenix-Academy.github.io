---
title: "چرا هر توسعه‌دهنده رباتیک باید Docker را یاد بگیرد؟"
summary: "Docker در پروژه‌های رباتیکی مشکل وابستگی‌ها، تفاوت سیستم‌ها، اجرای ROS 2 و Gazebo، توسعه تیمی و آماده‌سازی برای استقرار روی ربات واقعی را ساده‌تر می‌کند."
date: 2026-05-31
authors:
  - admin
tags:
  - Docker
  - ROS 2
  - Gazebo
  - Isaac ROS
  - رباتیک
categories:
  - آموزش رباتیک
image:
  filename: 1.jpeg
  caption: "Docker کمک می‌کند محیط توسعه رباتیکی از لپ‌تاپ تا شبیه‌ساز و ربات واقعی قابل تکرار بماند."
  alt_text: "تصویری مفهومی از اجرای ابزارهای رباتیک در محیط کانتینری Docker"
---

در رباتیک، «روی سیستم من کار می‌کند» معمولا آغاز یک دردسر طولانی است. یک پروژه ممکن است به نسخه مشخصی از <bdi dir="ltr">Ubuntu</bdi>، <bdi dir="ltr">ROS 2</bdi>، <bdi dir="ltr">Python</bdi>، <bdi dir="ltr">Gazebo</bdi>، <bdi dir="ltr">CUDA</bdi>، درایور دوربین، کتابخانه‌های پردازش تصویر و حتی تنظیمات شبکه وابسته باشد. کافی است یکی از این قطعات روی لپ‌تاپ عضو دیگر تیم متفاوت نصب شده باشد تا همان کدی که دیروز در شبیه‌ساز اجرا می‌شد، امروز با خطاهای عجیب متوقف شود. <bdi dir="ltr">Docker</bdi> دقیقا برای همین جنس مسئله ارزشمند است: محیط اجرای پروژه را همراه خود پروژه بسته‌بندی می‌کند تا توسعه، تست و انتقال بین سیستم‌ها قابل تکرارتر شود.

این مقاله قرار نیست <bdi dir="ltr">Docker</bdi> را به عنوان یک ابزار صرفا <bdi dir="ltr">DevOps</bdi> معرفی کند. در رباتیک، <bdi dir="ltr">Docker</bdi> یک ابزار عملی برای آرام‌تر کردن آشفتگی وابستگی‌هاست؛ مخصوصا وقتی <bdi dir="ltr">ROS 2</bdi>، <bdi dir="ltr">Gazebo</bdi>، <bdi dir="ltr">Isaac ROS</bdi>، <bdi dir="ltr">GPU</bdi>، سنسورها و چندین سرویس همزمان وارد بازی می‌شوند.

> وقتی محیط توسعه قابل تکرار باشد، انرژی تیم به جای رفع خطای نصب، صرف طراحی nodeها، شبیه‌سازی سناریوها و بهتر کردن رفتار ربات می‌شود.

## <bdi dir="ltr">Docker</bdi> چیست و چرا برای رباتیک مهم است؟

<bdi dir="ltr">Docker</bdi> نرم‌افزار را داخل محیط‌های ایزوله‌ای به نام <bdi dir="ltr">container</bdi> اجرا می‌کند. هر <bdi dir="ltr">container</bdi> از روی یک <bdi dir="ltr">image</bdi> ساخته می‌شود؛ <bdi dir="ltr">image</bdi> را می‌توان مثل یک نسخه آماده از محیط پروژه دید که سیستم‌عامل پایه، ابزارها، کتابخانه‌ها و وابستگی‌های لازم را در خود دارد. تفاوت مهم اینجاست که به جای نصب دستی <bdi dir="ltr">ROS 2</bdi>، <bdi dir="ltr">Gazebo</bdi> و ده‌ها پکیج روی سیستم هر نفر، یک تصویر مشترک ساخته می‌شود و همه اعضای تیم همان را اجرا می‌کنند.

فرض کنید تیمی روی <bdi dir="ltr">ROS 2 Humble</bdi> کار می‌کند. یک نفر <bdi dir="ltr">Ubuntu 22.04</bdi> دارد، نفر دیگر روی <bdi dir="ltr">Windows</bdi> با <bdi dir="ltr">WSL</bdi> توسعه می‌دهد و نفر سوم می‌خواهد بخشی از سیستم را روی <bdi dir="ltr">Jetson</bdi> تست کند. اگر همه چیز دستی نصب شود، اختلاف نسخه‌ها به سرعت خودش را نشان می‌دهد؛ اما با <bdi dir="ltr">Docker</bdi> می‌توان یک محیط مشترک ساخت که <bdi dir="ltr">workspace</bdi>، <bdi dir="ltr">dependency</bdi>ها، ابزارهای <bdi dir="ltr">build</bdi>، نسخه <bdi dir="ltr">ROS</bdi> و حتی ابزارهای شبیه‌سازی در آن مشخص شده‌اند. نتیجه این است که پروژه به جای وابسته بودن به حافظه و تنظیمات شخصی اعضای تیم، به یک فایل قابل بازبینی و قابل اشتراک‌گذاری وابسته می‌شود.

{{< figure src="2.png" caption="یک نگاه مفهومی به جایگاه container در مسیر توسعه: کد پروژه روی سیستم شما می‌ماند، اما build و اجرا در محیط کنترل‌شده Docker انجام می‌شود." >}}

اگر می‌خواهید تصویر کلی را با یک توضیح ویدیویی ببینید، این ویدیو می‌تواند نقطه شروع خوبی باشد: [Docker for Robotics](https://www.youtube.com/watch?v=SAMPOK_lazw). بعد از دیدن آن، بخش‌های بعدی مقاله روشن‌تر می‌شوند، چون می‌بینید مشکل اصلی فقط نصب پکیج نیست؛ مسئله ساختن محیطی است که بارها و روی ماشین‌های مختلف قابل اجرا باشد.

## از <bdi dir="ltr">ROS</bdi> تا <bdi dir="ltr">Gazebo</bdi>؛ <bdi dir="ltr">Docker</bdi> چه مشکلی را حل می‌کند؟

<bdi dir="ltr">ROS 2</bdi> معمولا از چندین <bdi dir="ltr">node</bdi> تشکیل می‌شود که با <bdi dir="ltr">topic</bdi>، <bdi dir="ltr">service</bdi>، <bdi dir="ltr">action</bdi> و <bdi dir="ltr">DDS</bdi> با هم ارتباط می‌گیرند. در کنار آن، <bdi dir="ltr">Gazebo</bdi> یا ابزارهای <bdi dir="ltr">visualization</bdi> مثل <bdi dir="ltr">RViz</bdi> هم وارد مسیر می‌شوند. حالا اگر <bdi dir="ltr">perception</bdi>، <bdi dir="ltr">navigation</bdi>، <bdi dir="ltr">simulation</bdi> و <bdi dir="ltr">logging</bdi> هرکدام <bdi dir="ltr">dependency</bdi>های متفاوتی داشته باشند، نگه داشتن یک سیستم تمیز و پایدار سخت می‌شود. <bdi dir="ltr">Docker</bdi> کمک می‌کند این بخش‌ها را به شکل روشن‌تری جدا کنیم و برای هر بخش محیط مناسب خودش را بسازیم.

در یک سناریوی ساده، می‌توان یک <bdi dir="ltr">container</bdi> برای <bdi dir="ltr">workspace</bdi> اصلی <bdi dir="ltr">ROS 2</bdi> داشت؛ کد پروژه با <bdi dir="ltr">volume</bdi> از سیستم میزبان به <bdi dir="ltr">container</bdi> وصل می‌شود، اما <bdi dir="ltr">build</bdi> و <bdi dir="ltr">run</bdi> داخل <bdi dir="ltr">container</bdi> انجام می‌گیرد. در سناریوهای حرفه‌ای‌تر، <bdi dir="ltr">Docker Compose</bdi> وارد می‌شود و چند <bdi dir="ltr">container</bdi> را همزمان بالا می‌آورد: یکی برای <bdi dir="ltr">simulation</bdi>، یکی برای <bdi dir="ltr">perception</bdi>، یکی برای <bdi dir="ltr">navigation</bdi> و یکی برای ابزارهای جانبی. این الگو باعث می‌شود معماری نرم‌افزار به معماری واقعی ربات نزدیک‌تر شود، چون هر بخش مرز و مسئولیت مشخص‌تری پیدا می‌کند.

> <bdi dir="ltr">Docker</bdi> جای فهمیدن <bdi dir="ltr">ROS 2</bdi>، شبکه و لینوکس را نمی‌گیرد؛ اما کاری می‌کند این مفاهیم در یک محیط قابل کنترل تمرین و اجرا شوند.

## <bdi dir="ltr">Gazebo</bdi> و ابزارهای گرافیکی داخل <bdi dir="ltr">container</bdi>

یکی از تفاوت‌های مهم رباتیک با بسیاری از پروژه‌های نرم‌افزاری این است که معمولا فقط با <bdi dir="ltr">terminal</bdi> سروکار نداریم. برای توسعه ربات باید شبیه‌ساز، <bdi dir="ltr">visualization</bdi>، دوربین مجازی، <bdi dir="ltr">lidar</bdi>، نقشه و حرکت ربات را ببینیم. اجرای ابزارهایی مثل <bdi dir="ltr">Gazebo</bdi> و <bdi dir="ltr">RViz</bdi> داخل <bdi dir="ltr">container</bdi> ممکن است، اما به تنظیمات بیشتری نیاز دارد؛ مثلا دسترسی به <bdi dir="ltr">display</bdi> سیستم میزبان از طریق <bdi dir="ltr">X11</bdi> یا <bdi dir="ltr">Wayland</bdi>، تنظیم <bdi dir="ltr">permission</bdi>ها، و در بعضی حالت‌ها استفاده از <bdi dir="ltr">VNC</bdi> یا راهکارهای <bdi dir="ltr">remote visualization</bdi>.

برای پروژه‌های سبک‌تر، اجرای <bdi dir="ltr">GUI</bdi> از طریق <bdi dir="ltr">X11</bdi> روی لینوکس کافی است. برای پروژه‌های سنگین‌تر، مخصوصا زمانی که <bdi dir="ltr">GPU</bdi> یا <bdi dir="ltr">simulation</bdi> سه‌بعدی جدی داریم، باید دسترسی <bdi dir="ltr">container</bdi> به <bdi dir="ltr">GPU</bdi> هم درست تنظیم شود. اینجاست که تفاوت بین «<bdi dir="ltr">Docker</bdi> را نصب کرده‌ام» و «<bdi dir="ltr">Docker</bdi> را برای رباتیک درست استفاده می‌کنم» خودش را نشان می‌دهد.

{{< figure src="3.jpeg" caption="در پروژه‌های رباتیکی، container فقط محیط build نیست؛ گاهی باید به شبیه‌ساز، GPU، شبکه و ابزارهای visualization هم متصل شود." >}}

## <bdi dir="ltr">Isaac ROS</bdi> و مسیر حرفه‌ای‌تر توسعه

در پروژه‌هایی که پردازش تصویر، شتاب‌دهی <bdi dir="ltr">GPU</bdi>، <bdi dir="ltr">depth camera</bdi> یا <bdi dir="ltr">inference</bdi> بلادرنگ جدی است، اکوسیستم <bdi dir="ltr">NVIDIA Isaac ROS</bdi> نمونه خوبی از یک <bdi dir="ltr">workflow</bdi> کانتینری است. در این سبک توسعه، محیط <bdi dir="ltr">build</bdi> و ابزارها داخل <bdi dir="ltr">container</bdi> نگه داشته می‌شوند تا <bdi dir="ltr">workspace</bdi> در شرایط <bdi dir="ltr">consistent</bdi> و <bdi dir="ltr">reproducible</bdi> ساخته شود. این کار به‌خصوص برای تیم‌هایی مهم است که بین شبیه‌سازی، تست روی <bdi dir="ltr">workstation</bdi> و اجرای نهایی روی <bdi dir="ltr">Jetson</bdi> جابه‌جا می‌شوند.

برای تکمیل این بخش، این ویدیو هم می‌تواند نگاه عملی‌تری بدهد: [Docker and Robotics Workflow](https://www.youtube.com/watch?v=kxAF-Pye8rI). بهتر است آن را بعد از آشنایی اولیه با مفهوم <bdi dir="ltr">image</bdi> و <bdi dir="ltr">container</bdi> ببینید، چون تمرکز اصلی در این مرحله روی <bdi dir="ltr">workflow</bdi> توسعه و انتقال محیط است.

## از کجا شروع کنیم؟

برای شروع، لازم نیست همان ابتدا یک معماری پیچیده چند-<bdi dir="ltr">container</bdi> بسازید. مسیر منطقی این است که اول یک <bdi dir="ltr">image</bdi> آماده <bdi dir="ltr">ROS 2</bdi> را اجرا کنید، بعد <bdi dir="ltr">workspace</bdi> خودتان را با <bdi dir="ltr">volume</bdi> به <bdi dir="ltr">container</bdi> وصل کنید، سپس یک پکیج ساده را <bdi dir="ltr">build</bdi> و <bdi dir="ltr">run</bdi> بگیرید. وقتی این مرحله پایدار شد، اجرای <bdi dir="ltr">Gazebo</bdi> یا <bdi dir="ltr">RViz</bdi> را اضافه کنید و بعد سراغ <bdi dir="ltr">Dockerfile</bdi> اختصاصی پروژه بروید. <bdi dir="ltr">Dockerfile</bdi> به شما اجازه می‌دهد نصب <bdi dir="ltr">dependency</bdi>ها و تنظیمات محیط را به جای یک راهنمای دستی طولانی، در قالب دستورهای روشن و قابل بازبینی نگه دارید.

در مرحله بعد می‌توانید <bdi dir="ltr">Docker Compose</bdi> را وارد کنید. مثلا <bdi dir="ltr">simulation</bdi>، <bdi dir="ltr">navigation</bdi> و <bdi dir="ltr">perception</bdi> را در سرویس‌های جدا بالا بیاورید و ارتباط آن‌ها را از طریق شبکه <bdi dir="ltr">Docker</bdi> و تنظیمات <bdi dir="ltr">ROS 2</bdi> بررسی کنید. این مرحله برای یادگیری معماری سیستم هم عالی است، چون خیلی سریع مشخص می‌شود کدام <bdi dir="ltr">node</bdi> به کدام سرویس، <bdi dir="ltr">topic</bdi>، <bdi dir="ltr">sensor</bdi> یا <bdi dir="ltr">permission</bdi> وابسته است.

> بهترین تمرین این است که یک پروژه کوچک <bdi dir="ltr">ROS 2</bdi> را انتخاب کنید، یک <bdi dir="ltr">Dockerfile</bdi> برای آن بسازید و از خودتان بپرسید: آیا عضو دیگری از تیم می‌تواند فقط با <bdi dir="ltr">clone</bdi> کردن <bdi dir="ltr">repo</bdi> و اجرای یک دستور، همان محیط را بالا بیاورد؟

## اشتباهات رایج هنگام استفاده از <bdi dir="ltr">Docker</bdi> در رباتیک

اولین اشتباه، فراموش کردن <bdi dir="ltr">GPU</bdi> است. اگر پروژه شما <bdi dir="ltr">Gazebo</bdi> سنگین، پردازش تصویر، <bdi dir="ltr">Isaac ROS</bdi> یا <bdi dir="ltr">inference</bdi> دارد، <bdi dir="ltr">container</bdi> باید به <bdi dir="ltr">GPU</bdi> دسترسی داشته باشد و نسخه‌های <bdi dir="ltr">CUDA</bdi>، <bdi dir="ltr">driver</bdi> و <bdi dir="ltr">runtime</bdi> با هم سازگار باشند. بدون این هماهنگی، ممکن است کد درست باشد اما اجرا کند، ناپایدار یا کاملا ناموفق شود.

دومین اشتباه، بی‌توجهی به سخت‌افزار واقعی است. روی ربات واقعی، <bdi dir="ltr">container</bdi> ممکن است به <bdi dir="ltr">camera</bdi>، <bdi dir="ltr">LiDAR</bdi>، <bdi dir="ltr">joystick</bdi>، <bdi dir="ltr">serial port</bdi>، <bdi dir="ltr">USB</bdi> یا شبکه داخلی ربات نیاز داشته باشد. پس باید از همان ابتدا به <bdi dir="ltr">permission</bdi>ها، <bdi dir="ltr">device mapping</bdi> و امنیت دسترسی‌ها فکر کرد. <bdi dir="ltr">Docker</bdi> می‌تواند محیط را تمیزتر کند، اما اگر بدون دقت به سخت‌افزار وصل شود، عیب‌یابی را سخت‌تر می‌کند.

سومین اشتباه، ساده گرفتن شبکه در <bdi dir="ltr">ROS 2</bdi> است. <bdi dir="ltr">ROS 2</bdi> از <bdi dir="ltr">DDS</bdi> استفاده می‌کند و <bdi dir="ltr">discovery</bdi> بین <bdi dir="ltr">node</bdi>ها به تنظیمات شبکه حساس است. وقتی <bdi dir="ltr">node</bdi>ها در <bdi dir="ltr">container</bdi>های جدا یا روی ماشین‌های مختلف اجرا می‌شوند، باید <bdi dir="ltr">network mode</bdi>، <bdi dir="ltr">domain id</bdi>، <bdi dir="ltr">multicast</bdi> و <bdi dir="ltr">interface</bdi>های شبکه را با دقت بررسی کرد. خیلی وقت‌ها مشکل از کد نیست؛ <bdi dir="ltr">node</bdi>ها فقط همدیگر را پیدا نمی‌کنند.

## جمع‌بندی

<bdi dir="ltr">Docker</bdi> برای توسعه‌دهنده رباتیک یک میانبر جادویی نیست، اما یکی از بهترین ابزارها برای ساختن محیط‌های قابل تکرار، قابل اشتراک‌گذاری و نزدیک‌تر به <bdi dir="ltr">deployment</bdi> واقعی است. اگر با <bdi dir="ltr">ROS 2</bdi>، <bdi dir="ltr">Gazebo</bdi> یا <bdi dir="ltr">Isaac ROS</bdi> کار می‌کنید، یاد گرفتن <bdi dir="ltr">Docker</bdi> باعث می‌شود پروژه‌هایتان کمتر به تنظیمات اتفاقی سیستم‌ها وابسته باشند و تیم بتواند با اعتماد بیشتری بین توسعه، شبیه‌سازی و اجرای واقعی حرکت کند.

از یک <bdi dir="ltr">container</bdi> ساده شروع کنید، بعد <bdi dir="ltr">workspace</bdi> را <bdi dir="ltr">mount</bdi> کنید، سپس <bdi dir="ltr">GUI</bdi> و <bdi dir="ltr">GPU</bdi> را اضافه کنید و در نهایت به <bdi dir="ltr">Docker Compose</bdi> برسید. این مسیر قدم‌به‌قدم هم برای یادگیری <bdi dir="ltr">Docker</bdi> مناسب است و هم برای فهمیدن معماری واقعی سیستم‌های رباتیکی.

## منابع

- [Docker Docs: Develop with Docker and ROS 2](https://docs.docker.com/guides/ros2/)
- [Open Robotics Discourse: Docker and Robotics tutorial series](https://discourse.openrobotics.org/t/new-tutorial-series-docker-and-robotics/32174)
- [NVIDIA Isaac ROS documentation](https://nvidia-isaac-ros.github.io/)
- [YouTube: Docker for Robotics](https://www.youtube.com/watch?v=SAMPOK_lazw)
- [YouTube: Docker and Robotics Workflow](https://www.youtube.com/watch?v=kxAF-Pye8rI)
