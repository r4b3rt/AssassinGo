![](./logo.jpg)

[![Rawsec's CyberSecurity Inventory](https://inventory.raw.pm/img/badges/Rawsec-inventoried-FF5050_flat.svg)](https://inventory.raw.pm/tools.html#AssassinGo)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://choosealicense.com/licenses/mit/)

# AssassinGo

AssassinGo is an extensible and concurrency information gathering and vulnerability scanning framework, with WebSocket based [Web GUI](https://github.com/U1in/AssassinGo-Front-End).

Just for learn, welcome PR.

## Features

- [x] Retrieve Security Headers
- [x] Bypass CloudFlare
- [x] Detect CMS Version
- [x] Honeypot Detect
- [x] Port Scan
- [x] Trace Route and Mark on Google Map
- [x] Subdomain Scan
- [x] Dir Scan and Site Map
- [x] Whois Lookup
- [x] Crawl the Paramed URLs
- [x] Basic SQLi Check
- [x] Basic XSS Check
- [x] Intruder
- [x] SSH Bruter
- [x] Google-Hacking with Headless-Chrome
- [x] Friendly PoC Interface
- [x] Web GUI(using WebSocket)
- [ ] Generate Report

## Installation

### localhost

```bash
git clone https://github.com/AmyangXYZ/AssassinGo
cd AssassinGo
docker-compose up --build -d
cat backup.sql | docker exec -i assassingo_mariadb_1 /usr/bin/mariadb -uag --password=password ag
```

Then visit http://127.0.0.1:8000 and login as admin:admin

### VPS

If you want to deploy on your VPS, please clone the [Frontend](https://github.com/U1in/AssassinGo-Front-End) and modify the `base_url` of AJAX and WebSocket, then run `npm run build` and copy the output to `web/` directory as [deploy.sh](./deploy.sh) says.

Remember to add your google-map key in `index.html`.

## Demo

![base](demo/demo1.png)

![traceroute](demo/demo2.png)

![subdomain](demo/demo6.png)

![intruder](demo/demo9.png)

![seek](demo/demo8.png)

![poc](demo/demo3.png)

## Outline Design

I choose **Composite Pattern** to increase expansibility.

![design-pattern](./design-pattern.png)

## API

### AJAX

Path | Method | Func | Params | Return
----- | ----- | ----- | ----- | -----
/token | POST | sign in | username=admin&password=adminn | {SG_Token:"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1M…W4ifQ.qY-k5f54CrQ6_dNdjgQgqjh5xS8iFZOjTLcfMfirY0w" (stored in cookie)}
/api/target | POST | set a target | target=xxx OR targets=t1,t2... | nil
/api/info/basic | GET | get ip and retrieve security headers | nil | {data:{"ip": "192.168.1.1", "webserver": "nginx","click_jacking_protection":true,"content_security_policy":false,"strict_transport_security":false,"x_content_type_options":true}
/api/info/bypasscf | GET | find real ip behind cloudflare | nil | {"real_ip":"123.123.123.123"}
/api/info/cms | GET | detect cms | nil | {data:{"cms": "wordpress"}}
/api/info/honeypot | GET | get ip and webserver | nil | {data:{"score": "0.3"}}
/api/info/whois | GET | whois | nil | {data:{"domain":"example.com","registrar_name":"alibaba", "admin_name":"xiaoming", "admin_email":"a@qq.com", "admin_phone":"+86.12312345678", "created_date":"2016-07-28T12:57:53.0Z","expiration_date":"2018-07-28T12:57:53.0Z", "ns":"dns9.hichina.com", "state":"clienttransferprohibited"}}
/api/poc | GET | get poc list | nil | {data:{"poc_list":["drupal-rce":{"id":"CVE-2017-7602","ty## pe":"remote code execution","text":"biubiubiu","platform## ":"php","data":"2018-04-25",## "reference":"https://cve.mitre.org/cgi-## bin/cvename.cgi?name=CVE-2018-7602"},"seacms-v654-rce"]## }}
/api/poc/:poc | GET | run the specified poc | nil | {data:{"host": "example.com", "exploitable":"true"}}

### WebSocket

Path | Func | Params | Return
----- | ----- | ----- | -----
/ws/info/port | port scan | nil | {"port": "80", "service": "http"}
/ws/info/tracert | trace route and mark on google map | nil | {"ttl": 1, "addr": 192.168.1.1, "elapsed_time": 22720440, "country": China, "lat": 34.2583,"long": 116.1614}
/ws/info/subdomain | enmu subdomain | nil | {"subdomain":"earth.google.com"}
/ws/info/dirb | brute force dir | {"concurrency":20, "dict":"php"}; {"stop":1} | {"path": "admin.php", "resp_status": 200, "resp_len": 110}
/ws/attack/crawl | crawl paramed urls | {"max_depth": 4} | {"url": "example.com/?id=1"}
/ws/attack/sqli | check sqli | nil | {"sqli_url": "example.com/?id=1}
/ws/attack/xss | check xss | nil | {"xss_url": "example.com/?id=1}
/ws/attack/intrude | brute force | {"header": "GET / HTTP/1.1 ...", "payload": "p1,p2...", "concurrency": "10"}; {"stop":1}| {"payload": 1, "resp_status": 200, "resp_len": 110}
/ws/attack/ssh | brute force ssh | {"port":"22",, "concurrency":40} | {"user":"root","passwd":"biubiubiu"}
/ws/seek | seek targets | {"query": "biu", "se": "bing/google", "max_page": 10} | {"urls": urls}
/ws/poc/:poc | run poc | {concurrency:10} | {"exploitable_host": "example.com"}

## License

MIT
