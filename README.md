<div align="center">
  <strong><a href="README-EN.md">English</a></strong> | <strong><a href="README.md">فارسی</a></strong> | <strong><a href="README-CH.md">中文</a></strong>
</div>
<br>

# NetDash — Network traffic monitoring and control dashboard
A lightweight, database-free, fully automated panel for **live network traffic monitoring, bandwidth throttling, domain/IP blocking, ping monitoring** and much more. Suitable for **VPN servers, home/corporate routers, offices and small teams**.

---
<img width="1256" height="616" alt="Screenshot (165)" src="https://github.com/user-attachments/assets/0ed26721-bd57-4613-926d-9666bbc4a912" />

## ✨ قابلیت‌ها

- **داشبورد لحظه‌ای شبکه:** نمایش نرخ دانلود/آپلود هر اینترفیس + نمودارهای زنده
- **مسدودسازی دامنه/IP/CIDR:** با یک کلیک؛ همراه **صفحهٔ هشدار کاربر** (HTTP 451) در حالت Page Mode
- **محدودیت سرعت (Shaping):** اعمال سقف سرعت آپلود/دانلود برای هر اینترفیس از داخل پنل
- **گزارش‌های روزانه/ماهانه:** حجم مصرفی تفکیک‌شده به تفکیک اینترفیس (Traffic usage)
- **کنترل اینترفیس‌ها:** روشن/خاموش‌کردن کارت شبکه از داخل پنل
- **پایش پینگ و کیفیت اینترنت:** محاسبهٔ میانگین، بیشینه، پرسنـتایل ۹۵٪ و نرخ Packet Loss
- **نمایش ترافیک زنده بر اساس پورت:** از طریق conntrack (کرنل)
- **رابط فارسی + مد تیره/روشن، موبایل‌فرندلی**
- **بازسازی خودکار قوانین بعد از ری‌استارت** (iptables/ipset/dnsmasq)
- **بدون دیتابیس**، نصب ساده روی لینوکس

---

## 🚀 نصب سریع (یک‌خطی)

