# Xray VLESS + WebSocket + TLS + Nginx + WARP + CDN

Автоматический установщик Xray VLESS с поддержкой WebSocket, TLS, Nginx, Cloudflare WARP и CDN защитой.

## Особенности

- ✅ **VLESS + WebSocket + TLS** — современный и безопасный протокол
- ✅ **Nginx** — как reverse proxy с сайтом-заглушкой
- ✅ **Cloudflare WARP** — для обхода блокировок (OpenAI, ChatGPT и др.)
- ✅ **CDN поддержка** — защита от прямого доступа к серверу
- ✅ **Два метода SSL** — Cloudflare DNS API или Standalone
- ✅ **Fail2Ban + Web-Jail** — защита от брутфорса и сканеров
- ✅ **BBR** — опциональное ускорение TCP
- ✅ **Anti-Ping** — скрытие сервера от обнаружения
- ✅ **Управление логами** — просмотр и очистка

## Быстрая установка (1 строка)

```bash
curl -L https://raw.githubusercontent.com/HnDK0/VLESS-WebSocket-TLS-Nginx-WARP/main/vless-setup.sh  -o vwn && bash vwn
```

Или классический способ:
```bash
wget https://raw.githubusercontent.com/HnDK0/VLESS-WebSocket-TLS-Nginx-WARP/main/vless-setup.sh
chmod +x vless-setup.sh
sudo ./vless-setup.sh
```

После первого запуска скрипт доступен как команда:
```bash
sudo vwn
```

## Требования

- Чистый VPS с Ubuntu/Debian/CentOS
- Root доступ
- Домен, направленный на сервер (для SSL)

## Порты

| Порт | Назначение |
|------|-----------|
| 22   | SSH (изменяемый) |
| 443  | VLESS + Nginx HTTPS |
| 40000| WARP SOCKS5 (локальный) |

## Меню управления (29 пунктов)

```
================================================================
   XRAY VLESS + WARP + CDN | 06.02.2026 07:44
================================================================
  NGINX: RUNNING  |  XRAY: RUNNING  |  WARP: ACTIVE
  SSL: OK (89 d)  |  BBR: ON  |  F2B: OFF
  WebJail: NO  |  CDN: OFF
----------------------------------------------------------------
	1.  Установить Xray (VLESS+WS+TLS+WARP+CDN)
	2.  Показать QR-код и ссылку
	3.  Сменить UUID
	—————————————— Конфигурация —————————————
	4.  Изменить порт Xray
	5.  Изменить путь WebSocket
	6.  Изменить сайт-заглушку
	7.  Перевыпустить SSL сертификат
	8.  Сменить домен
	—————————————— CDN и WARP ———————————————
	9.  Переключить CDN режим (ON/OFF)
	10. Переключить режим WARP (Global/Split)
	11. Добавить домен в WARP
	12. Удалить домен из WARP
	13. Редактировать список WARP (Nano)
	14. Проверить IP (Real vs WARP)
	—————————————— Безопасность —————————————
	15. Включить BBR
	16. Включить Fail2Ban
	17. Включить Web-Jail
	18. Сменить SSH порт
	—————————————— Логи —————————————————————
	19. Логи Xray (access)
	20. Логи Xray (error)
	21. Логи Nginx (access)
	22. Логи Nginx (error)
	23. Очистить все логи
	—————————————— Сервисы ——————————————————
	24. Перезапустить все сервисы
	25. Обновить Xray-core
	26. Полное удаление
	—————————————— UFW, SSL и Logs ————————————
	27. Управление UFW (открыть/закрыть порты)
	28. Управление автообновлением SSL
	29. Управление автоочисткой логов
	—————————————— Выход ————————————————————
	0.   Выйти
----------------------------------------------------------------
```

## CDN режим

При включении CDN режима (пункт 9):
- Скачиваются актуальные IP адреса Cloudflare
- Создается geo-модуль для определения CF IP
- Прямой доступ к серверу блокируется (return 444)
- Работает только через Cloudflare Proxy

