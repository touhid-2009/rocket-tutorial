# پروژه Rocket

## 📋 مقدمه

**راکت** یک پروژه پیشرفته برای ایجاد تانل‌های امن و پرسرعت است که از پروتکل‌های مختلف شبکه پشتیبانی می‌کند. این پروژه با استفاده از زبان Go نوشته شده و قابلیت‌های منحصر به فردی برای انتقال داده‌ها ارائه می‌دهد.

## ✨ ویژگی‌های کلیدی

### 🌐 **پروتکل‌های پشتیبانی شده**
- **TCPMUX**
- **WSMUX**

### 🚀 **عملکرد بالا**
- **مدیریت حافظه بهینه**: استفاده از buffer pools
- **مدیریت اتصالات همزمان**: پشتیبانی از هزاران اتصال
- **فشرده‌سازی داده**: پشتیبانی از LZ4
- **رمزنگاری**: XOR encryption

### 🔒 **امنیت و پایداری**
- **احراز هویت مبتنی بر توکن**: امنیت بالا
- **اتصالات پایدار**: Keep-alive و retry mechanism
- **مدیریت خطا**: تشخیص و بازیابی خودکار

## 🎯 **کاربردها**

### **تانل‌سازی شبکه**
- انتقال امن داده‌ها از طریق اینترنت
- دور زدن محدودیت‌های شبکه
- ایجاد VPN شخصی

### **پروکسی و لود بالانسر**
- توزیع بار بین سرورها
- پروکسی معکوس
- مدیریت اتصالات

## 🛠 **نصب و راه‌اندازی**

```bash
# Running server
./rocket server -c config.toml

# Running client
./rocket client -c config.toml
```

## ⚙️ **پیکربندی سرور**

### **تنظیمات کلی سرور**

```toml
[server] # Local server settings (Iran)
bind_addr = ":4040"                       # Address and port for the server to listen on (mandatory).
bind_addrs = [":4040", ":4041"]           # Multiple addresses and ports to listen on.
transport = "tcpmux"                      # Transport protocol to use ("tcpmux", "wsmux") (mandatory).
default_token = "musix"                   # Default authentication token for secure communication.
log_level = "info"                        # Log level ("panic", "fatal", "error", "warn", "info", "debug", "trace") (optional, default: "info").
so_rcvbuf = 4194304                       # Socket receive buffer size in bytes (default 0, determined by kernel).
so_sndbuf = 1094304                       # Socket send buffer size in bytes (default 0, determined by kernel).
mss = 1340                                # Maximum segment size for TCP connections (default 0, determined by kernel).
keep_alive = 20                           # Interval in seconds to send keep-alive packets for tunnel connections (optional, default: 20s).
disable_nodelay = false                   # Disable TCP_NODELAY (optional, default: false).

default_disable_nodelay = false           # Default TCP_NODELAY setting for local services.
default_keep_alive = 20                   # Default keep-alive interval for local services (seconds).
default_mux_concurrency = 16              # Default maximum number of multiplexed streams per connection.
default_mux_version = 2                   # Default mux protocol version.
default_mux_framesize = 32768             # Default mux frame size in bytes (max: 65535).
default_mux_recievebuffer = 4194304       # Default receive buffer size for mux sessions in bytes.
default_mux_streambuffer = 2097152        # Default buffer size per mux stream in bytes (max: 2147483647). Must not be larger than the receive buffer.
```

### **تنظیمات سرویس سرور**

```toml
[server.services.web] # "web" is your service name
bind_addr = "0.0.0:2057" # Address and port for your service to bind on and to expose the tunneled service.
token = "musix"                           # Authentication token (mandatory if default_token is not set).
disable_nodelay = false                   # Disable TCP_NODELAY for this service.
mux_concurrency = 16                      # Maximum number of concurrent streams for this service.
mux_version = 2                           # Mux protocol version for this service.
mux_framesize = 32768                     # Frame size for mux packets in bytes (default: 32768).
mux_recievebuffer = 4194304               # Receive buffer size for mux sessions in bytes (default: 4194304).
mux_streambuffer = 2000000                # Buffer size per mux stream in bytes (default: 2000000).
keep_alive = 20                           # Keep-alive interval for local connections (seconds).
```
### **توضیحات کامل آپشن‌های سرور**