اسکریپت نصب تمام پیش‌نیازها را آماده، سرویس‌ها را تنظیم و برنامه را اجرا می‌کند:

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/Free-Guy-IR/Interface-Traffic-Monitoring/main/netdash-install.sh)
```

> اسکریپت در صورت نیاز **systemd-resolved** را غیرفعال و `/etc/resolv.conf` را به `127.0.0.1` تنظیم می‌کند و `dnsmasq` را راه‌اندازی می‌کند. قوانین فایروال توسط خود برنامه مدیریت می‌شود.

پس از نصب، پنل روی این آدرس در دسترس است:

```
http://SERVER_IP:18080/
```

---

## 🔧 منوی مدیریت (اسکریپت)
اسکریپت نصب یک منوی متنی ارائه می‌کند:
1. **Install / Reinstall** – پیش‌نیازها + دانلود کد + ساخت سرویس systemd
2. **Update from GitHub** – بروز‌رسانی سورس برنامه
3. **Edit .env** – ویرایش تنظیمات محیطی در `/etc/netdash.env`
4. **View Live Logs** – مشاهدهٔ لاگ زندهٔ سرویس
5. **Stop** / **6. Start** – مدیریت سرویس
7. **Remove Completely** – حذف کامل سرویس و فایل‌ها
8. **Status** – وضعیت سرویس

---

## ⚙️ تنظیمات محیطی (ENV)

فایل تنظیمات در مسیر `/etc/netdash.env` ذخیره می‌شود. مهم‌ترین متغیرها:

| متغیر | پیش‌فرض | توضیح |
|---|---|---|
| `NETDASH_PORT` | `18080` | پورت پنل وب |
| `NETDASH_BLOCK_PORT` | `18081` | پورت صفحهٔ هشدار HTTP |
| `NETDASH_TOKEN` | خالی | اگر ست شود، باید هدر `X-Auth-Token` برای عملیات مدیریتی ارسال شود |
| `NETDASH_MAX_POINTS` | `120` | حداکثر نقاط هر نمودار |
| `NETDASH_IPSET_MODE` | `1` | استفاده از `dnsmasq + ipset` برای بلاک دامنه‌ها |
| `NETDASH_SNI_BLOCK` | `1` | افزودن قوانین SNI برای TLS/443 |
| `NETDASH_PAGE_MODE` | `1` | ریدایرکت HTTP به صفحهٔ مسدودسازی (پورت Block) |
| `NETDASH_SNI_LEARN` | `1` | یادگیری IPهای مقصد از SNI و افزودن به ipset |
| `NETDASH_SNI_IFACES` | خالی | لیست اینترفیس‌ها برای SNI Learner، جدا با کاما (اختیاری) |
| `NETDASH_ENFORCE_DNS` | `1` | ریدایرکت DNS کلاینت‌ها به DNS لوکال (iptables nat) |
| `NETDASH_BLOCK_DOT` | `0` | بلاک DoT/DoQ (پورت‌های 853/TCP و 8853/UDP) |
| `NETDASH_PRELOAD_META` | `0` | پیش‌بارگذاری چند دامنهٔ پیش‌فرض برای بلاک |
| `NETDASH_AUTO_PIP` | `1` | نصب خودکار `scapy` در صورت نیاز |
| `NETDASH_PORTS_MONITOR` | `1` | فعال‌سازی مانیتور پورت‌ها (conntrack) |
| `NETDASH_PORTS_INTERVAL` | `1.0` | بازهٔ نمونه‌برداری مانیتور پورت‌ها (ثانیه) |
| `NETDASH_PING_TARGETS` | `1.1.1.1,8.8.8.8,9.9.9.9` | لیست مقصدهای پینگ |
| `NETDASH_PING_INTERVAL` | `5.0` | فاصلهٔ پینگ‌ها (ثانیه) |
| `NETDASH_PING_WINDOW` | `50` | اندازهٔ پنجرهٔ محاسبهٔ آمار پینگ |

> فهرست کامل متغیرها در کد منبع `netdash.py` موجود است.

---

## 🗂️ فایل‌ها و پِرسیستنس

NetDashSelects a data path in order of priority (first writable path):
1. `/var/lib/netdash/`
2. `~/.local/share/netdash/`
3. `/tmp/netdash/`

فایل‌های داده‌ای در این مسیر ایجاد می‌شوند:
- `history.json` — تاریخچهٔ لحظه‌ای نرخ دانلود/آپلود (برای نمودارها)
- `totals.json` — مجموع ترافیک هر اینترفیس + آخرین شمارنده‌های سیستمی
- `period_totals.json` — تجمیع روزانه/ماهانهٔ دانلود/آپلود به تفکیک اینترفیس
- `filters.json` — اقلام بلاک‌لیست و وضعیت اعمال‌شده آن‌ها
- `blocks_registry.json` — ایندکس جانبی برای نمایش/بازسازی بلوک‌ها
- `sni-seen.log` — لاگ رویدادهای SNI مشاهده‌شده
- `sni-index.json` — ایندکس دامنه→IPهای مشاهده‌شده (برای preseed)
- `ports_totals.json` — مجموع دانلود/آپلود بر اساس پورت (conntrack)

همچنین فایل پیکربندی dnsmasq در مسیر زیر ساخته/بازسازی می‌شود:
- `/etc/dnsmasq.d/netdash-blocks.conf` — اتصال دامنه‌ها به ipsetها

---

## 🔐 نکات امنیتی
- برای درخواست‌های تغییردهنده (افزودن/حذف فیلترها، اعمال محدودیت و…)، **توکن** تنظیم کنید و از هدر `X-Auth-Token: <TOKEN>` استفاده کنید.
- پنل را پشت فایروال یا فقط روی لوکال‌نت در دسترس بگذارید و در صورت نیاز **Reverse Proxy + TLS** قرار دهید.

---

## 🧰 دستورات سرویس

```bash
# وضعیت/لاگ
sudo systemctl status netdash
sudo journalctl -u netdash -f

# شروع/توقف/ری‌استارت
sudo systemctl start netdash
sudo systemctl stop netdash
sudo systemctl restart netdash
```

---

## 🩺    Troubleshooting

- **dnsmasq روی پورت 53 بالا نمی‌آید (port in use):**‌
  ```bash
  sudo systemctl disable --now systemd-resolved
  echo 'nameserver 127.0.0.1' | sudo tee /etc/resolv.conf
  sudo systemctl enable --now dnsmasq
  sudo systemctl restart dnsmasq && systemctl status dnsmasq
  ```
- **قوانین iptables اعمال نمی‌شود:** مطمئن شوید کاربر اجازهٔ sudo بدون پسورد برای دستورات شبکه را دارد یا برنامه را با کاربر روت اجرا کنید.
- **لاگ سرویس را ببینید:** `journalctl -u netdash -f`
- **پورت پنل باز نیست:** فایروال سیستم یا Cloud/Provider را بررسی کنید (پورت پیش‌فرض 18080).

---

## 👨‍💻 توسعه

- کد اصلی: [`netdash.py`](https://github.com/Free-Guy-IR/Interface-Traffic-Monitoring/blob/main/netdash.py)
- اسکریپت نصب: [`netdash-install.sh`](https://github.com/Free-Guy-IR/Interface-Traffic-Monitoring/blob/main/netdash-install.sh)

پیشنهاد/باگ‌ریپورت‌ها را در Issues ثبت کنید 🌟

---

## 💖 حمایت
اگر NetDash برایتان مفید بود، لطفاً با ⭐️ دادن به مخزن از پروژه حمایت کنید.
