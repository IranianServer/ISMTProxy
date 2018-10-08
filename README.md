# راه اندازی سرور MTProxy برروی Centos 6.x و Centos 7.x

## مرحله اول :

###### 1. آپدیت کردن رپوزیتوری های سنتوس :
سنتوس نسخه 6:
```
yum -y install epel-release
rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
```

سنتوس نسخه 7:
```
yum -y install epel-release
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```


###### 2. نصب پکیج های مورد نیاز
```
yum -y install wget gcc gcc-c++ flex bison make bind bind-libs bind-utils openssl openssl-devel perl quota libaio libcom_err-devel libcurl-devel tar diffutils nano dbus.x86_64 db4-devel cyrus-sasl-devel perl-ExtUtils-Embed.x86_64 cpan vim-common screen libtool perl-core zlib-devel htop git
```

## مرحله دوم:

###### 1. دانلود و کامپایل کردن MTProxy :
```
cd ~
git clone https://github.com/IranianServer/ISMTProxy.git
cd ISMTProxy
chmod 0755 install.sh
./install.sh
```

اطلاعات شما در دایرکتوری روبرو قرار گرفته :  *objs/bin/mtproto-proxy*

###### 2. دانلود فایل های کانفیگ MTProxy :
```
cd ~/ISMTProxy/objs/bin
curl -s https://core.telegram.org/getProxySecret -o proxy-secret
curl -s https://core.telegram.org/getProxyConfig -o proxy-multi.conf
```

###### 3. ایجاد یک سکرت جدید به منظور اتصال کاربران:
```
head -c 16 /dev/urandom | xxd -ps
```

## قبلا از اجرا کردن MTProxy :  *(پروکسی خود را در ربات مربوطه ادد کرده و تگ کانال اسپانسری را دریافت کنید)*

1. به ربات [@MTProxybot](https://t.me/MTProxybot) یک پیام بدهید.
2. دستور */newproxy* را ارسال کنید.
3. آیپی سرور خود را همراه با پورت ارسال کنید مانند روبرو ==> *host:port*
4. سکرت کد دریافتی در سرور توسط دستور رو برو را وارد کنید *head -c 16 /dev/urandom | xxd -ps*
5. دستور */myproxies* را وارد کنید و بعد از تنطیم کانال اسپانسری تگ آن را دریافت کنید  ( *66102ce906bfhsm89dko7bfc04b6543* )
6. حالا میتونید پروکسی خود را همراه با کانال اسپانسری اجرا نمائید.

## اجرای MTProxy بر روی سرور
```
screen
cd ~/ISMTProxy/objs/bin && ./mtproto-proxy --user nobody --port 8888 --http-ports 444 --mtproto-secret <secret> --proxy-tag  <tag> --aes-pwd proxy-secret proxy-multi.conf --slaves 2
```
or 
```
screen
cd ~/ISMTProxy/objs/bin && ./mtproto-proxy -u nobody -p 8888 -H 444 -S <secret> -P  <tag> --aes-pwd proxy-secret proxy-multi.conf -M 2
```

for limit connections per worker:
```
screen
cd ~/ISMTProxy/objs/bin && ./mtproto-proxy --user nobody --port 8888 --http-ports 444 --mtproto-secret <secret> --proxy-tag  <tag> --aes-pwd proxy-secret proxy-multi.conf --slaves 2 --max-special-connections 10
```

where:
- **444** پورت دلخواه به منظور اتصال به پروکسی
- **8888** پورت داخلی ، شما میتوانید از این پورت به منظور دریافت آمار کاربران استفاده کنید . مانند : *wget localhost:8888/stats*
- **\<secret\>** سکرت کد ایجاد شده توسط کامند : *head -c 16 /dev/urandom | xxd -ps*
- **\<tag\>** تگ دریافتی توسط ربات : [@MTProxybot](https://t.me/MTProxybot)
- **2** تعداد کاربران پروکسی ، اگر سرور مناسبی دارید میتوانید این تعداد را بیشتر در نظر بگیرید
- میتوانید گزینه های دیگر را توسط کامند روبرو در پروکسی خد اضافه کنید ```# ~/MTProxy/objs/bin/mtproto-proxy -help```

###  :exclamation: فراموش نکنید که  [پورت پروکسی](https://www.google.com/search?q=open+port+linux+firewall&ie=utf-8&oe=utf-8&client=firefox-b-ab) را در فایروال باز کنید

## قطع کردن پروکسی
```
pkill -u mtproxy && pkill screen
```


[@iranian_servers](https://t.me/iranian_servers)