#### **تنظیمات اتصال**
- **`bind_addr`**: آدرس و پورت اصلی که سرور روی آن گوش می‌دهد
- **`bind_addrs`**: لیست چندین آدرس و پورت برای گوش دادن همزمان
- **`transport`**: نوع پروتکل انتقال (tcpmux یا wsmux)

#### **تنظیمات امنیت**
- **`default_token`**: توکن پیش‌فرض برای احراز هویت تمام سرویس‌ها
- **`token`**: توکن اختصاصی برای هر سرویس (اولویت بالاتر از default_token)

#### **تنظیمات شبکه**
- **`so_rcvbuf`**: اندازه بافر دریافت سوکت (برای بهینه‌سازی عملکرد)
- **`so_sndbuf`**: اندازه بافر ارسال سوکت (برای بهینه‌سازی عملکرد)
- **`mss`**: حداکثر اندازه سگمنت TCP
- **`keep_alive`**: فاصله زمانی ارسال بسته‌های keep-alive
- **`disable_nodelay`**: غیرفعال کردن الگوریتم Nagle

#### **تنظیمات SMUX (Stream Multiplexing)**
- **`mux_concurrency`**: حداکثر تعداد استریم‌های همزمان در هر اتصال
- **`mux_version`**: نسخه پروتکل SMUX (1 یا 2)
- **`mux_framesize`**: اندازه فریم‌های SMUX (حداکثر 65535 بایت)
- **`mux_recievebuffer`**: اندازه بافر دریافت جلسات SMUX
- **`mux_streambuffer`**: اندازه بافر هر استریم SMUX (نباید بزرگتر از receive buffer باشد)

#### **تنظیمات لاگ**
- **`log_level`**: سطح جزئیات لاگ‌ها (panic, fatal, error, warn, info, debug, trace)

## ⚙️ **پیکربندی کلاینت**

### **تنظیمات کلی کلاینت**

```toml
[client] # Client settings
remote_addr = "127.0.0.1:4040"                 # Remote server address and port to connect to (mandatory).
remote_addrs = ["127.0.0.1:4040", "127.0.0.1:4041"] # List of remote server addresses for failover or load balancing.
local_addrs = ["127.0.0.1:0"]                  # Local addresses to bind outgoing connections (optional).
edge_ip = "127.0.0.2"                           # Specific local IP to use for outgoing wsmux connections (optional).
transport = "tcpmux"                            # Transport protocol to use ("tcpmux" or "wsmux") (mandatory).
default_token = "musix"                         # Default authentication token for secure communication.
log_level = "info"                              # Log level ("panic", "fatal", "error", "warn", "info", "debug", "trace") (optional, default: "info").
so_rcvbuf = 1094304                             # Socket receive buffer size in bytes (default 0, determined by kernel).
so_sndbuf = 4194304                             # Socket send buffer size in bytes (default 0, determined by kernel).
mss = 1340                                      # Maximum segment size for TCP connections (default 0, determined by kernel).
disable_nodelay = false                         # Disable TCP_NODELAY (optional, default: false).

default_disable_nodelay = false           # Default TCP_NODELAY setting for local services.
default_keep_alive = 20                   # Default keep-alive interval for local services (seconds).
default_mux_concurrency = 16              # Default maximum number of multiplexed streams per connection.
default_mux_version = 2                   # Default mux protocol version.
default_mux_framesize = 32768             # Default mux frame size in bytes (max: 65535).
default_mux_recievebuffer = 4194304       # Default receive buffer size for mux sessions in bytes.
default_mux_streambuffer = 2097152        # Default buffer size per mux stream in bytes (max: 2147483647). Must not be larger than the receive buffer.
default_retry_interval = 3 # # Default nterval in seconds between reconnection attempts if the connection fails.
default_dial_timeout = 10 #  # Default imeout in seconds for dialing the remote server. 
default_connection_pool = 16 # Default number of persistent connections to the server to maintain.
```