**Важно:** Включайте CDN режим ТОЛЬКО после настройки домена через Cloudflare с включенным оранжевым облаком!

## WARP режимы

**Global** — весь трафик через WARP (для максимальной анонимности)

**Split** — только выбранные домены через WARP (по умолчанию OpenAI/ChatGPT)

## SSL сертификаты

**Метод 1 — Cloudflare DNS API** (рекомендуется):
- Порт 80 остается закрытым
- Требуются API ключи Cloudflare
- Автоматическое продление

**Метод 2 — Standalone**:
- Временно открывает порт 80
- Не требует API ключей
- Автоматическое открытие/закрытие порта через хуки

### Автообновление SSL

При установке автоматически настраивается cron задача:
- **Время**: раз в 35 дней в 3:00
- **Команда**: `acme.sh --cron` с хуками для UFW
- **Хуки**: `vwn open-80` перед обновлением, `vwn close-80` после
- **Лог**: `/var/log/acme_cron.log`

Управление автообновлением — пункт меню **28**.

### Автоочистка логов

При установке автоматически настраивается:
- **Ротация логов** — ежедневно, хранение 7 дней
- **Очистка логов** — раз в неделю (воскресенье в 4:00)

Управление автоочисткой — пункт меню **29**.

## UFW Firewall

Управление портами — пункт меню **27**:
- Показать текущие правила
- Открыть порт (с выбором tcp/udp/both)
- Закрыть порт
- Включить/выключить UFW
- Сбросить все правила

## Безопасность

| Функция | Описание |
|---------|----------|
| UFW | Firewall с правилами для 22, 443 |
| Anti-Ping | Скрытие сервера от пингов (ICMP) — включается автоматически при установке |
| Fail2Ban | Блокировка брутфорса SSH |
| Web-Jail | Блокировка сканеров nginx (404 ловушка) |
| CDN Only | Доступ только через Cloudflare |

### Anti-Ping

При установке автоматически отключается ответ на ICMP ping:
```bash
sysctl -w net.ipv4.icmp_echo_ignore_all=1
```

Это предотвращает обнаружение сервера как прокси/VPN через простой ping-скан.

## Структура конфигов

```
/etc/nginx/
├── nginx.conf                 # Основной конфиг
├── conf.d/
│   ├── xray.conf             # VLESS server
│   ├── default.conf          # Заглушка для IP
│   ├── cloudflare_whitelist.conf    # Geo-модуль CF
│   └── cloudflare_real_ips.conf     # Real IP от CF
└── cert/
    ├── cert.pem              # SSL сертификат
    ├── cert.key              # SSL ключ
    ├── default.crt           # Самоподписанный
    └── default.key

/usr/local/etc/xray/
└── config.json               # Конфиг Xray

/usr/local/etc/xray/
└── warp_domains.txt          # Список доменов для WARP
```

## Проблемы и решения

### WARP не подключается
```bash
sudo systemctl restart warp-svc
sleep 3
sudo warp-cli --accept-tos connect
sudo warp-cli --accept-tos status
```

### Nginx не запускается
```bash
sudo nginx -t              # Проверка конфига
sudo systemctl restart nginx
```

### SSL сертификат истек
```bash
sudo vwn
# Пункт 7 — Перевыпустить SSL сертификат
# или
# Пункт 28 — Проверить автообновление
```

## Удаление

```bash
sudo vwn
# Пункт 26 — Полное удаление
```

Или вручную:
```bash
sudo systemctl stop nginx xray warp-svc
sudo warp-cli disconnect
sudo apt purge -y nginx cloudflare-warp
sudo bash <(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh) @ remove
sudo rm -rf /etc/nginx /usr/local/etc/xray /root/.cloudflare_api
```

## Благодарности

- [Xray-core](https://github.com/XTLS/Xray-core)
- [Cloudflare WARP](https://1.1.1.1/)
- [acme.sh](https://github.com/acmesh-official/acme.sh)

## Лицензия

MIT License
