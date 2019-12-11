---
title: Часто задаваемые вопросы о шлюзе приложений Azure
description: Найдите ответы на часто задаваемые вопросы о шлюзе приложений Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: c93198848058bad8c9af6903cc68253e71e2d668
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996669"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Часто задаваемые вопросы о шлюзе приложений

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ниже приведены часто задаваемые вопросы о шлюзе приложений Azure.

## <a name="general"></a>Общие сведения

### <a name="what-is-application-gateway"></a>Что такое шлюз приложений?

Шлюз приложений Azure предоставляет контроллер доставки приложений (ADC) в качестве службы. Она предлагает различные возможности балансировки нагрузки уровня 7 для приложений. Эта служба обладает высокой доступностью, масштабируемостью и полностью управляется Azure.

### <a name="what-features-does-application-gateway-support"></a>Какие функции поддерживает Шлюз приложений?

Шлюз приложений поддерживает автоматическое масштабирование, разгрузку SSL и сквозной протокол SSL, брандмауэр веб-приложения (WAF), сходство сеансов на основе файлов cookie, маршрутизацию на основе URL-пути, размещение с несколькими сайтами и другие компоненты. Полный список поддерживаемых функций см. в статье [Что такое Шлюз приложений Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Чем отличаются шлюз приложений и Azure Load Balancer?

Шлюз приложений — это подсистема балансировки нагрузки уровня 7, которая означает, что она работает только с веб-трафиком (HTTP, HTTPS, WebSocket и HTTP/2). Он поддерживает такие возможности, как завершение SSL, сходство сеансов на основе файлов cookie и циклический перебор трафика балансировки нагрузки. Load Balancer балансировки нагрузки трафика на уровне 4 (TCP или UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Какие протоколы поддерживает Шлюз приложений?

Шлюз приложений поддерживает протоколы HTTP, HTTPS, HTTP/2 и WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Как Шлюз приложений поддерживает протокол HTTP/2?

См. раздел [Поддержка HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Какие ресурсы поддерживаются как часть серверного пула?

См. раздел [Поддерживаемые серверные ресурсы](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>В каких регионах доступен шлюз приложений?

Шлюз приложений доступен во всех регионах глобальной платформы Azure. Он также доступен в Azure для [государственных организаций](https://azure.microsoft.com/overview/clouds/government/)( [21vianet](https://www.azure.cn/) ).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Это развертывание предназначено для моей подписки или доступно для всех клиентов?

Шлюз приложений — это специальное развертывание в вашей виртуальной сети.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Поддерживает ли шлюз приложений перенаправление HTTP-HTTPS?

Перенаправление поддерживается. См. раздел [Обзор перенаправления шлюза приложений](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>В каком порядке обрабатываются прослушиватели?

См. [порядок обработки прослушивателей](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Где найти IP-адрес и DNS шлюза приложений?

Если вы используете общедоступный IP-адрес в качестве конечной точки, вы найдете сведения об IP-адресах и DNS в ресурсе общедоступного IP-адреса. Или найдите его на портале на странице Обзор шлюза приложений. Если вы используете внутренние IP-адреса, найдите информацию на странице Обзор.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Каковы параметры времени ожидания проверки активности и времени ожидания простоя TCP?

 В SKU шлюза приложений версии 1 время ожидания поддержания активности составляет 120 секунд. Время ожидания проверки активности для SKU v2 составляет 75 секунд. Время ожидания простоя TCP составляет 4 минуты по умолчанию на интерфейсном виртуальном IP-адресе (VIP) шлюза приложений.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Изменится ли IP-адрес или DNS-имя в течение времени существования шлюза приложений?

В SKU шлюза приложений версии 1 виртуальный IP-адрес может измениться, если вы останавливаете и запускаете шлюз приложений. Но DNS-имя, связанное с шлюзом приложений, не изменяется в течение времени существования шлюза. Так как DNS-имя не меняется, следует использовать псевдоним CNAME и указать его DNS-адрес шлюза приложений. В SKU шлюза приложений версии 2 можно задать статический IP-адрес, поэтому IP-адреса и DNS-имена не будут меняться в течение времени существования шлюза приложений. 

### <a name="does-application-gateway-support-static-ip"></a>Поддерживает ли Шлюз приложений статические IP-адреса?

Да, SKU шлюза приложений версии 2 поддерживает статические общедоступные IP-адреса. Номер SKU версии 1 поддерживает статические внутренние IP-адреса.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Поддерживает ли Шлюз приложений несколько общедоступных IP-адресов в шлюзе?

Шлюз приложений поддерживает только один общедоступный IP-адрес.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Каким должен быть размер подсети Шлюза приложений?

См. раздел [рекомендации по размеру подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Можно ли развернуть более одного ресурса шлюза приложений в одной подсети?

Да. В дополнение к нескольким экземплярам данного развертывания шлюза приложений можно предоставить другой уникальный ресурс шлюза приложений для существующей подсети, содержащей другой ресурс шлюза приложений.

Одна подсеть не может одновременно поддерживать как Standard_v2, так и стандартный шлюз приложений.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Поддерживает ли Шлюз приложений заголовки X-Forwarded-For?

Да. См. статью об [изменениях в запросе](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Сколько времени занимает развертывание шлюза приложений? Будет ли шлюз приложений работать в процессе обновления?

Подготовка новых развертываний номера SKU Шлюза приложений версии 1 может занять до 20 минут. Изменения размера или количества экземпляров не нарушаются, и шлюз остается активным в течение этого времени.

В большинстве развертываний, использующих SKU версии 2, инициализация занимает около 6 минут. Однако это может занять больше времени в зависимости от типа развертывания. Например, развертывание в нескольких Зоны доступности с несколькими экземплярами может занять более 6 минут. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Можно ли использовать сервер Exchange Server в качестве серверного шлюза?

Нет. Шлюз приложений не поддерживает такие протоколы электронной почты, как SMTP, IMAP и POP3. 

## <a name="performance"></a>Ориентированное на производительность

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Каким образом Шлюз приложений поддерживает высокий уровень доступности и масштабируемость?

SKU шлюза приложений версии 1 поддерживает сценарии высокого уровня доступности при развертывании двух или более экземпляров. Azure распространяет эти экземпляры между доменами обновления и сбоя, чтобы гарантировать, что экземпляры не завершатся одновременно. Номер SKU версии 1 поддерживает масштабируемость путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

Номер SKU версии 2 автоматически гарантирует, что новые экземпляры распределяются между доменами сбоя и доменами обновления. При выборе избыточности зоны новейшие экземпляры также распределяются между зонами доступности, чтобы обеспечить устойчивость зональные к сбоям.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Разделы справки получить сценарий аварийного восстановления в центрах обработки данных с помощью шлюза приложений?

Используйте диспетчер трафика для распределения трафика между несколькими шлюзами приложений в разных центрах обработки данных.

### <a name="does-application-gateway-support-autoscaling"></a>Поддерживает ли шлюз приложений Автомасштабирование?

Да, номер SKU Шлюза приложений версии 2 поддерживает автоматическое масштабирование. Дополнительные сведения см. в статье [Автомасштабирование и избыточное в зону шлюз приложений](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Происходит ли ручное увеличение или уменьшение масштаба по причине простоя?

Нет. Экземпляры распределяются через домены обновления и домены сбоя.

### <a name="does-application-gateway-support-connection-draining"></a>Поддерживает ли Шлюз приложений фильтрацию подключений?

Да. Можно настроить сток подключений, чтобы изменить элементы в внутреннем пуле без прерывания работы. Дополнительные сведения см. в [разделе сток подключений в шлюзе приложений](overview.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Можно ли изменить размер экземпляра со среднего на большой без прерывания?

Да. Azure распределяет экземпляры между доменами обновления и сбоя, чтобы гарантировать, что экземпляры не будут завершаться сбоем одновременно. Шлюз приложений поддерживает масштабирование путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

## <a name="configuration"></a>Настройка

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Всегда ли Шлюз приложений развертывается в виртуальной сети?

Да. Шлюз приложений всегда развертывается в подсети виртуальной сети. Эта подсеть может содержать только шлюзы приложений. Дополнительные сведения см. в статье [требования к виртуальной сети и подсети](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Может ли шлюз приложений взаимодействовать с экземплярами вне своей виртуальной сети или за пределами своей подписки?

При наличии IP-подключения шлюз приложений может взаимодействовать с экземплярами за пределами виртуальной сети, в которой он находится. Шлюз приложений также может взаимодействовать с экземплярами вне подписки, в которой он находится. Если вы планируете использовать внутренние IP-адреса в качестве членов серверного пула, используйте [пиринг виртуальной сети](../virtual-network/virtual-network-peering-overview.md) или [шлюз VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Можно ли развернуть еще что-нибудь в подсети шлюза приложений?

Нет. Но вы можете развернуть другие шлюзы приложений в подсети.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Поддерживаются ли группы безопасности сети в подсети шлюза приложений?

См. раздел [группы безопасности сети в подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Поддерживает ли подсеть шлюза приложений определяемые пользователем маршруты?

См. раздел [определяемые пользователем маршруты, поддерживаемые в подсети шлюза приложений](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Какие у шлюза приложений ограничения? Можно ли увеличить предельные значения?

См. раздел [ограничения шлюза приложений](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Можно ли одновременно использовать шлюз приложений как для внешнего, так и для внутреннего трафика?

Да. Шлюз приложений поддерживает один внутренний IP-адрес и один внешний IP-адрес для шлюза приложений.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Поддерживает ли шлюз приложений пиринг виртуальных сетей?

Да. Пиринг виртуальных сетей обеспечивает балансировку нагрузки трафика в других виртуальных сетях.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Можно ли взаимодействовать с локальными серверами, подключенными с помощью виртуальных туннелей ExpressRoute или VPN?

Да, при условии, что разрешен трафик.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Может ли один серверный пул обслуживать множество приложений на разных портах?

Поддерживается архитектура микрослужб. Для проверки на разных портах необходимо настроить несколько параметров HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Поддерживают ли пользовательские зонды подстановочные знаки или регулярные выражения в ответных данных?

Нет. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Как правила маршрутизации обрабатываются в шлюзе приложений?

См. раздел [порядок правил обработки](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Что означают поля узла для пользовательских зондов?

В поле Host указывается имя для отправки зонда при настройке многосайтового шлюза приложений. В противном случае используйте "127.0.0.1". Это значение отличается от имени узла виртуальной машины. Его формат: \<протокол\>://\<узел\>:\<порт\>\<путь\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Можно ли разрешить шлюзу приложений доступ только к некоторым исходным IP-адресам?

Да. См. раздел [ограничение доступа к определенным исходным IP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)-адресам.

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Можно ли использовать один и тот же порт как для общедоступных, так и для частных прослушивателей?

Нет.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Доступны ли рекомендации для перехода с SKU версии v1 на SKU v2?

Да. Дополнительные сведения см. в разделе [Перенос шлюза приложений Azure и брандмауэра веб-приложения с версии v1 на v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Конфигурация — SSL

### <a name="what-certificates-does-application-gateway-support"></a>Какие сертификаты поддерживает шлюз приложений?

Шлюз приложений поддерживает самозаверяющие сертификаты, сертификаты центров сертификации, сертификаты высокой надежности (EV) и сертификаты с подстановочными знаками.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Какие наборы шифров поддерживают шлюз приложений?

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

Сведения о настройке параметров SSL см. в статье [Настройка версий политик SSL и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Поддерживает ли шлюз приложений перешифрование трафика на серверную часть?

Да. Шлюз приложений поддерживает разгрузку SSL и сквозной протокол SSL, который повторно шифрует трафик на серверную часть.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Можно ли настроить политику SSL для управления версиями протокола SSL?

Да. Шлюз приложений можно настроить для запрета TLS 1.0, TLS 1.1 и TLS 1.2. По умолчанию SSL 2,0 и 3,0 уже отключены и не могут быть настроены.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Можно ли настроить комплекты шифров и порядок политик?

Да. В шлюзе приложений можно [настроить комплекты шифров](application-gateway-ssl-policy-overview.md). Чтобы определить пользовательскую политику, включите по меньшей мере один из следующих комплектов шифров. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Шлюз приложений использует SHA256 для управления серверной частью.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Сколько SSL-сертификатов поддерживает шлюз приложений?

Шлюз приложений поддерживает до 100 SSL-сертификатов.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Сколько сертификатов проверки подлинности для перешифрования серверной части поддерживает шлюз приложений?

Шлюз приложений поддерживает до 100 сертификатов проверки подлинности.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Поддерживает ли шлюз приложений встроенную интеграцию с Azure Key Vault?

Да, SKU шлюза приложений версии 2 поддерживает Key Vault. Дополнительные сведения см. [в разделе завершение SSL с помощью сертификатов Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Разделы справки настроить прослушиватели HTTPS для сайтов com и .NET? 

Для маршрутизации на основе нескольких доменов (на основе узла) можно создать многосайтовые прослушиватели, настроить прослушиватели, использующие протокол HTTPS, в качестве протокола и связать прослушиватели с правилами маршрутизации. Дополнительные сведения см. в разделе [Размещение нескольких сайтов с помощью шлюза приложений](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Можно ли использовать специальные символы в пароле PFX-файла?

Нет, используйте только буквенно-цифровые символы в пароле PFX-файла.

## <a name="configuration---web-application-firewall-waf"></a>Настройка — брандмауэр веб-приложения (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Предлагает ли SKU WAF все функции, доступные в стандартном SKU?

Да. WAF поддерживает все функции SKU "Стандартный".

### <a name="how-do-i-monitor-waf"></a>Как выполнять мониторинг WAF?

Мониторинг WAF с помощью ведения журнала диагностики. Дополнительные сведения см. в разделе [ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Блокируется ли трафик в режиме обнаружения?

Нет. Режим обнаружения только регистрирует трафик, который запускает правило WAF.

### <a name="can-i-customize-waf-rules"></a>Могу ли я настроить правила WAF?

Да. Дополнительные сведения см. в разделе [Настройка групп правил и правил WAF](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Какие правила в настоящее время доступны для WAF?

В настоящее время WAF поддерживает запросы CR [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)и [3,1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Эти правила обеспечивают безопасность на основе большинства первых 10 уязвимостей, в которых открытый проект безопасности веб-приложений (OWASP) определяет: 

* Защита от внедрения кода SQL.
* Защита от межсайтовых сценариев
* Защита от распространенных веб-атак, таких как внедрение команд, HTTP-запрос несанкционированных, разделение HTTP-ответов и атака с удаленным включением файлов.
* Защита от нарушений протокола HTTP.
* Защита от аномалий протокола HTTP, например отсутствия агента пользователя узла и заголовков accept.
* Защита от программ-роботов, программ-обходчиков и сканеров.
* Обнаружение распространенных недопустимых конфигураций приложений (то есть Apache, IIS и т. д.)

Дополнительные сведения см. в статье [OWASP Top-10 уязвимостей](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Поддерживает ли WAF защиту от атак DDoS?

Да. Вы можете включить защиту от атак DDoS в виртуальной сети, в которой развертывается шлюз приложения. Таким образом действие службы Защиты от атак DDoS Azure распространится и на виртуальный IP-адрес (VIP) шлюза приложения.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Доступны ли рекомендации для перехода с SKU версии v1 на SKU v2?

Да. Дополнительные сведения см. в разделе [Перенос шлюза приложений Azure и брандмауэра веб-приложения с версии v1 на v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Конфигурация — входной контроллер для AKS

### <a name="what-is-an-ingress-controller"></a>Что такое входной контроллер?

Kubernetes позволяет создавать ресурсы `deployment` и `service` для внутреннего представления группы модулей Pod в кластере. Чтобы обеспечить доступ к той же службе извне, определяется ресурс [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) , который обеспечивает балансировку нагрузки, завершение SSL и виртуальное размещение на основе имени.
Для удовлетворения этого `Ingress`ного ресурса требуется входной контроллер, который прослушивает любые изменения в ресурсах `Ingress` и настраивает политики подсистемы балансировки нагрузки.

Контроллер входящего трафика шлюза приложений позволяет использовать [шлюз приложений Azure](https://azure.microsoft.com/services/application-gateway/) в качестве входящего трафика для [службы Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) , также известной как кластер AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Может ли один экземпляр входящего контроллера управлять несколькими шлюзами приложений?

В настоящее время один экземпляр входящего контроллера может быть связан только с одним шлюзом приложений.

## <a name="diagnostics-and-logging"></a>Диагностика и ведение журнала

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Какие типы журналов предоставляет шлюз приложений?

Шлюз приложений предоставляет три журнала: 

* **ApplicationGatewayAccessLog**: журнал доступа содержит каждый запрос, отправленный на интерфейсный сервер шлюза приложений. Данные включают в себя IP-адрес вызывающей стороны, запрошенный URL, задержку ответа, код возврата и байты. Журнал доступа собирается каждые 300 секунд. Он содержит одну запись для каждого шлюза приложений.
* **ApplicationGatewayPerformanceLog**. Журнал производительности фиксирует сведения о производительности для каждого шлюза приложений. Сведения включают пропускную способность в байтах, общее число обслуженных запросов, количество неудачных запросов, а также работоспособное и неработоспособное число экземпляров сервера.
* **ApplicationGatewayFirewallLog**. для шлюзов приложений, настроенных с помощью WAF, журнал брандмауэра содержит запросы, которые регистрируются в режиме обнаружения или в режиме предотвращения.

Дополнительные сведения см. в разделе [работоспособность серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Как узнать, работоспособны ли участники серверного пула?

Проверьте работоспособность с помощью командлета PowerShell `Get-AzApplicationGatewayBackendHealth` или портала. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Какова политика хранения для журналов диагностики?

Журналы диагностики поступают в учетную запись хранения клиента. Клиенты могут настроить политику хранения в соответствии с их предпочтениями. Журналы диагностики можно также отправлять в концентратор событий или в журналы Azure Monitor. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Как получить журналы аудита для Шлюза приложений?

На портале в колонке меню шлюза приложений выберите **Журнал действий** для доступа к журналу аудита. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Можно ли настроить оповещения для Шлюза приложений?

Да. В шлюзе приложений оповещения настраиваются на основе метрик. Дополнительные сведения см. в статьях [метрики шлюза приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) и [Получение уведомлений об оповещениях](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Как анализировать статистику трафика для Шлюза приложений?

Просматривать и анализировать журналы доступа можно несколькими способами. Используйте журналы Azure Monitor, Excel, Power BI и т. д.

Можно также использовать шаблон диспетчер ресурсов, который устанавливает и запускает популярный анализатор журнала [гоакцесс](https://goaccess.io/) для журналов доступа к шлюзу приложений. Гоакцесс предоставляет полезную статистику HTTP-трафика, такую как уникальные посетители, запрошенные файлы, узлы, операционные системы, браузеры и коды состояния HTTP. Дополнительные сведения см. в [файле сведений в папке Диспетчер ресурсов Template](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Что может вызвать работоспособность серверной части для возвращения неизвестного состояния?

Как правило, вы видите неизвестное состояние, когда доступ к серверной части блокируется группой безопасности сети (NSG), настраиваемой службой DNS или определяемой пользователем маршрутизацией (UDR) в подсети шлюза приложений. Дополнительные сведения см. в разделе [работоспособность серверной части, ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Есть ли в любом случае, когда в журналах потоков NSG не отображается разрешенный трафик?

Да. Если конфигурация соответствует приведенной ниже ситуации, вы не увидите разрешенный трафик в журналах потоков NSG:
- Вы развернули шлюз приложений версии 2
- У вас есть NSG в подсети шлюза приложений
- Вы включили журналы потоков NSG на этом NSG

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о шлюзе приложений см. в статье [что такое шлюз приложений Azure?](overview.md).