### **تنظیمات سرویس کلاینت**

```toml
[client.services.web] # "web" is your service name
local_addr = "127.0.1:5201" # Local address and port to forward the tunneled service.
token = "musix"                                 # Authentication token for this service (overrides default_token if set).
connection_pool = 8                            # Number of persistent connections to the server to maintain (for load balancing and concurrency) (default: 8)
disable_nodelay = false                         # Disable TCP_NODELAY for this service.
retry_interval = 3                              # Interval in seconds between reconnection attempts if the connection fails. (default: 3)
dial_timeout = 10                               # Timeout in seconds for dialing the remote server. (default 10s)
mux_concurrency = 16                            # Maximum number of concurrent multiplexed streams per connection. (default 16)
mux_version = 2                                 # Mux protocol version for this service.
mux_framesize = 32768                           # Frame size for mux packets in bytes.
mux_recievebuffer = 4194304                     # Receive buffer size for mux sessions in bytes.
mux_streambuffer = 2097152                      # Buffer size per mux stream in bytes.
keep_alive = 20                                 # Keep-alive interval for tunnel connections (seconds).
```

### **توضیحات کامل آپشن‌های کلاینت**

#### **تنظیمات اتصال**
- **`remote_addr`**: آدرس و پورت سرور راه دور
- **`remote_addrs`**: لیست چندین سرور برای failover یا load balancing
- **`local_addrs`**: آدرس‌های محلی برای اتصالات خروجی
- **`edge_ip`**: IP محلی خاص برای اتصالات wsmux

#### **تنظیمات امنیت**
- **`default_token`**: توکن پیش‌فرض برای احراز هویت
- **`token`**: توکن اختصاصی برای هر سرویس

#### **تنظیمات شبکه**
- **`so_rcvbuf`**: اندازه بافر دریافت سوکت
- **`so_sndbuf`**: اندازه بافر ارسال سوکت
- **`mss`**: حداکثر اندازه سگمنت TCP
- **`disable_nodelay`**: غیرفعال کردن الگوریتم Nagle

#### **تنظیمات اتصال و retry**
- **`connection_pool`**: تعداد اتصالات پایدار برای نگهداری
- **`retry_interval`**: فاصله بین تلاش‌های اتصال مجدد
- **`dial_timeout`**: timeout برای اتصال به سرور
- **`keep_alive`**: فاصله ارسال بسته‌های keep-alive

#### **تنظیمات SMUX**
- **`mux_concurrency`**: حداکثر تعداد استریم‌های همزمان
- **`mux_version`**: نسخه پروتکل SMUX
- **`mux_framesize`**: اندازه فریم‌های SMUX
- **`mux_recievebuffer`**: اندازه بافر دریافت جلسات SMUX
- **`mux_streambuffer`**: اندازه بافر هر استریم SMUX

## 📝 **مثال‌های پیکربندی**

### **کانفیگ سرور ساده**

```toml
[server]
bind_addr = ":4040"
transport = "tcpmux"
default_token = "musix"

[server.services.ssh]
bind_addr = "0.0.0.0:2057"
token = "musix"
```

### **کانفیگ کلاینت ساده**

```toml
[client]
remote_addr = "127.0.0.1:4040"
transport = "tcpmux"
default_token = "musix"

[client.services.ssh]
local_addr = "127.0.0.1:5201"
token = "musix"
```

### **کانفیگ پیشرفته با چندین سرویس**

```toml
[server]
bind_addr = ":4040"
transport = "tcpmux"
default_token = "musix"
log_level = "info"
keep_alive = 30

[server.services.ssh]
bind_addr = "0.0.0.0:2057"
token = "ssh_token"
mux_concurrency = 32
keep_alive = 60

[server.services.web]
bind_addr = "0.0.0.0:80"
token = "web_token"
mux_concurrency = 16
```

