+++
date = '2026-11-10T12:00:00+01:00'
draft = false
title = 'Что такое DNS'
# Temporary unlisted page: reachable by direct link only.
noindex = true
[build]
  list = 'never'
[sitemap]
  disable = true
+++
# Что такое DNS

DNS (Domain Name System) — это система, которая преобразует доменные имена в IP-адреса, то есть числовые адреса серверов в сети. Когда вы вводите в браузере имя сайта, ваше устройство отправляет запрос на DNS-сервер и получает в ответ IP-адрес сервера, на котором расположен запрошенный сайт.

> **Пример:** доменному имени www.example.com соответствует IP-адрес `104.20.23.154` (протокол IPv4) или `2606:4700:10::ac42:93f3` (IPv6).

Проще всего представить DNS-сервер как аналог телефонной книги в смартфоне: вы вводите имя контакта и нажимаете «Вызов», а телефон соединяет вас с соответствующим абонентским номером. Однако эта аналогия упускает важную разницу: телефонная книга хранится на вашем устройстве и/или в вашем облачном хранилище, и сведения заносите в неё вы. DNS-серверы расположены в интернете, и за их скорость, безопасность и полноту информации отвечают DNS-провайдеры.

## Типы DNS-серверов

DNS-серверы образуют иерархическую структуру. Все серверы можно разделить на несколько типов:

