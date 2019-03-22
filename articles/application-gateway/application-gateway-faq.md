---
title: Часто задаваемые вопросы о шлюзе приложений Azure
description: На этой странице содержатся ответы на часто задаваемые вопросы о шлюзе приложений Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: f549f9c612797c1c956d6921fe4898a5f8bee9e6
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319420"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Часто задаваемые вопросы о шлюзе приложений

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Общие сведения

### <a name="what-is-application-gateway"></a>Что такое шлюз приложений?

Шлюз приложений Azure — это контроллер доставки приложений (ADC) как услуга, предлагающий различные возможности балансировки нагрузки уровня 7 для приложений. Это высокодоступная и масштабируемая служба, которая полностью управляется Azure.

### <a name="what-features-does-application-gateway-support"></a>Какие функции поддерживает Шлюз приложений?

Шлюз приложений поддерживает автоматическое масштабирование, разгрузку SSL и прямой режим связи SSL, брандмауэр веб-приложения, сопоставление сеансов на основе файлов cookie, маршрутизацию на основе URL-пути, размещение нескольких сайтов и другие функции. Полный список поддерживаемых функций см. в статье [Что такое Шлюз приложений Azure?](application-gateway-introduction.md)

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Какова разница между Шлюзом приложений и Azure Load Balancer?

Шлюз приложений — балансировщик нагрузки уровня 7, это означает, что он работает только для веб-трафика (HTTP/HTTPS и WebSocket и HTTP/2). Он поддерживает такие функции, как завершение SSL-подключений, сопоставление сеансов на основе файлов cookie и циклический перебор для распределения нагрузки трафика. Load Balancer распределяет нагрузку трафика на уровне 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Какие протоколы поддерживает Шлюз приложений?

Шлюз приложений поддерживает протоколы HTTP, HTTPS, HTTP/2 и WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Как Шлюз приложений поддерживает протокол HTTP/2?

См. в разделе [поддержка HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) чтобы узнать, как шлюз приложений поддерживает протокол HTTP/2.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Какие ресурсы сейчас поддерживаются как часть серверного пула?

См. в разделе [поддерживается серверным ресурсам](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) чтобы узнать, какие ресурсы поддерживаются шлюзом приложений.

### <a name="what-regions-is-the-service-available-in"></a>В каких регионах доступна служба?