```toml
[client]
remote_addr = "server.example.com:4040"
transport = "tcpmux"
default_token = "musix"
log_level = "info"

[client.services.ssh]
local_addr = "127.0.0.1:5201"
token = "ssh_token"
connection_pool = 4
retry_interval = 5

[client.services.web]
local_addr = "127.0.0.1:8080"
token = "web_token"
connection_pool = 8
```

## 🔧 **بهینه‌سازی عملکرد**

### **تنظیمات بافر**
- **`so_rcvbuf`**: برای ترافیک بالا، 4MB یا بیشتر
- **`so_sndbuf`**: برای ترافیک بالا، 4MB یا بیشتر
- **`mux_recievebuffer`**: 4MB برای عملکرد بهینه
- **`mux_streambuffer`**: 2MB برای تعادل بین حافظه و عملکرد

### **تنظیمات همزمانی**
- **`mux_concurrency`**: 16-32 برای اکثر موارد
- **`connection_pool`**: 4-8 برای کلاینت‌ها

### **تنظیمات شبکه**
- **`keep_alive`**: 20-60 ثانیه بسته به شرایط شبکه
- **`disable_nodelay`**: true برای کاهش latency

## 🚨 **محدودیت‌ها و نکات مهم**

### **محدودیت‌های SMUX**
- **`mux_framesize`**: حداکثر 65535 بایت
- **`mux_streambuffer`**: حداکثر 2147483647 بایت
- **`mux_streambuffer`**: نباید بزرگتر از `mux_recievebuffer` باشد

### **نکات امنیتی**
- همیشه از توکن‌های قوی استفاده کنید
- توکن‌های مختلف برای سرویس‌های مختلف
- لاگ‌ها را در محیط تولید بررسی کنید

### **نکات عملکرد**
- برای ترافیک بالا، بافرها را افزایش دهید
- `connection_pool` را بر اساس تعداد کاربران تنظیم کنید
- `keep_alive` را بر اساس شرایط شبکه تنظیم کنید

## 📚 **مرجع کامل آپشن‌ها**

### **آپشن‌های اجباری**
- `bind_addr` / `remote_addr`: آدرس و پورت
- `transport`: نوع پروتکل
- `token`: توکن احراز هویت

### **آپشن‌های اختیاری با پیش‌فرض**
- `log_level`: "info"
- `keep_alive`: 20 ثانیه
- `mux_concurrency`: 16
- `mux_version`: 2
- `connection_pool`: 8 (کلاینت)
- `retry_interval`: 3 ثانیه (کلاینت)
- `dial_timeout`: 10 ثانیه (کلاینت)

### **آپشن‌های پیشرفته**
- `so_rcvbuf` / `so_sndbuf`: بافرهای سوکت
- `mss`: حداکثر اندازه سگمنت
- `disable_nodelay`: کنترل الگوریتم Nagle
- `mux_framesize` / `mux_recievebuffer` / `mux_streambuffer`: تنظیمات SMUX


### **نحوه تنظیم سرویس‌ها**
برای کانفیگ تانل نیاز به یک فایل سرور (ایران) و یک کلاینت (خارج) دارید. یک سری آپشن ها مربوط به کلیت تانل هست که باید در زیر قسمت [server] یا [client] تنظیم کنید. یک سری آپشن ها هم مختص به سرویس هایی هست که دارید.

اما سرویس چیه؟
در راکت برخلاف بکهال هر پورتی به یک نامی شناخته میشه. مثلا فرض کنید میخواید سرور رسپینا رو به هتزنر تانل کنید باید اول یک اسم سرویس مشترک انتخاب کنید. من برای مثال در کانفیگ ایران می نویسم:
```
[server.services.respina]
bind_addr = "0.0.0.0:80"
token = "musix"
```

این یعنی یک سرویس دارم که در سرور ایران به پورت 80 متصل شده و توکن اون هم `musix` هست. در سرور خارج هم باید همین سرویس مشخص شه:


```
[client.services.respina]
local_addr = "127.0.0.1:5201"
token = "musix"
```
این یعنی هر ترافیک که به این سرویس اومد رو به پورت و آدرس `127.0.0.1:520` میفرسته. در بالا هم تمام آپشن ها رو توضیح دادم.