* **Корневые серверы** обеспечивают работу корневой зоны DNS: в ответ на запросы других серверов они сообщают адреса серверов, отвечающих за нужную доменную зону верхнего уровня (Top-Level Domain, TLD), например RU или COM. Корневых серверов 13, их адреса имеют вид `<x>.root-servers.net`, где `x` — латинская буква от `a` до `m`. При этом у каждого сервера множество копий, работающих под одним IP-адресом: благодаря технологии маршрутизации Anycast запрос направляется на ближайший физический сервер. Корневые серверы обслуживают 12 организаций, в том числе [ICANN](https://www.icann.org/en), а содержимое корневой зоны координирует IANA — подразделение ICANN. К корневым серверам обращаются далеко не при каждом запросе: большинство запросов резолвер обрабатывает по данным из кэша.

* **TLD-серверы** отвечают за работу доменных зон верхнего уровня. Как правило, они не хранят IP-адреса сайтов, а указывают на авторитативные серверы конкретных доменов.

* **Авторитативные серверы** содержат записи о доменах с их IP-адресами и возвращают их в ответ на запросы. Именно они играют роль той самой телефонной книги.

* **Рекурсивные резолверы** выступают посредниками между пользователем и остальной сетью DNS-серверов. Они обрабатывают запросы и выполняют поиск адресов, запрашивая информацию у корневых, TLD- и авторитативных серверов. Резолверы также кэшируют полученные ответы, чтобы при повторном запросе сразу вернуть адрес, не проходя всю цепочку заново. Собственные резолверы есть у интернет-провайдеров: их адреса устройство получает автоматически при подключении к сети и использует по умолчанию. Существуют также публичные резолверы, которые позволяют сделать поиск быстрее и безопаснее.

## Как работает DNS-запрос

Когда вы вводите в браузере адрес сайта, DNS-запрос проходит следующие этапы:

1. Браузер проверяет, нет ли IP-адреса в его кэше или в кэше операционной системы.
2. Если адреса в кэше нет, запрос отправляется рекурсивному резолверу, который тоже первым делом проверяет свой кэш.
3. Если адрес не найден, резолвер отправляет запрос корневому серверу. Корневой сервер возвращает адрес TLD-сервера нужной доменной зоны.
4. Резолвер запрашивает TLD-сервер и получает адрес нужного авторитативного сервера.
5. Резолвер запрашивает авторитативный сервер, получает IP-адрес и передаёт его браузеру.

Получив IP-адрес, браузер обращается к серверу и загружает сайт. На деле, пока сайт загружается, браузер может сделать не один, а несколько десятков DNS-запросов: сайт может включать субдомены, картинки или видео, размещённые на сторонних хостингах, или рекламные баннеры из партнёрской сети, и для них тоже нужно получить адреса.

## Зачем менять настройки DNS

Описанный выше процесс происходит автоматически и не требует участия пользователя. Резолверы операторов связи, как правило, неплохо справляются с базовой задачей — находить IP-адреса запрошенных сайтов. Менять DNS-провайдера имеет смысл, если вам нужны дополнительные возможности:

* **Скорость**. Публичные резолверы часто отвечают быстрее, чем серверы оператора связи, но так бывает не всегда — скорость лучше [проверить самостоятельно](#как-проверить-скорость-dns-сервера).
* **Фильтрация рекламы, трекеров или нежелательного контента для детей**. Некоторые сервисы используют списки рекламных доменов и публичные «чёрные списки» и не дают загружать отмеченный в них контент.
* **Приватность**. Запросы к резолверу оператора связи передаются в открытом виде. Оператор знает ваши персональные данные и может хранить сведения о посещённых вами сайтах, использовать их в рекламных целях или передавать властям. Публичные сервисы позволяют шифровать DNS-трафик, а некоторые из них также обещают не хранить логи дольше суток. Учтите, что шифрование скрывает запросы от оператора связи, но не от самого DNS-провайдера, поэтому при выборе сервиса обращайте внимание на его политику логов.

> **Как шифруется DNS-трафик:** для шифрования используются два протокола: **DoT** (DNS over TLS) и **DoH** (DNS over HTTPS). DoT работает через отдельный порт 853, поэтому такой трафик легко распознать и заблокировать. DoH передаётся через порт 443 вместе с обычным HTTPS-трафиком сайтов, поэтому выделить его сложнее.

Смена DNS-провайдера, скорее всего, не поможет обойти блокировки. Она справляется только с самым примитивным способом блокировки — на уровне DNS, когда резолвер оператора связи просто не отдаёт адрес ресурса. В этом случае ограничение можно обойти с помощью публичного сервиса, но только при зашифрованном подключении (DoH или DoT): обычные DNS-запросы оператор может перехватить и подменить ответ, даже если вы указали сторонний сервер. Если же сайт блокируется на уровне IP-адресов или с помощью глубокой фильтрации трафика (DPI/ТСПУ), смена DNS-провайдера не поможет.

## Сравнение DNS-провайдеров

В таблице указаны основные адреса каждого сервиса. Если у сервиса есть режимы фильтрации с отдельными адресами, они перечислены в колонке «Фильтрация».

Все адреса в таблице предназначены для настроек устройства, а не для открытия в браузере:

* **IPv4 и IPv6** — адреса для обычного, незашифрованного подключения. Их указывают в сетевых настройках компьютера, смартфона или роутера.
* **DoH** — адрес вида `https://…/dns-query` для зашифрованного подключения. Его указывают в настройках безопасного DNS в браузере, в Windows 11 или в роутере. Если открыть такой адрес в браузере, вы увидите ошибку или пустую страницу: сервер принимает только DNS-запросы.
* **DoT** — имя сервера без `https://`. Его указывают, например, в настройках частного DNS-сервера на Android.

> **Совет:** таблица прокручивается по горизонтали. На компьютере прокручивайте колёсико мыши с зажатой клавишей **Shift** или проведите двумя пальцами по тачпаду. На телефоне проведите пальцем по таблице.

<a id="dns-table"></a>

| Сервис | IPv4 | IPv6 | Фильтрация | Политика логов | Юрисдикция | Адрес для DoH / DoT |
|---|---|---|---|---|---|---|
| [**Cloudflare**](https://one.one.one.one/) | `1.1.1.1`<br>`1.0.0.1` | `2606:4700:4700::1111`<br>`2606:4700:4700::1001` | Без фильтрации по умолчанию.<br>Вредоносные сайты: `1.1.1.2`, `1.0.0.2`.<br>Вредоносные сайты и контент для взрослых: `1.1.1.3`, `1.0.0.3` | Полные IP-адреса не записываются на диск. Логи запросов и усечённые IP-адреса удаляются в течение 25 часов, бессрочно хранится только агрегированная статистика. Соблюдение политики проверяет независимая аудиторская компания | США | DoH: `https://cloudflare-dns.com/dns-query`<br>DoT: `one.one.one.one` |
| [**Google Public DNS**](https://developers.google.com/speed/public-dns) | `8.8.8.8`<br>`8.8.4.4` | `2001:4860:4860::8888`<br>`2001:4860:4860::8844` | Нет | Временные логи с IP-адресами хранятся 24–48 часов. В постоянные логи попадает выборка запросов без IP-адресов, с приблизительным местоположением (не точнее города) | США | DoH: `https://dns.google/dns-query`<br>DoT: `dns.google` |
| [**Quad9**](https://quad9.net/) | `9.9.9.9`<br>`149.112.112.112` | `2620:fe::fe`<br>`2620:fe::9` | Вредоносные и фишинговые сайты блокируются по умолчанию.<br>Без фильтрации: `9.9.9.10`, `149.112.112.10` | IP-адреса не записываются. Хранится только обезличенная статистика | Швейцария | DoH: `https://dns.quad9.net/dns-query`<br>DoT: `dns.quad9.net` |
| [**OpenDNS (Cisco)**](https://www.opendns.com/) | `208.67.222.222`<br>`208.67.220.220` | `2620:119:35::35`<br>`2620:119:53::53` | Базовая защита от фишинга.<br>FamilyShield (контент для взрослых): `208.67.222.123`, `208.67.220.123`.<br>Гибкая настройка категорий после регистрации | Запросы записываются для статистики в личном кабинете. Действует общая политика конфиденциальности Cisco | США | DoH: `https://doh.opendns.com/dns-query`<br>DoT: `dns.opendns.com` |
| [**AdGuard DNS**](https://adguard-dns.io/) | `94.140.14.14`<br>`94.140.15.15` | `2a10:50c0::ad1:ff`<br>`2a10:50c0::ad2:ff` | Реклама, трекеры и фишинговые сайты блокируются по умолчанию.<br>Семейный режим: `94.140.14.15`, `94.140.15.16`.<br>Без фильтрации: `94.140.14.140`, `94.140.14.141`.<br>Собственные правила — в личном DNS-сервере (AdGuard DNS Private) | Публичный сервис не обрабатывает персональные данные. Хранятся только агрегированные метрики и анонимный список доменов за последние 24 часа | Кипр | DoH: `https://dns.adguard-dns.com/dns-query`<br>DoT: `dns.adguard-dns.com` |
| [**Яндекс DNS**](https://dns.yandex.ru/) | `77.88.8.8`<br>`77.88.8.1` | `2a02:6b8::feed:0ff`<br>`2a02:6b8:0:1::feed:0ff` | Базовый режим без фильтрации.<br>Безопасный (вредоносные и мошеннические сайты): `77.88.8.88`, `77.88.8.2`.<br>Семейный (дополнительно контент для взрослых): `77.88.8.7`, `77.88.8.3` | Срок хранения в документации сервиса не указан. Действует общая политика конфиденциальности Яндекса | Россия | DoH: `https://common.dot.dns.yandex.net/dns-query`<br>DoT: `common.dot.dns.yandex.net` |
| [**NextDNS**](https://nextdns.io/) | Выдаются индивидуально в личном кабинете | Выдаются индивидуально в личном кабинете | Гибкая настройка: реклама, трекеры, вредоносные сайты, родительский контроль, собственные чёрные и белые списки | Без аккаунта логи не ведутся. С аккаунтом логи можно отключить или выбрать срок хранения (от 1 часа до 2 лет) и страну хранения (США, ЕС, Великобритания, Швейцария) | США | DoH: `https://dns.nextdns.io/<ID>`<br>DoT: `<ID>.dns.nextdns.io` |
| [**Control D**](https://controld.com/) | `76.76.2.0`<br>`76.76.10.0` | `2606:1a40::`<br>`2606:1a40:1::` | Бесплатные профили с разными адресами: вредоносные сайты (`76.76.2.1`), реклама и трекеры (`76.76.2.2`), семейный режим (`76.76.2.4`).<br>Гибкая настройка в платной версии | Бесплатные серверы не хранят IP-адреса, временные метки и сами запросы. Логи ведутся только в платной версии с собственными профилями | Канада | DoH: `https://freedns.controld.com/p0`<br>DoT: `p0.freedns.controld.com` |
| [**CleanBrowsing**](https://cleanbrowsing.org/) | `185.228.168.168`<br>`185.228.169.168` | `2a0d:2a00:1::`<br>`2a0d:2a00:2::` | Основные адреса — семейный фильтр.<br>Только контент для взрослых: `185.228.168.10`, `185.228.169.11`.<br>Только вредоносные сайты: `185.228.168.9`, `185.228.169.9` | В бесплатной версии IP-адреса и другие персональные данные не записываются. Обезличенные данные о запросах могут храниться бессрочно | США | DoH: `https://doh.cleanbrowsing.org/doh/family-filter/`<br>DoT: `family-filter-dns.cleanbrowsing.org` |
| [**Mullvad DNS**](https://mullvad.net/ru/help/dns-over-https-and-dns-over-tls) | `194.242.2.2` (только через DoH/DoT) | `2a07:e340::2` (только через DoH/DoT) | Без фильтрации по умолчанию.<br>Отдельные серверы с разными наборами блокировок: реклама и трекеры (`adblock.dns.mullvad.net`), вредоносные сайты, соцсети, контент для взрослых, азартные игры | Запросы не логируются | Швеция | DoH: `https://dns.mullvad.net/dns-query`<br>DoT: `dns.mullvad.net` |

> **Обратите внимание:** Яндекс DNS работает в соответствии с российским законодательством и не открывает сайты, заблокированные в России. Сервисы из других юрисдикций могут быть недоступны или работать нестабильно в отдельных странах.

Данные актуальны на ноябрь 2026 года. Адреса и политики сервисов могут меняться — перед настройкой сверьтесь с официальной документацией провайдера.

## Как проверить скорость DNS-сервера

Прежде чем менять DNS-провайдера, вы можете самостоятельно проверить скорость выбранных резолверов. На неё влияют ваше местоположение, маршрутизация оператора связи и текущая загрузка канала, поэтому при выборе лучше не опираться на рейтинги и усреднённые показатели из интернета.

Замерить скорость для вашего устройства проще всего прямо в браузере с помощью сервисов, которые автоматически проверяют время ответа резолверов, например [DNS Speed Test](https://dnsspeedtest.online/) или [Control D DNS Speed Test](https://controld.com/tools/dns-speed-test).

## Как настроить DNS на устройстве

Вы можете настроить DNS на отдельном устройстве или для всей домашней сети через роутер. Ниже приведены инструкции для Android и Windows 11.

### Android

Частный DNS-сервер доступен на Android 9 и новее. Настройка действует для всех подключений телефона: и для Wi-Fi, и для мобильного интернета.

Чтобы сменить DNS-провайдера на Android:

1. Откройте **Настройки**.
2. Выберите пункт **Сеть и интернет**. В зависимости от производителя телефона и оболочки пункт может называться **Подключения** или **Другие способы подключения**. Если вы не можете найти нужный пункт, воспользуйтесь поиском в настройках: введите `DNS`.
3. Выберите **Частный DNS-сервер**.
4. Выберите вариант **Вручную**. В некоторых версиях Android он называется **Имя хоста поставщика частного DNS** или похожим образом.
5. В появившемся поле введите [имя хоста для DoT](#dns-table) выбранного провайдера, например `dns.google`.
6. Нажмите **Сохранить**.

Чтобы убедиться, что настройка работает, откройте в браузере сервис проверки, например [browserleaks.com/dns](https://browserleaks.com/dns) или [dnsleaktest.com](https://www.dnsleaktest.com/). Он покажет, чей DNS-сервер обрабатывает ваши запросы: там должен быть указан выбранный провайдер. Если после сохранения появилось сообщение об отсутствии подключения к интернету, проверьте, правильно ли введено имя хоста.

Чтобы вернуть настройки по умолчанию, выберите в пункте **Частный DNS-сервер** вариант **Авто** или **Выкл.**

### Windows 11

Сменить DNS-провайдера в Windows 11 можно несколькими способами. Самый простой — через меню **Параметры**: только здесь можно включить шифрование DNS (DoH). Также DNS можно задать в свойствах сетевого адаптера в Панели управления или через командную строку — эти способы работают и в Windows 10.

Чтобы сменить DNS-провайдера через меню **Параметры**:

1. Нажмите **Пуск** и выберите **Параметры** или нажмите сочетание клавиш **Win + I**.
2. Выберите раздел **Сеть и Интернет**.
3. Выберите тип подключения, для которого хотите изменить настройки: **Wi-Fi** или **Ethernet** (проводное подключение).
4. Для **Wi-Fi** выберите пункт **Свойства оборудования**, если хотите задать настройки для всех беспроводных сетей. Чтобы задать настройки только для одной сети, откройте **Управление известными сетями**, выберите нужную сеть, а затем её свойства.
5. В поле **Назначение DNS-сервера** нажмите кнопку **Изменить**.
6. В окне **Изменить настройки DNS** выберите **Вручную** вместо **Автоматически (DHCP)**.
7. Включите переключатель **IPv4**, **IPv6** или оба.
8. Введите [адреса](#dns-table) выбранного провайдера в поля **Предпочтительный DNS-сервер** и **Дополнительный DNS-сервер** для каждого включённого протокола.
9. Чтобы включить шифрование, настройте его под каждым адресом. Названия пунктов зависят от версии Windows 11:
   * В актуальных версиях выберите в списке **DNS по протоколу HTTPS** вариант **Включено (ручной шаблон)** и вставьте [адрес для DoH](#dns-table) выбранного провайдера в появившееся поле шаблона. Для некоторых известных сервисов, например Google, Cloudflare и Quad9, можно выбрать **Включено (автоматический шаблон)**: Windows подставит адрес сама. Чтобы при недоступности сервера запросы не отправлялись в незашифрованном виде, отключите переключатель отката к обычному DNS.
   * В ранних версиях (21H2) выберите в списках **Предпочтительное шифрование DNS** и **Альтернативное шифрование DNS** вариант **Только зашифрованные (DNS через HTTPS)**. Эти версии поддерживают шифрование только для известных сервисов, свой шаблон указать нельзя.
10. Нажмите **Сохранить**.

Чтобы убедиться, что настройка работает, откройте в браузере сервис проверки, например [browserleaks.com/dns](https://browserleaks.com/dns): в списке серверов должен быть выбранный провайдер. Если вы включили шифрование, в поле **Назначение DNS-сервера** рядом с адресами появится отметка о том, что соединение зашифровано.

Чтобы вернуть настройки по умолчанию, в поле **Назначение DNS-сервера** нажмите **Изменить** и выберите **Автоматически (DHCP)**.

---

# What is DNS

DNS (Domain Name System) is a system that translates domain names into IP addresses, the numeric addresses of servers on a network. When you type a website name in your browser, your device sends a request to a DNS server and receives the IP address of the server that hosts the website.

> **Example:** The domain name www.example.com corresponds to the IP address `104.20.23.154` (IPv4) or `2606:4700:10::ac42:93f3` (IPv6).

The easiest way to think of a DNS server is as the contact list on your phone: you choose a contact name and tap **Call**, and the phone connects you to the matching phone number. However, this analogy misses an important difference: your contact list is stored on your device or in your cloud storage, and you add the entries yourself. DNS servers are located on the internet, and DNS providers are responsible for their speed, security, and accuracy.

## Types of DNS servers

DNS servers form a hierarchy. They can be divided into several types:

* **Root servers** support the DNS root zone: in response to queries from other servers, they return the addresses of the servers responsible for the requested top-level domain (TLD), such as COM or RU. There are 13 root servers, and their addresses look like `<x>.root-servers.net`, where `x` is a letter from `a` to `m`. Each server has many copies that share the same IP address: thanks to Anycast routing, a query is sent to the nearest physical server. The root servers are operated by 12 organizations, including [ICANN](https://www.icann.org/en), and the contents of the root zone are coordinated by IANA, a function of ICANN. Root servers are not involved in every query: resolvers answer most queries from their cache.

* **TLD servers** manage top-level domain zones. As a rule, they don't store website IP addresses but point to the authoritative servers for specific domains.

* **Authoritative servers** store records for domains, including their IP addresses, and return them in response to queries. They are the actual contact list from the analogy above.

* **Recursive resolvers** act as intermediaries between the user and the rest of the DNS. They process queries and look up addresses by querying root, TLD, and authoritative servers. Resolvers also cache the answers they receive, so they can return an address right away the next time without going through the whole chain again. Internet service providers (ISPs) run their own resolvers: your device receives their addresses automatically when it connects to the network and uses them by default. There are also public resolvers that can make lookups faster and more secure.

## How a DNS query works

When you type a website address in your browser, the DNS query goes through the following steps:

1. The browser checks whether the IP address is in its own cache or in the operating system cache.
2. If the address isn't cached, the query is sent to a recursive resolver, which also checks its cache first.
3. If the address isn't found, the resolver queries a root server. The root server returns the address of the TLD server for the domain zone.
4. The resolver queries the TLD server and receives the address of the authoritative server.
5. The resolver queries the authoritative server, receives the IP address, and passes it to the browser.

Once it has the IP address, the browser connects to the server and loads the website. In practice, a browser can make dozens of DNS queries while a single page loads: the page may include subdomains, images or videos hosted elsewhere, or ads from partner networks, and each of them needs an address too.

## Why change your DNS settings

The process described above happens automatically and requires no action from the user. ISP resolvers usually handle the basic task of finding website IP addresses well enough. Changing your DNS provider makes sense if you need additional features:

* **Speed**. Public resolvers often respond faster than ISP servers, but not always, so it's best to [test the speed yourself](#how-to-test-dns-server-speed).
* **Blocking ads, trackers, or content unsuitable for children**. Some services use lists of ad domains and public blocklists and prevent the listed content from loading.
* **Privacy**. Queries to your ISP's resolver are sent unencrypted. Your ISP knows who you are and can store information about the websites you visit, use it for advertising, or share it with the authorities. Public services let you encrypt DNS traffic, and some of them also promise not to keep logs for more than 24 hours. Keep in mind that encryption hides your queries from your ISP but not from the DNS provider itself, so check the provider's logging policy before you choose.

> **How DNS traffic is encrypted:** There are two encryption protocols: **DoT** (DNS over TLS) and **DoH** (DNS over HTTPS). DoT uses a dedicated port, 853, so this traffic is easy to detect and block. DoH is sent over port 443 together with regular HTTPS website traffic, so it's harder to single out.

Changing your DNS provider is unlikely to help you bypass censorship. It only works against the most basic type of blocking, at the DNS level, when the ISP's resolver simply doesn't return the address of a website. In this case, a public service can help, but only over an encrypted connection (DoH or DoT): your ISP can intercept regular DNS queries and spoof the answer even if you've set a third-party server. If a website is blocked by IP address or with deep packet inspection (DPI), changing your DNS provider won't help.

## DNS provider comparison

The table lists the main addresses of each service. If a service offers filtering modes with separate addresses, they're listed in the **Filtering** column.

All addresses in the table are meant for your device settings, not for opening in a browser:

* **IPv4 and IPv6** are addresses for a regular, unencrypted connection. You enter them in the network settings of your computer, phone, or router.
* **DoH** is an address like `https://…/dns-query` for an encrypted connection. You enter it in the secure DNS settings of your browser, Windows 11, or router. If you open this address in a browser, you'll see an error or a blank page: the server only accepts DNS queries.
* **DoT** is a server name without `https://`. You enter it, for example, in the Private DNS settings on Android.

> **Tip:** The table scrolls horizontally. On a computer, hold **Shift** while scrolling the mouse wheel, or swipe with two fingers on the touchpad. On a phone, swipe the table.

<a id="dns-table-en"></a>

| Service | IPv4 | IPv6 | Filtering | Logging policy | Jurisdiction | DoH / DoT address |
|---|---|---|---|---|---|---|
| [**Cloudflare**](https://one.one.one.one/) | `1.1.1.1`<br>`1.0.0.1` | `2606:4700:4700::1111`<br>`2606:4700:4700::1001` | No filtering by default.<br>Malware: `1.1.1.2`, `1.0.0.2`.<br>Malware and adult content: `1.1.1.3`, `1.0.0.3` | Full IP addresses are never written to disk. Query logs and truncated IP addresses are deleted within 25 hours; only aggregated statistics are kept indefinitely. Compliance is verified by an independent audit firm | USA | DoH: `https://cloudflare-dns.com/dns-query`<br>DoT: `one.one.one.one` |
| [**Google Public DNS**](https://developers.google.com/speed/public-dns) | `8.8.8.8`<br>`8.8.4.4` | `2001:4860:4860::8888`<br>`2001:4860:4860::8844` | None | Temporary logs with IP addresses are kept for 24–48 hours. Permanent logs contain a sample of queries without IP addresses, with approximate location (no more precise than city level) | USA | DoH: `https://dns.google/dns-query`<br>DoT: `dns.google` |
| [**Quad9**](https://quad9.net/) | `9.9.9.9`<br>`149.112.112.112` | `2620:fe::fe`<br>`2620:fe::9` | Malware and phishing sites are blocked by default.<br>No filtering: `9.9.9.10`, `149.112.112.10` | IP addresses are not recorded. Only anonymized statistics are kept | Switzerland | DoH: `https://dns.quad9.net/dns-query`<br>DoT: `dns.quad9.net` |
| [**OpenDNS (Cisco)**](https://www.opendns.com/) | `208.67.222.222`<br>`208.67.220.220` | `2620:119:35::35`<br>`2620:119:53::53` | Basic phishing protection.<br>FamilyShield (adult content): `208.67.222.123`, `208.67.220.123`.<br>Custom category filtering after sign-up | Queries are logged for statistics in your account dashboard. Cisco's general privacy policy applies | USA | DoH: `https://doh.opendns.com/dns-query`<br>DoT: `dns.opendns.com` |
| [**AdGuard DNS**](https://adguard-dns.io/) | `94.140.14.14`<br>`94.140.15.15` | `2a10:50c0::ad1:ff`<br>`2a10:50c0::ad2:ff` | Ads, trackers, and phishing sites are blocked by default.<br>Family protection: `94.140.14.15`, `94.140.15.16`.<br>No filtering: `94.140.14.140`, `94.140.14.141`.<br>Custom rules with a private DNS server (AdGuard DNS Private) | The public service doesn't process personal data. Only aggregated metrics and an anonymous list of domains from the last 24 hours are kept | Cyprus | DoH: `https://dns.adguard-dns.com/dns-query`<br>DoT: `dns.adguard-dns.com` |
| [**Yandex DNS**](https://dns.yandex.com/) | `77.88.8.8`<br>`77.88.8.1` | `2a02:6b8::feed:0ff`<br>`2a02:6b8:0:1::feed:0ff` | Basic mode with no filtering.<br>Safe (malware and fraudulent sites): `77.88.8.88`, `77.88.8.2`.<br>Family (adult content as well): `77.88.8.7`, `77.88.8.3` | The service documentation doesn't specify a retention period. Yandex's general privacy policy applies | Russia | DoH: `https://common.dot.dns.yandex.net/dns-query`<br>DoT: `common.dot.dns.yandex.net` |
| [**NextDNS**](https://nextdns.io/) | Assigned individually in your account | Assigned individually in your account | Fully customizable: ads, trackers, malware, parental controls, custom blocklists and allowlists | No logs without an account. With an account, you can turn logs off or choose a retention period (from 1 hour to 2 years) and a storage location (USA, EU, UK, Switzerland) | USA | DoH: `https://dns.nextdns.io/<ID>`<br>DoT: `<ID>.dns.nextdns.io` |
| [**Control D**](https://controld.com/) | `76.76.2.0`<br>`76.76.10.0` | `2606:1a40::`<br>`2606:1a40:1::` | Free profiles with separate addresses: malware (`76.76.2.1`), ads and trackers (`76.76.2.2`), family-friendly (`76.76.2.4`).<br>Custom filtering in the paid version | Free resolvers don't store IP addresses, timestamps, or the queries themselves. Logs are kept only in the paid version with custom profiles | Canada | DoH: `https://freedns.controld.com/p0`<br>DoT: `p0.freedns.controld.com` |
| [**CleanBrowsing**](https://cleanbrowsing.org/) | `185.228.168.168`<br>`185.228.169.168` | `2a0d:2a00:1::`<br>`2a0d:2a00:2::` | The main addresses use the family filter.<br>Adult content only: `185.228.168.10`, `185.228.169.11`.<br>Malware only: `185.228.168.9`, `185.228.169.9` | In the free version, IP addresses and other personal data are not recorded. Anonymized query data may be kept indefinitely | USA | DoH: `https://doh.cleanbrowsing.org/doh/family-filter/`<br>DoT: `family-filter-dns.cleanbrowsing.org` |
| [**Mullvad DNS**](https://mullvad.net/en/help/dns-over-https-and-dns-over-tls) | `194.242.2.2` (DoH/DoT only) | `2a07:e340::2` (DoH/DoT only) | No filtering by default.<br>Separate servers with different blocking sets: ads and trackers (`adblock.dns.mullvad.net`), malware, social media, adult content, gambling | DNS queries are not logged | Sweden | DoH: `https://dns.mullvad.net/dns-query`<br>DoT: `dns.mullvad.net` |

> **Note:** Yandex DNS complies with Russian law and doesn't resolve websites blocked in Russia. Services from other jurisdictions may be unavailable or unstable in some countries.

This information is current as of November 2026. Addresses and policies may change, so check the provider's official documentation before you configure anything.

## How to test DNS server speed

Before you change your DNS provider, you can test the speed of the resolvers you're considering. Speed depends on your location, your ISP's routing, and the current network load, so it's best not to rely on rankings and averages published online.

The easiest way to measure speed for your device is right in your browser, with services that automatically test resolver response times, such as [DNS Speed Test](https://dnsspeedtest.online/) or [Control D DNS Speed Test](https://controld.com/tools/dns-speed-test).

## How to set up DNS on your device

You can set up DNS on an individual device or for your whole home network through your router. Below are instructions for Android and Windows 11.

### Android

Private DNS is available on Android 9 and later. The setting applies to all of the phone's connections, both Wi-Fi and mobile data.

To change your DNS provider on Android:

1. Open **Settings**.
2. Tap **Network & internet**. Depending on the phone manufacturer, this item may be called something else, for example, **Connections** > **More connection settings** on Samsung phones. If you can't find it, search for `Private DNS` in Settings.
3. Tap **Private DNS**.
4. Select **Private DNS provider hostname**.
5. Enter the [DoT hostname](#dns-table-en) of your chosen provider, for example, `dns.google`.
6. Tap **Save**.

To make sure the setting works, open a DNS test service in your browser, such as [browserleaks.com/dns](https://browserleaks.com/dns) or [dnsleaktest.com](https://www.dnsleaktest.com/). It shows which DNS servers handle your queries: your chosen provider should be listed. If you see a "No internet access" message after saving, check that you entered the hostname correctly.

To restore the default settings, select **Automatic** or **Off** under **Private DNS**.

### Windows 11

There are several ways to change your DNS provider in Windows 11. The easiest is through the **Settings** app: it's the only place where you can turn on DNS encryption (DoH). You can also set DNS in the network adapter properties in Control Panel or from the command line; these methods also work in Windows 10.

To change your DNS provider in **Settings**:

1. Select **Start** > **Settings**, or press **Win + I**.
2. Select **Network & internet**.
3. Select the connection type you want to configure: **Wi-Fi** or **Ethernet** (wired connection).
4. For **Wi-Fi**, select **Hardware properties** to apply the settings to all wireless networks. To apply them to a single network only, select **Manage known networks**, select the network, and then open its properties.
5. Next to **DNS server assignment**, select **Edit**.
6. In the **Edit DNS settings** window, change **Automatic (DHCP)** to **Manual**.
7. Turn on **IPv4**, **IPv6**, or both.
8. Enter the provider's [addresses](#dns-table-en) in **Preferred DNS** and **Alternate DNS** for each protocol you turned on.
9. To turn on encryption, configure it under each address. The option names depend on your version of Windows 11:
   * In current versions, set **DNS over HTTPS** to **On (manual template)** and paste the provider's [DoH address](#dns-table-en) into the **DNS over HTTPS template** field. For some well-known services, such as Google, Cloudflare, and Quad9, you can select **On (automatic template)** instead, and Windows will fill in the address for you. To prevent queries from being sent unencrypted when the server is unavailable, turn off **Fallback to plaintext**.
   * In early versions (21H2), set **Preferred DNS encryption** and **Alternate DNS encryption** to **Encrypted only (DNS over HTTPS)**. These versions support encryption only for well-known services; you can't enter a custom template.
10. Select **Save**.

To make sure the setting works, open a DNS test service in your browser, such as [browserleaks.com/dns](https://browserleaks.com/dns): your chosen provider should be listed among the servers. If you turned on encryption, the addresses under **DNS server assignment** are marked **(Encrypted)**.

To restore the default settings, select **Edit** next to **DNS server assignment** and choose **Automatic (DHCP)**.