Шлюз приложений доступен во всех регионах глобальной платформы Azure. Он также доступен в [Azure China 21Vianet](https://www.azure.cn/) и [Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Это специальное развертывание для подписки или общее для всех клиентов?

Шлюз приложений — это специальное развертывание в вашей виртуальной сети.

### <a name="is-http-https-redirection-supported"></a>Поддерживается ли перенаправление протоколов HTTP -> HTTPS?

Перенаправление поддерживается. Дополнительные сведения см. в статье [Общие сведения о перенаправлении для Шлюза приложений](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>В каком порядке обрабатываются прослушиватели?

См. в разделе [порядок обработки прослушиватели](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Где найти IP-адрес и DNS-имя Шлюза приложений?

При использовании общедоступного IP-адреса в качестве конечной точки эти сведения вы можете найти в ресурсах общедоступного IP-адреса или на странице обзора для шлюза приложений на портале. Для внутренних IP-адресов эту информацию можно найти на странице обзора.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Что такое Keep-Alive timeout и параметр времени ожидания простоя TCP на шлюзе приложений?

Keep-Alive задержки в номере SKU v1-120 в секунду Keep-Alive задержки в номере SKU v2-время ожидания простоя TCP 75 в секунду — 4 мин. по умолчанию на сервере переднего плана виртуального IP-адреса шлюза приложений.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Меняются ли IP-адрес и DNS-имя в течение времени существования Шлюза приложений?

Виртуальный IP-адрес может измениться, если шлюз приложений остановлен и запущен. DNS-имя, связанное со шлюзом приложений, не меняется никогда. По этой причине мы рекомендуем использовать псевдоним CNAME и настроить его для DNS-адреса шлюза приложений.

### <a name="does-application-gateway-support-static-ip"></a>Поддерживает ли Шлюз приложений статические IP-адреса?

Да, номер SKU Шлюза приложений версии 2 поддерживает статические общедоступные IP-адреса. Номер SKU версии 1 поддерживает статические внутренние IP-адреса.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Поддерживает ли Шлюз приложений несколько общедоступных IP-адресов в шлюзе?

Шлюз приложений поддерживает только один общедоступный IP-адрес.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Каким должен быть размер подсети Шлюза приложений?

См. в разделе [рекомендации по выбору размера подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) знать размер подсети, необходимых для развертывания.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>В. Можно ли развернуть более одного ресурса шлюза приложений в одной подсети?

Да, помимо нескольких экземпляров конкретного развертывания Шлюза приложений вы можете подготовить другой уникальный ресурс Шлюза приложений в существующей подсети, которая уже содержит другой ресурс Шлюза приложений.

Смешанное использование Standard_v2 и шлюза приложений (ценовая категория "Стандартный") в той же подсети не поддерживается.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Поддерживает ли Шлюз приложений заголовки X-Forwarded-For?

Да. См. в разделе [изменения, чтобы запросить](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) Дополнительные сведения о заголовке x-forwarded-for, поддерживаемые шлюзом приложений.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Сколько времени требуется для развертывания шлюза приложений? Работает ли Шлюз приложений во время обновления?

Подготовка новых развертываний номера SKU Шлюза приложений версии 1 может занять до 20 минут. Изменения в размере или числе экземпляров не нарушают работу шлюза приложений, он остается активным во время обновления.

Подготовка развертываний номера SKU версии 2 может занять около 5–6 минут.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange server может использоваться в качестве серверной части с помощью шлюза приложений?

Нет, шлюз приложений поддерживает протоколы электронной почты, такие как SMTP, IMAP и POP3. 

## <a name="performance"></a>Производительность

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Каким образом Шлюз приложений поддерживает высокий уровень доступности и масштабируемость?

Если развернуто два или больше экземпляров, номер SKU Шлюза приложений версии 1 поддерживает сценарии с высоким уровнем доступности. Azure распределяет эти экземпляры по доменам сбоя и обновления, чтобы гарантировать, что все экземпляры не завершатся ошибкой в одно и то же время. Номер SKU версии 1 поддерживает масштабируемость путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

Номер SKU версии 2 автоматически гарантирует, что новые экземпляры распределяются между доменами сбоя и доменами обновления. Если вы выбрали избыточность между зонами, последние экземпляры также распределяются по зонам доступности, что обеспечивает устойчивость зон к сбоям.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Как добиться сценария аварийного восстановления в центрах обработки данных с помощью Шлюза приложений?

Клиенты могут использовать диспетчер трафика для распределения трафика между несколькими шлюзами приложений в разных центрах обработки данных.

### <a name="is-autoscaling-supported"></a>Поддерживается ли автоматическое масштабирование?

Да, номер SKU Шлюза приложений версии 2 поддерживает автоматическое масштабирование. Дополнительные сведения см. в статье [Автоматическое масштабирование и Шлюз приложений, избыточный между зонами (общедоступная предварительная версия)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Вызывает ли простой увеличение или уменьшение масштаба, выполненное вручную?

Простой отсутствует. Экземпляры распределяются через домены обновления и домены сбоя.

### <a name="does-application-gateway-support-connection-draining"></a>Поддерживает ли Шлюз приложений фильтрацию подключений?

Да. Вы можете настроить фильтрацию подключений, чтобы изменить участников во внутреннем пуле без прерывания. Это позволит продолжить передачу данных по имеющимся подключениям в предыдущий пункт назначения, пока подключение не будет закрыто или не истечет настроенное время ожидания. Этот фильтр подключений ожидает завершения только текущих активных соединений. Шлюз приложений не учитывает состояние сеанса приложения.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Можно ли изменить размер экземпляра со среднего на большой без прерывания?

Да, можно. Azure распределяет экземпляры по доменам сбоя и обновления, чтобы гарантировать, что все экземпляры не завершатся ошибкой в одно и то же время. Шлюз приложений поддерживает масштабирование путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

## <a name="configuration"></a>Параметр Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Всегда ли Шлюз приложений развертывается в виртуальной сети?

Да, шлюз приложений всегда развертывается в подсети виртуальной сети. Эта подсеть может содержать только шлюзы приложений. См. в разделе [требования к виртуальной сети и подсети](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) приведены рекомендации по подсети для шлюза приложений.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Может взаимодействовать шлюз приложений с экземплярами за пределами виртуальной сети, в которой он находится, или за ее пределами подписку, которую он находится в?

Шлюз приложений может взаимодействовать с экземплярами за пределами виртуальной сети, он находится в или вне подписку, которую он находится, до тех пор, пока есть IP-подключения. Если вы планируете использовать внутренние IP-адреса в качестве участников серверного пула, потребуется [пиринговая связь между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md) или [VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Можно ли развернуть еще что-нибудь в подсети шлюза приложений?

Нет, но вы можете развернуть другие шлюзы приложений в подсети.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Поддерживаются ли группы безопасности сети в подсети шлюза приложений?

См. в разделе [ограничения групп безопасности сети в подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-supported-on-the-application-gateway-subnet) Дополнительные сведения о группах безопасности сети, поддерживается в подсети шлюза приложений.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Поддерживаются ли определяемые пользователем маршруты в подсети шлюза приложений?

См. в разделе [ограничения определяемые пользователем маршруты](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) Дополнительные сведения об определяемых пользователем маршрутов, который поддерживается в подсети шлюза приложений.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Какие у шлюза приложений ограничения? Можно ли увеличить предельные значения?

Сведения об ограничениях см. в разделе [Ограничения шлюза приложений](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Можно ли использовать Шлюз приложений одновременно для внешнего и внутреннего трафика?

Да, Шлюз приложений может иметь один внутренний IP-адрес и один внешний.

### <a name="is-vnet-peering-supported"></a>Поддерживается ли пиринговая связь между виртуальными сетями?

Да, поддерживается, что положительно сказывается на балансировке трафика в других виртуальных сетях.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Можно ли взаимодействовать с локальными серверами, если они подключены через туннели ExpressRoute или VPN?

Да, при условии, что разрешен трафик.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Может ли один серверный пул обслуживать несколько приложений, используя разные порты?

Поддерживается архитектура микрослужб. Вам потребуется настроить несколько параметров HTTP для проверки различных портов.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Поддерживают ли пользовательские пробы подстановочные знаки или регулярные выражения в данных ответа?

Нет, не поддерживают.

### <a name="how-are-rules-processed"></a>Как обрабатываются правила?

См. в разделе [порядок обработки правил](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) чтобы понять, как правила маршрутизации обрабатываются в шлюзе приложений.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Что означает поле "Узел" для пользовательских проб?

Это поле указывает имя узла, куда необходимо отправить пробу. Применяется только в случае, когда в шлюзе приложений настроено многосайтовое подключение. В противном случае используется значение 127.0.0.1. Это значение отличается от имени узла виртуальной машины и имеет следующий формат: \<протокол\>://\<узел\>:\<порт\>\<путь\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Можно ли добавить в список разрешений доступ Шлюза приложений к нескольким исходным IP-адресам?

Да. См. в разделе [ограничить доступ для определенных IP-адреса источника](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) понять, как убедиться, что только список разрешенных IP-адреса источника сможете получить доступ к шлюзу приложений.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Можно ли использовать один и тот же порт для прослушивателей общедоступных и частных сетей?

Нет, такая возможность не поддерживается.

## <a name="configuration---ssl"></a>Конфигурация — SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Какие сертификаты поддерживает Шлюз приложений?

Поддерживаются самозаверяющие сертификаты, сертификаты ЦС, сертификаты высокой Надежности и групповые.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Какие текущие комплекты шифров поддерживает Шлюз приложений?

Ниже представлены текущие комплекты шифров, поддерживаемые Шлюзом приложений. Дополнительные сведения о настройке параметров SSL см. в статье [Настройка версий политики SSL и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Поддерживает ли Шлюз приложений повторное шифрование трафика к внутренней службе?

Да, шлюз поддерживает разгрузку SSL и сквозной режим связи SSL, повторно шифрующие трафик к внутренней службе.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Можно ли настроить политику SSL для управления версиями протокола SSL?

Да, вы можете настроить шлюз приложений, чтобы запретить версии TLS 1.0, TLS 1.1 и TLS 1.2. Протоколы SSL версий 2.0 и 3.0 уже отключены по умолчанию, и их настройки нельзя изменить.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Можно ли настроить комплекты шифров и порядок политик?

Да, имеется возможность [настроить комплекты шифров](application-gateway-ssl-policy-overview.md). При определении пользовательской политики необходимо включить хотя бы один из следующих комплектов шифров. Шлюз приложений использует SHA256 для управления серверной частью.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Какое количество SSL-сертификатов поддерживается?

Поддерживается до 100 SSL-сертификатов.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Сколько сертификатов проверки подлинности поддерживается для повторного шифрования внутренней службы?

Поддерживается до 10 сертификатов. По умолчанию — 5.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Предусмотрена ли в Шлюзе приложений встроенная интеграция с Azure Key Vault?

Нет, он не интегрируется с этим хранилищем.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Как настроить HTTPS-прослушивателей для сайтов .com и .net? 

Несколько доменов маршрутизации на основе (на основе узла), можно создать прослушиватели для нескольких сайтов, выберите в качестве протокола в конфигурации прослушивателя HTTPS и связать прослушиватели с правилами маршрутизации. Дополнительные сведения см. в разделе [размещение нескольких сайтов с помощью шлюза приложений](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Конфигурация — брандмауэр веб-приложения (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>Все ли функции SKU ценовой категории "Стандартный" поддерживаются в номере SKU WAF?

Да, доступны.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Какую версию CRS поддерживает Шлюз приложений?

Шлюз приложений поддерживает [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) и CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Как выполнять мониторинг WAF?

WAF отслеживается с помощью журнала ведения диагностики. Дополнительные сведения см. в статье [Мониторинг работоспособности серверной части, ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Блокируется ли трафик в режиме обнаружения?

Нет, режим обнаружения только регистрирует трафик, активирующий правило WAF.

### <a name="can-i-customize-waf-rules"></a>Могу ли я настроить правила WAF?

Да, правила WAF можно настраивать. Дополнительные сведения см. в разделе [Настройка группы правил и правил WAF](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available"></a>Какие правила на сегодняшний день доступны?

В настоящее время WAF поддерживает CRS версий [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) и [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), что обеспечивает базовую защиту от наиболее важных уязвимостей, определенных открытым проектом безопасности веб-приложений (OWASP). Список 10 распространенных уязвимостей доступен [здесь](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

* Защита от внедрения кода SQL.

* Защита от межсайтовых сценариев.

* Защита от распространенных сетевых атак, в том числе от внедрения команд, несанкционированных HTTP-запросов, разделения HTTP-запросов и атак с включением удаленного файла.

* Защита от нарушений протокола HTTP.

* Защита от аномалий протокола HTTP, например отсутствия агента пользователя узла и заголовков accept.

* Защита от программ-роботов, программ-обходчиков и сканеров.

* Обнаружение распространенных неправильных конфигураций приложений (Apache, IIS и т. д.).

### <a name="does-waf-also-support-ddos-prevention"></a>Поддерживает ли WAF также защиту от атак DDoS?

Да. Вы можете включить защиту от атак DDos в виртуальной сети, где развертывается шлюз приложений. Это гарантирует, что виртуальный IP-адрес шлюза приложений также защищен с помощью службы Защиты от атак DDoS.

## <a name="diagnostics-and-logging"></a>Диагностика и ведение журнала

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Какие типы журналов доступны для Шлюза приложений?

Для шлюза приложений доступны три журнала. Дополнительные сведения о журналах и других диагностических возможностях см. в статье [Работоспособность серверной части, журналы диагностики и метрики для Шлюза приложений](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog**. В журнале доступа содержится каждый запрос, переданный во внешний интерфейс шлюза приложений. Здесь указаны такие данные, как IP-адрес вызывающего объекта, запрашиваемый URL-адрес, задержка ответа, код возврата, входящие и исходящие байты. Данные для журнала доступа собираются каждые 300 секунд. Этот журнал содержит одну запись для каждого экземпляра шлюза приложений.
* **ApplicationGatewayPerformanceLog**. В журнал производительности записываются сведения о производительности каждого экземпляра, включая общее число обработанных запросов, пропускную способность в байтах, число неудачных запросов, а также число работоспособных и неработоспособных серверных экземпляров.
* **ApplicationGatewayFirewallLog**. Журнал брандмауэра содержит запросы, которые были зарегистрированы в режиме обнаружения или предотвращения на шлюзе приложений, на котором настроен брандмауэр веб-приложения.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Как узнать, работоспособны ли участники серверного пула?

Вы можете использовать командлет PowerShell `Get-AzApplicationGatewayBackendHealth` или проверить работоспособность через портал, ознакомившись со сведениями в статье [Мониторинг работоспособности серверной части, ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Что такое политика хранения журналов диагностики?

Журналы диагностики находятся в учетных записях хранения клиентов, и клиенты могут задать политику хранения в зависимости от своих предпочтений. Журналы диагностики также можно отправить в концентратор событий или журналы Azure Monitor. Дополнительные сведения см. в статье [Работоспособность серверной части, журналы диагностики и метрики для Шлюза приложений](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Как получить журналы аудита для Шлюза приложений?

Для шлюза приложений доступны журналы аудита. На портале, щелкните **журнал действий** в колонке меню шлюза приложений для доступа к журналу аудита. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Можно ли настроить оповещения для Шлюза приложений?

Да, Шлюз приложений поддерживает оповещения. Оповещения настраиваются на основе метрик. Дополнительные сведения о метриках Шлюза приложений см. в разделе [Метрики](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics). Дополнительные сведения об оповещениях см. в статье [Обзор оповещений в Microsoft Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Как анализировать статистику трафика для Шлюза приложений?

Вы можете просматривать и анализировать журналы доступа с помощью ряда механизмов: журналы Azure Monitor, Excel, Power BI и т. д.

Мы также опубликовали шаблон Resource Manager, который устанавливает и запускает популярный анализатор журналов [GoAccess](https://goaccess.io/) для журналов доступа шлюза приложений. GoAccess предоставляет ценную статистику трафика HTTP, такую как уникальные посетители, запрошенные файлы, узлы, операционные системы, браузеры, коды состояния HTTP и многое другое. Дополнительные сведения см. в [файле сведений в папке шаблона Resource Manager на GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Запрос работоспособности серверной части возвращает неизвестное состояние. Чем это может быть вызвано?

Самая распространенная причина — доступ к серверной части заблокирован группой безопасности, пользовательский DNS-сервер, или у вас есть определяемый пользователем Маршрут в подсети шлюза приложений. Дополнительные сведения см. в статье [Работоспособность серверной части, журналы диагностики и метрики для Шлюза приложений](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Шлюзе приложений см. в [этой статье](overview.md).
