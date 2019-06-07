---
title: Часто задаваемые вопросы о шлюзе приложений Azure
description: Ответы на часто задаваемые вопросы о шлюзе приложений Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 25779cd62f70df41a7734d2f8097504923dcb3a3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430885"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Часто задаваемые вопросы о шлюзе приложений

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ниже приведены часто задаваемые вопросы и ответы о шлюзе приложений Azure.

## <a name="general"></a>Общие сведения

### <a name="what-is-application-gateway"></a>Что такое шлюз приложений?

Шлюз приложений Azure предоставляет контроллер доставки приложений (ADC) как услугу. Он предлагает различные возможности уровня 7 балансировки нагрузки для ваших приложений. Эта служба является высокодоступных, масштабируемых и полностью управляются Azure.

### <a name="what-features-does-application-gateway-support"></a>Какие функции поддерживает Шлюз приложений?

Шлюз приложений поддерживает автоматическое масштабирование, разгрузки SSL и -сквозного режима связи SSL, брандмауэр веб-приложения (WAF), соответствие сеансу на основе файлов cookie, URL-адрес маршрутизации на основе пути, многосайтового размещения и другие функции. Полный список поддерживаемых функций см. в статье [Что такое Шлюз приложений Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Отличия между шлюзом приложений и подсистемы балансировки нагрузки Azure

Шлюз приложений — балансировщик нагрузки уровня 7, это означает, что он работает только с веб-трафика (HTTP, HTTPS, WebSocket и HTTP/2). Он поддерживает возможности, такие как завершение SSL, сходство сеансов на основе файлов cookie и циклический перебор для балансировки нагрузки трафика. Загрузите Balancer балансирует нагрузку трафика уровня 4 (TCP или UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Какие протоколы поддерживает Шлюз приложений?

Шлюз приложений поддерживает протоколы HTTP, HTTPS, HTTP/2 и WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Как Шлюз приложений поддерживает протокол HTTP/2?

См. в разделе [поддержка HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Какие ресурсы поддерживаются как часть серверного пула?

См. в разделе [поддерживается серверным ресурсам](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>В каких регионах доступна шлюза приложений?

Шлюз приложений доступен во всех регионах глобальной платформы Azure. Он также доступен в [21Vianet в Azure для Китая](https://www.azure.cn/) и [Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>— Это развертывание выделенных для моей подписки или общее для всех клиентов?

Шлюз приложений — это специальное развертывание в вашей виртуальной сети.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Шлюз приложений поддерживает перенаправление HTTP-HTTPS?

Перенаправление поддерживается. См. в разделе [Общие сведения о перенаправлении для шлюза приложений](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>В каком порядке обрабатываются прослушиватели?

См. в разделе [порядок обработки прослушивателя](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Где найти IP-адрес приложения шлюза и DNS?

Если вы используете общедоступный IP-адрес в качестве конечной точки, вы найдете сведения IP-адрес и DNS на открытый ресурс IP-адреса. Или найдите его на портале на странице "Обзор" для шлюза приложений. Если вы используете внутренние IP-адреса, сведения на странице "Обзор".

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Что такое параметры времени ожидания проверки активности и времени ожидания простоя TCP

 В SKU шлюза приложений версии 1 время ожидания активности — 120 секунд. Время ожидания активности для v2 SKU — 75 секунд. Время ожидания простоя TCP является значением по умолчанию 4 минуты, на внешний интерфейс виртуального IP-адрес (VIP) шлюза приложений.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP-адрес или DNS-имя изменяется в течение времени существования шлюза приложений?

Виртуальный IP-адрес можно изменить, если остановить и запустить шлюз приложений. Но DNS-имя, связанное со шлюзом приложений не меняется в течение времени существования шлюза. Так как DNS-имя не меняются, следует использовать псевдоним CNAME и настроить его для DNS-адрес шлюза приложений.

### <a name="does-application-gateway-support-static-ip"></a>Поддерживает ли Шлюз приложений статические IP-адреса?

Да, номер SKU шлюза приложений версии 2 поддерживает статические общедоступные IP-адреса. Номер SKU версии 1 поддерживает статические внутренние IP-адреса.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Поддерживает ли Шлюз приложений несколько общедоступных IP-адресов в шлюзе?

Шлюз приложений поддерживает только один общедоступный IP-адрес.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Каким должен быть размер подсети Шлюза приложений?

См. в разделе [рекомендации по выбору размера подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Можно ли развернуть более одного ресурса шлюза приложений в одной подсети?

Да. В дополнение к несколько экземпляров данного развертывания шлюза приложений вы можете подготовить другого ресурса шлюза приложений с существующей подсетью, содержащий на другой ресурс шлюза приложений.

Подсеть не поддерживает Standard_v2 и стандартный шлюз приложений друг с другом.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Поддерживает ли Шлюз приложений заголовки X-Forwarded-For?

Да. См. в разделе [изменения запроса](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Сколько времени занимает развертывания шлюза приложений? Шлюз приложений будет работать в процессе обновления?

Подготовка новых развертываний номера SKU Шлюза приложений версии 1 может занять до 20 минут. Изменения размера экземпляров или count не может нарушить работу системы, а шлюз остается активным в течение этого времени.

Подготовка развертываний, использующих v2 SKU может занять до 6 минут.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Можно использовать Exchange Server в качестве серверной части с помощью шлюза приложений?

№ Шлюз приложений не поддерживает протоколы электронной почты, например SMTP, IMAP и POP3. 

## <a name="performance"></a>Производительность

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Каким образом Шлюз приложений поддерживает высокий уровень доступности и масштабируемость?

Номер SKU v1 шлюз приложений поддерживает сценарии высокого уровня доступности, при развертывании двух или более экземпляров. Azure распределяет эти экземпляры по доменам сбоя и обновления, чтобы убедиться, что экземпляры у всех ошибок, в то же время. Номер SKU версии 1 поддерживает масштабируемость путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

Номер SKU версии 2 автоматически гарантирует, что новые экземпляры распределяются между доменами сбоя и доменами обновления. При желании избыточность зон новые экземпляры также распределяются в зонах доступности, чтобы предложить зональные отказоустойчивость.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Как добиться сценария аварийного восстановления в центрах обработки данных с помощью шлюза приложений?

Используйте диспетчер трафика для распределения трафика между несколькими шлюзами приложений в разных центрах обработки данных.

### <a name="does-application-gateway-support-autoscaling"></a>Шлюз приложений поддерживает автоматическое масштабирование?

Да, номер SKU Шлюза приложений версии 2 поддерживает автоматическое масштабирование. Дополнительные сведения см. в разделе [автоматическое масштабирование и избыточность в пределах зоны шлюза приложений](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Не ручное масштабирование и уменьшить время простоя причина?

№ Экземпляры распределяются через домены обновления и домены сбоя.

### <a name="does-application-gateway-support-connection-draining"></a>Поддерживает ли Шлюз приложений фильтрацию подключений?

Да. Фильтрация для изменения элементов в пределах серверного пула без прерывания подключений можно настроить. Эта настройка позволяет вам продолжить отправку существующие подключения к назначению предыдущих либо до этого подключения закрывает или настраиваемое время ожидания истекает. Фильтрация подключений ожидает только текущих активных подключений для завершения. Шлюз приложений не будет учитывать состояние сеанса приложения.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Можно ли изменить размер экземпляра со среднего на большой без прерывания?

Да. Azure распределяет экземпляры по доменам сбоя и обновления, чтобы убедиться, что экземпляры не завершатся ошибкой все одновременно. Шлюз приложений поддерживает масштабирование путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

## <a name="configuration"></a>Параметр Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Всегда ли Шлюз приложений развертывается в виртуальной сети?

Да. Шлюз приложений всегда развертывается в подсети виртуальной сети. Эта подсеть может содержать только шлюзы приложений. Дополнительные сведения см. в разделе [требования к виртуальной сети и подсети](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Может взаимодействовать шлюз приложений с экземплярами за пределами виртуальной сети или вне его подписки?

До тех пор, пока компьютер подключен к IP-адрес, шлюз приложений может взаимодействовать с экземплярами за пределами виртуальной сети, он находится в. Шлюз приложений также могут взаимодействовать с экземплярами за пределами подписки, в которой он находится. Если вы планируете использовать внутренние IP-адреса в качестве участников серверного пула, использовать [пиринга виртуальных сетей](../virtual-network/virtual-network-peering-overview.md) или [VPN-шлюза Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Можно ли развернуть еще что-нибудь в подсети шлюза приложений?

№ Но вы можете развернуть другие шлюзы приложений в подсети.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Поддерживаются ли группы безопасности сети в подсети шлюза приложений?

См. в разделе [группы безопасности сети в подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Подсети шлюза приложений поддерживает определяемые пользователем маршруты?

См. в разделе [определяемые пользователем маршруты, поддерживаемые в подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Какие у шлюза приложений ограничения? Можно ли увеличить предельные значения?

См. в разделе [ограничения шлюза приложений](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Можно ли одновременно использовать шлюз приложений для внешнего и внутреннего трафика?

Да. Шлюз приложений поддерживает один внутренний IP-адрес и один внешний IP-адрес на шлюз приложений.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Шлюз приложений поддерживает пиринг виртуальных сетей?

Да. Пиринг виртуальных сетей помогает балансировать нагрузку в других виртуальных сетях.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Можно ли взаимодействовать с локальными серверами, если они подключены через туннели ExpressRoute или VPN?

Да, при условии, что разрешен трафик.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Можно один серверный пул обслуживать несколько приложений, используя разные порты?

Поддерживается архитектура Микрослужб. Для проверки различных портов, необходимо настроить несколько параметров HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Пользовательская проверка работоспособности поддерживают подстановочные знаки или регулярное выражение в данных ответа?

№ 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Как обрабатываются правила маршрутизации в шлюз приложений?

См. в разделе [порядок обработки правил](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Для пользовательских проб что поле Host обозначения?

Поле Host указывает имя, чтобы отправить пробу после настройки многосайтового на шлюзе приложений. В противном случае используйте "127.0.0.1». Это значение отличается от имени узла виртуальной машины. Его формат \<протокола\>://\<узла\>:\<порт\>\<путь\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Разрешить доступ шлюза приложений к только несколько исходных IP-адресов?

Да. См. в разделе [ограничить доступ для определенных IP-адреса источника](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Можно использовать тот же порт для прослушивателей общедоступные и частные выходом?

№

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Есть ли рекомендации, доступные для переноса с v1 SKU на v2 SKU?

Да. Дополнительные сведения см., [перенос шлюза приложений Azure и брандмауэра веб-приложения с v1 на v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Конфигурация — SSL

### <a name="what-certificates-does-application-gateway-support"></a>Какие сертификаты поддерживает шлюз приложений?

Шлюз приложений поддерживает самозаверяющих сертификатов, центров сертификации (ЦС), сертификаты, сертификаты высокой надежности (EV) и групповые сертификаты.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Какие наборы шифров шлюз приложений поддерживает?

Шлюз приложений поддерживает следующие комплекты шифров. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Сведения о настройке параметров SSL, см. в разделе [Настройка версий политики SSL и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Шлюз приложений поддерживает шифрования трафика в серверную часть?

Да. Шлюз приложений поддерживает разгрузку SSL и -сквозного режима связи SSL, которые повторно трафика в серверную часть.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Можно настроить политику SSL для версий протокола SSL для элемента управления?

Да. Вы можете настроить шлюз приложений, чтобы запретить TLS1.0, TLS1.1 и TLS1.2. По умолчанию SSL 2.0 и 3.0 уже отключены и не настраивается.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Можно ли настроить комплекты шифров и порядок политик?

Да. В шлюзе приложений, вы можете [настроить комплекты шифров](application-gateway-ssl-policy-overview.md). Чтобы определить пользовательскую политику, включите хотя бы один из следующих комплектов шифров. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Шлюз приложений использует SHA256 для управления серверной частью.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Какое количество SSL-сертификаты поддерживает шлюз приложений?

Шлюз приложений поддерживает до 100 сертификатов SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Сколько сертификатов проверки подлинности для шифрования базы данных поддерживает шлюз приложений?

Шлюз приложений поддерживает до 10 сертификатов проверки подлинности. Значение по умолчанию — 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Интегрируется ли шлюз приложений в собственном коде с хранилищем ключей Azure?

Да, номер SKU шлюза приложений версии 2 поддерживает хранилище ключей. Дополнительные сведения см. в разделе [завершение запросов SSL с сертификатами в Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Как настроить HTTPS-прослушивателей для сайтов .com и .net? 

Несколько доменов маршрутизации на основе (на основе узла), можно создать многосайтового прослушиватели, настройте прослушиватели, использовать протокол HTTPS и связать прослушиватели с правилами маршрутизации. Дополнительные сведения см. в разделе [размещение нескольких сайтов с помощью шлюза приложений](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Конфигурация — брандмауэр веб-приложения (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Номера SKU WAF предлагает все функции, доступные в SKU "стандартный"?

Да. WAF поддерживает все функции в SKU "стандартный".

### <a name="which-crs-versions-does-application-gateway-support"></a>Какие версии CRS поддерживает шлюз приложений?

Шлюз приложений поддерживает [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) и CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Как выполнять мониторинг WAF?

Монитор WAF через ведение журналов диагностики. Дополнительные сведения см. в разделе [ведения журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Блокируется ли трафик в режиме обнаружения?

№ Режим обнаружения только регистрирует трафик, который активирует правило WAF.

### <a name="can-i-customize-waf-rules"></a>Могу ли я настроить правила WAF?

Да. Дополнительные сведения см. в разделе [WAF, настроить правила и группы правил](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Какие правила в настоящее время доступны для WAF?

Настоящее время WAF поддерживает CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) и [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Эти правила обеспечивают базовую защиту от наиболее Топ-10 уязвимостей, идентифицирует Open Web Application Security Project (OWASP): 

* Защита от внедрения кода SQL.
* Защита от межсайтовых сценариев
* Защита от распространенных сетевых атак, таких как внедрение команд, несанкционированных HTTP-ответа разделение, HTTP-запроса и атака с включением удаленного файла
* Защита от нарушений протокола HTTP.
* Защита от аномалий протокола HTTP, например отсутствия агента пользователя узла и заголовков accept.
* Защита от программ-роботов, программ-обходчиков и сканеров.
* Обнаружение распространенных неправильных конфигураций приложений (то есть, Apache, IIS и т. д.)

Дополнительные сведения см. в разделе [уязвимостей OWASP Топ-10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Поддерживает ли WAF защита от атак DDoS?

Да. Вы можете включить защиту от атак DDoS в виртуальной сети, где развертывается шлюз приложений. Этот параметр гарантирует, что служба защиты от атак DDoS Azure также защищает приложения шлюза виртуальный IP-адрес (VIP).

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Есть ли рекомендации, доступные для переноса с v1 SKU на v2 SKU?

Да. Дополнительные сведения см., [перенос шлюза приложений Azure и брандмауэра веб-приложения с v1 на v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Диагностика и ведение журнала

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Какие типы журналов предоставляет шлюз приложений?

Шлюз приложений предоставляет три журнала: 

* **ApplicationGatewayAccessLog**: В журнале доступа содержится каждый запрос на внешний интерфейс шлюза приложения. Они включают в себя IP-адрес вызывающей стороны, запрошенный URL-адрес, задержка ответа, код возврата и байт и выхода из системы. Журнала доступа собираются каждые 300 секунд. Он содержит одну запись для каждого шлюза приложений.
* **ApplicationGatewayPerformanceLog**: Журнал производительности записываются сведения о производительности для каждого шлюза приложений. Сведения включают пропускную способность в байтах, число, число невыполненных запросов, а также число работоспособных и неработоспособных серверной части экземпляров.
* **ApplicationGatewayFirewallLog**: Для шлюзов приложений, которые можно настроить с помощью WAF журнал брандмауэра содержит запросы, которые были зарегистрированы в режиме обнаружения или предотвращения режим.

Дополнительные сведения см. в разделе [серверной части работоспособности, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Как узнать, работоспособны ли участники серверного пула?

Проверка работоспособности с помощью командлета PowerShell `Get-AzApplicationGatewayBackendHealth` или портала. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Что такое политика хранения для журналов диагностики

Поток журналов диагностики в учетную запись хранения клиента. Клиенты могут задать политику хранения в зависимости от своих предпочтений. Журналы диагностики также могут отправляться в концентратор событий или журналы Azure Monitor. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Как получить журналы аудита для Шлюза приложений?

На портале в колонке меню шлюза приложений, выберите **журнал действий** получить доступ к журналу аудита. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Можно ли настроить оповещения для Шлюза приложений?

Да. В шлюзе приложений предупреждения настраиваются на основе метрик. Дополнительные сведения см. в разделе [метрику шлюза приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) и [получение уведомлений об оповещениях](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Как анализировать статистику трафика для Шлюза приложений?

Можно просматривать и анализировать журналы доступ несколькими способами. Используйте журналы Azure Monitor, Excel, Power BI и т. д.

Можно также использовать шаблон Resource Manager, который устанавливает и запускает популярного [GoAccess](https://goaccess.io/) журнала анализатора для журналов доступа шлюза приложений. GoAccess предоставляет ценные статистику трафика HTTP, например уникальных посетителей, запрашиваемых файлов, узлы, операционных систем, браузеров и коды состояния HTTP. Дополнительные сведения в GitHub, см. в разделе [файле сведений в папке шаблона Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Как можно вызвать работоспособности серверной части для возврата неизвестное состояние?

Как правило вы см. в разделе неизвестном состоянии при блокировании доступа к серверной части группы безопасности сети (NSG), пользовательский DNS-сервер, или определяемые пользователем маршрутизацию (UDR) в подсети шлюза приложений. Дополнительные сведения см. в разделе [серверной части работоспособности, ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о шлюзе приложений см. в разделе [что такое шлюз приложений Azure?](overview.md).
