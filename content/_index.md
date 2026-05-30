---
title: "Home"
summary: ""
date: "2026-01-17"
type: "landing"
design:
  spacing: "6rem"
sections:
  - block: "hero"
    content:
      title: "آکادمی ققنوس"
      text: "تجلی نوزایی و بالندگی علمی بر مدار اندیشه و اراده راسخ جوانان ایران‌زمین" 
      primary_action:
        text: "شروع"
        url: "/blog/"
        icon: "rocket-launch"
      secondary_action:
        text: "نقشه راه"
        url: "#roadmap"
        icon: "arrow-path-rounded-square"
    design:
      background:
        image:
          filename: bg.png
          size: contain
          position: center center
          repeat: no-repeat
          parallax: false
          filters:
            brightness: 0.82
            contrast: 1.05
        color: "#0b1320"
      css_class: "phoenix-hero-right"
    ce: "section-2e010377"
    As: "section-954c71ed"

  - block: "markdown"
    content:
      text: |
        <div style="width: min(1040px, calc(100vw - 2rem)); max-width: none; margin-right: calc(50% - min(520px, calc(50vw - 1rem))); margin-left: calc(50% - min(520px, calc(50vw - 1rem))); padding: 1rem 0 0.5rem; overflow-x: auto;">
          <div style="display: grid; grid-template-columns: repeat(3, minmax(210px, 1fr)); gap: 1.5rem; align-items: start; min-width: 690px;">
            <div style="width: 100%; text-align: center;">
              <img src="/uploads/robot-arm.png" alt="رباتیک و بازوهای هوشمند" style="width: 100%; height: 190px; object-fit: cover; border-radius: 16px; box-shadow: 0 16px 40px rgba(36, 52, 87, 0.12);">
              <h3 style="margin: 0.9rem 0 0; font-size: 1.05rem; font-weight: 700; color: #0f172a;">رباتیک</h3>
            </div>
            <div style="width: 100%; text-align: center;">
              <img src="/uploads/uav.png" alt="پهپادهای خودران" style="width: 100%; height: 190px; object-fit: cover; border-radius: 16px; box-shadow: 0 16px 40px rgba(15, 23, 42, 0.12);">
              <h3 style="margin: 0.9rem 0 0; font-size: 1.05rem; font-weight: 700; color: #0f172a;">پهپادهای خودران</h3>
            </div>
            <div style="width: 100%; text-align: center;">
              <img src="/uploads/AI.jpeg" alt="هوش مصنوعی و یادگیری ماشین" style="width: 100%; height: 190px; object-fit: cover; background: #ffffff; border-radius: 16px; box-shadow: 0 16px 40px rgba(15, 23, 42, 0.12);">
              <h3 style="margin: 0.9rem 0 0; font-size: 1.05rem; font-weight: 700; color: #0f172a;">هوش مصنوعی</h3>
            </div>
          </div>
        </div>
    design:
      background:
        color: "#f1f1f1"
    ce: "section-showcase"
   
  - block: "markdown"
    content:
      title: "خوش آمدید"
      text: |
        به آکادمی ققنوس خوش آمدید. اینجا مسیر پژوهش تا پیاده‌سازی را دنبال می‌کنیم؛ از ایده و شبیه‌سازی تا آزمایش، مستندسازی و انتشار.
    design:
      background:
        color: "#ffffff"
    ce: "section-welcome"
  - block: "cta-image-paragraph"
    content:
      items:
        - title: "درباره ما"
          text: |
           آکادمی ققنوس؛ پیشران آموزش فناوری‌های پیشرفته روز به نسل جوان. ما با تمرکز بر هوش مصنوعی، رباتیک و پهپادهای خودران، مأموریت خود را آغاز کرده‌ایم تا به پشتوانه نوآوری جوانان ایرانی، از مرزهای اقتصاد سنتی فراتر رفته و دریچه‌هایی نو به سوی اقتصاد خلاق و آینده‌ساز بگشاییم.
          feature_icon: "check"
          features:
            - "توسعه آزمایشگاه‌محور و مستندسازی شفاف"
            - "تلفیق پژوهش، شبیه‌سازی و نمونه‌سازی سریع"
            - "بسترسازی تخصصی در جهت شکل‌گیری زیست‌بوم کارآفرینی برای نسل جوان خلاق"
          button:
            text: "مطالعه مقالات"
            url: "/blog/"
    design:
      background:
        color: "#f5f7fa"
      css_class: "dark:bg-gray-900"
    ce: "section-about"
    id: "about"
    As: "section-4647dcfd"
  - block: "markdown"
    content:
      title: "نقشه راه پژوهش"
      text: |
        <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:1.25rem;">
          <div><strong>1. مبانی</strong><br>Linux، Python و ابزارهای توسعه.</div>
          <div><strong>2. رباتیک</strong><br>ROS 2، معماری نرم‌افزار و ارتباط میان اجزا.</div>
          <div><strong>3. شبیه‌سازی</strong><br>Gazebo، آزمایش سناریو و اعتبارسنجی رفتار.</div>
          <div><strong>4. پهپاد</strong><br>PX4، ناوبری، کنترل و تله‌متری.</div>
          <div><strong>5. عامل‌محور</strong><br>Agentic AI، هماهنگی چندعامل و تصمیم‌گیری.</div>
          <div><strong>6. انتشار</strong><br>مقاله، پروژه، دمو و مستندسازی قابل ارائه.</div>
        </div>
    design:
      background:
        color: "#d5862cc8"
    ce: "section-roadmap"
    id: "roadmap"
  - block: "features"
    content:
      title: "محورهای اصلی"
      text: "چهار مسیر اصلی آکادمی ققنوس برای پژوهش، آزمایش و انتشار دانش"
      items:
        - name: "رباتیک و ROS 2"
          icon: "magnifying-glass"
          description: "طراحی گره‌ها، ارتباطات و معماری نرم‌افزار برای سیستم‌های رباتیکی."
        - name: "پهپاد و PX4"
          icon: "bolt"
          description: "کنترل، تله‌متری، پرواز خودکار و یکپارچه‌سازی با سناریوهای واقعی."
        - name: "شبیه‌سازی و دوقلوی دیجیتال"
          icon: "sparkles"
          description: "اعتبارسنجی رفتار سیستم پیش از استقرار و کاهش ریسک آزمون میدانی."
        - name: "Agentic AI و چندعامل"
          icon: "code-bracket"
          description: "طراحی جریان‌های هوشمند برای تصمیم‌گیری، برنامه‌ریزی و همکاری عامل‌ها."
        - name: "مقالات و آموزش‌ها"
          icon: "star"
          description: "تبدیل تجربه‌های آزمایشگاهی به مقاله، آموزش و راهنمای عملی."
        - name: "مدیریت دانش"
          icon: "rectangle-group"
          description: "ساختاردهی محتوا، برچسب‌ها و مسیر یادگیری برای استفاده‌ی دوباره."
    ce: "section-features"
    id: "features"
    As: "section-835d75b8"    
  - block: "markdown"
    content:
      title: ""
      text: |
        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 3rem; padding: 4rem 2rem;">
          <div>
            <h3 style="color: #42c6ff; margin-bottom: 1.5rem; font-size: 1.1rem; font-weight: 600;">دسترسی سریع</h3>
            <ul style="list-style: none; padding: 0; margin: 0;">
              <li style="margin-bottom: 0.75rem;"><a href="/" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">خانه</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="#roadmap" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">نقشه راه</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="/blog/" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">مقالات</a></li>
              <li><a href="#about" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">درباره</a></li>
            </ul>
          </div>
          <div>
            <h3 style="color: #42c6ff; margin-bottom: 1.5rem; font-size: 1.1rem; font-weight: 600;">منابع</h3>
            <ul style="list-style: none; padding: 0; margin: 0;">
              <li style="margin-bottom: 0.75rem;"><a href="/blog/" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">مستندات</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="/blog/" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">مطالعات موردی</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">مرجع فنی</a></li>
              <li><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">پشتیبانی</a></li>
            </ul>
          </div>
          <div>
            <h3 style="color: #42c6ff; margin-bottom: 1.5rem; font-size: 1.1rem; font-weight: 600;">حقوقی</h3>
            <ul style="list-style: none; padding: 0; margin: 0;">
              <li style="margin-bottom: 0.75rem;"><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">حریم خصوصی</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">شرایط استفاده</a></li>
              <li style="margin-bottom: 0.75rem;"><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">سیاست کوکی</a></li>
              <li><a href="#" style="color: #ffffff; text-decoration: none; transition: color 0.3s;">انطباق</a></li>
            </ul>
          </div>
        </div>
        <div style="border-top: 1px solid rgba(66, 198, 255, 0.3); margin-top: 3rem; padding-top: 2rem; text-align: center; color: rgba(255, 255, 255, 0.8); font-size: 0.9rem;">
          <p style="margin: 0;">© 2026 آکادمی ققنوس. همه حقوق محفوظ است | <a href="#" style="color: #42c6ff; text-decoration: none;">حریم خصوصی</a> | <a href="#" style="color: #42c6ff; text-decoration: none;">شرایط</a></p>
        </div>
    design:
      background:
        color: "#0b1f4d"
      spacing:
        padding:
          - "2rem"
          - "0"
          - "2rem"
          - "0"
    ce: "section-79a197c2"
    As: "section-ccd683ad"
---
