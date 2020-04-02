---
title: Часто задаваемые вопросы о шлюзе приложений Azure
description: Найдите ответы на часто задаваемые вопросы о шлюзе приложений Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: d9691a6fd5c320242b9677776cbd08be4f800921
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544507"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Часто задаваемые вопросы о шлюзе приложений

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ниже приведены общие вопросы о шлюзе приложений Azure.

## <a name="general"></a>Общие сведения

### <a name="what-is-application-gateway"></a>Что такое шлюз приложений?

Azure Application Gateway предоставляет контроллер доставки приложений (ADC) в качестве службы. Он предлагает различные возможности балансировки нагрузки 7 уровня 7 для ваших приложений. Эта служба очень доступна, масштабируема и полностью управляется Azure.

### <a name="what-features-does-application-gateway-support"></a>Какие функции поддерживает Шлюз приложений?

Приложение Gateway поддерживает автоматическое масштабирование, разгрузку SSL и сквозной SSL, брандмауэр веб-приложений (WAF), сродство сеансов на основе файлов cookie, маршрутизирование на основе URL-пути, многоузловое хостинг и другие функции. Полный список поддерживаемых функций см. в статье [Что такое Шлюз приложений Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Чем отличаются шлюз приложений и балансер загрузки Azure?

Приложение Gateway представляет собой балансизатор нагрузки уровня 7, что означает, что он работает только с веб-трафиком (HTTP, HTTPS, WebSocket и HTTP/2). Он поддерживает такие возможности, как sSL-прекращение, сродство сеансов на основе файлов cookie и круглый малиновка для балансировки нагрузки. Нагрузка балансер нагрузки-баланс движения на уровне 4 (TCP или UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Какие протоколы поддерживает Шлюз приложений?

Шлюз приложений поддерживает протоколы HTTP, HTTPS, HTTP/2 и WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Как Шлюз приложений поддерживает протокол HTTP/2?

Смотрите [поддержку HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Какие ресурсы поддерживаются в рамках пула бэкэнда?

Смотрите [поддерживаемые ресурсы бэкэнда.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)

### <a name="in-what-regions-is-application-gateway-available"></a>В каких регионах доступен шлюз приложений?

Шлюз приложений доступен во всех регионах глобальной платформы Azure. Он также доступен в [Azure China 21Vianet](https://www.azure.cn/) и [Azure правительства](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Предназначено ли развертывание для моей подписки или оно является общим для всех клиентов?

Шлюз приложений — это специальное развертывание в вашей виртуальной сети.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Поддерживает ли шлюз приложения HTTP-TO-HTTPS перенаправление?

Перенаправление поддерживается. Смотрите [обзор приложения Gateway перенаправить](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>В каком порядке обрабатываются прослушиватели?

Смотрите [порядок обработки слушателя.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Где найти IP-адрес ip и DNS-портал приложений?

Если вы используете общедоступный IP-адрес в качестве конечной точки, вы найдете информацию об ИС и DNS на общедоступном ресурсе IP-адреса. Или найти его на портале, на странице обзора для портала приложения. Если вы используете внутренние IP-адреса, найдите информацию на странице обзора.

Для v2 SKU откройте общедоступный ресурс IP и выберите **Конфигурацию.** Для настройки имени DNS доступно поле **марки DNS (необязательно).**

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Каковы настройки тайм-аута Keep-Alive и тайм-аута TCP?

*Тайм-аут Keep-Alive* определяет, как долго шлюз приложения будет ждать клиента, чтобы отправить другой запрос HTTP на постоянное соединение перед его повторного использования или закрытия. *Тайм-аут TCP определяет,* как долго подключение TCP остается открытым в случае отсутствия активности. 

*Тайм-аут Keep-Alive* в Application Gateway v1 SKU составляет 120 секунд, а в v2 SKU - 75 секунд. *TCP простоя тайм-аут* 4-минутный по умолчанию на передней виртуальной IP (VIP) как v1 и v2 SKU приложения шлюза. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Изменяется ли имя IP или DNS в течение всего срока службы шлюза приложения?

В Application Gateway V1 SKU VIP может измениться, если вы остановитесь и запустите шлюз приложения. Но имя DNS, связанное с шлюзом приложения, не меняется в течение всего срока службы шлюза. Поскольку имя DNS не изменяется, следует использовать псевдоним CNAME и указать его на dNS-адрес шлюза приложения. В Application Gateway V2 SKU можно установить IP-адрес как статический, поэтому имя IP и DNS не изменится в течение всего срока службы шлюза приложения. 

### <a name="does-application-gateway-support-static-ip"></a>Поддерживает ли Шлюз приложений статические IP-адреса?

Да, Application Gateway v2 SKU поддерживает статические общедоступные IP-адреса. Номер SKU версии 1 поддерживает статические внутренние IP-адреса.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Поддерживает ли Шлюз приложений несколько общедоступных IP-адресов в шлюзе?

Шлюз приложения поддерживает только один общедоступный IP-адрес.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Каким должен быть размер подсети Шлюза приложений?

Смотрите [соображения размера подсети Application Gateway.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Можно ли развернуть более одного ресурса шлюза приложений в одной подсети?

Да. В дополнение к нескольким экземплярам заданного развертывания шлюза приложений можно предоставить другой уникальный ресурс Пристаного шлюза существующей подсети, содержащий другой ресурс Gateway application.

Одна подсеть не может поддерживать как Standard_v2, так и стандартный шлюз приложений вместе.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Поддерживает ли Application Gateway v2 маршруты, определяемые пользователями (UDR)?

Да, но только конкретные сценарии. Для получения дополнительной информации смотрите [обзор конфигурации приложения Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Поддерживает ли Шлюз приложений заголовки X-Forwarded-For?

Да. Смотрите [изменения запроса](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Сколько времени занимает развертывание шлюза приложения? Будет ли мой шлюз приложения работать во время его обновления?

Подготовка новых развертываний номера SKU Шлюза приложений версии 1 может занять до 20 минут. Изменения размера экземпляра или количества не являются разрушительными, и шлюз остается активным в течение этого времени.

Большинство развертываний, которые используют v2 SKU занимает около 6 минут, чтобы обеспечить. Однако это может занять больше времени в зависимости от типа развертывания. Например, развертывание в нескольких зонах доступности с большим количеством экземпляров может занять более 6 минут. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Могу ли я использовать Exchange Server в качестве бэкэнда с помощью шлюза приложений?

Нет. Приложение Gateway не поддерживает протоколы электронной почты, такие как SMTP, IMAP и POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Есть ли рекомендации для перехода из v1 SKU в v2 SKU?

Да. Для получения подробной информации [см.](migrate-v1-v2.md)

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Будет ли продолжать поддерживаться application Gateway v1 SKU?

Да. Приложение Шлюз v1 SKU будет по-прежнему поддерживаться. Тем не менее, настоятельно рекомендуется перейти к v2, чтобы воспользоваться обновлениями функций в этом SKU. Для получения дополнительной [информации см. Автомасштабирование и зона-излишним приложение шлюза v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="performance"></a>Производительность

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Каким образом Шлюз приложений поддерживает высокий уровень доступности и масштабируемость?

Приложение Gateway v1 SKU поддерживает сценарии высокой доступности при развертывании двух или более экземпляров. Azure распространяет эти экземпляры по областям обновления и неисправностей, чтобы гарантировать, что экземпляры не сбой в то же время. Номер SKU версии 1 поддерживает масштабируемость путем добавления нескольких экземпляров одного шлюза для распределения нагрузки.

Номер SKU версии 2 автоматически гарантирует, что новые экземпляры распределяются между доменами сбоя и доменами обновления. Если вы выбираете резервизм зоны, новейшие экземпляры также распределяются по зонам доступности, чтобы предложить устойчивость зональных сбоев.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Как достичь сценария DR в центрах обработки данных с помощью портала приложений?

Используйте traffic Manager для распределения трафика по нескольким шлюзам приложений в различных центрах обработки данных.

### <a name="does-application-gateway-support-autoscaling"></a>Поддерживает ли автоматический масштабирование шлюз приложения?

Да, номер SKU Шлюза приложений версии 2 поддерживает автоматическое масштабирование. Для получения дополнительной [информации см.](application-gateway-autoscaling-zone-redundant.md)

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Приводит ли ручной или автоматический масштаб или масштаб дауна время простоя?

Нет. Экземпляры распределяются через домены обновления и домены сбоя.

### <a name="does-application-gateway-support-connection-draining"></a>Поддерживает ли Шлюз приложений фильтрацию подключений?

Да. Можно настроить соединение, истощающее для изменения членов в пуле бэкэнда без сбоев. Для получения дополнительной [информации](features.md#connection-draining)см.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Можно ли изменить размер экземпляра со среднего на большой без прерывания?

Да.

## <a name="configuration"></a>Параметр Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Всегда ли Шлюз приложений развертывается в виртуальной сети?

Да. Приложение Gateway всегда развертывается в виртуальной сетевой подсети. Эта подсеть может содержать только шлюзы приложений. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Может ли Application Gateway общаться со экземплярами за пределами своей виртуальной сети или за пределами подписки?

Пока у вас есть IP-соединение, Application Gateway может общаться со экземплярами за пределами виртуальной сети, в которую он подключен. Приложение Gateway также может общаться со экземплярами вне подписки, в которых он вне. Если вы планируете использовать внутренние I-провайдеры в качестве бэкэнд-пула, используйте [виртуальную сеть пиринга](../virtual-network/virtual-network-peering-overview.md) или [Azure VPN Gateway.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Можно ли развернуть еще что-нибудь в подсети шлюза приложений?

Нет. Но вы можете развернуть другие шлюзы приложений в подсети.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Поддерживаются ли группы сетевой безопасности в подсети шлюза приложения?

Смотрите [группы сетевой безопасности в подсети Application Gateway.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Поддерживает ли подсеть шлюза приложения маршруты, определяемые пользователем?

Смотрите [маршруты, поддерживаемые пользователями, в подсети Application Gateway.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Какие у шлюза приложений ограничения? Можно ли увеличить предельные значения?

Посмотреть [лимиты шлюзов приложений](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Могу ли я одновременно использовать шлюз приложения как для внешнего, так и для внутреннего трафика?

Да. Application Gateway поддерживает один внутренний IP и один внешний IP на шлюз приложения.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Поддерживает ли Application Gateway виртуальную сеть пиринга?

Да. Виртуальная сеть пиринга помогает нагрузки баланса трафика в других виртуальных сетей.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Могу ли я поговорить с наемными серверами, когда они подключены ExpressRoute или VPN туннелями?

Да, при условии, что разрешен трафик.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Может ли один бэкэнд-пул обслуживать множество приложений в разных портах?

Архитектура микрослужб поддерживается. Чтобы исследовать на разных портах, необходимо настроить несколько настроек HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Поддерживают ли пользовательские зонды подстановочные знаки или regex по данным ответов?

Нет. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Как обрабатываются правила реуктора в шлюзе приложения?

Смотрите [Правила обработки.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Что означает поле Host для пользовательских зондов?

Поле Host определяет имя для отправки зонда, когда вы настроили мультисайт на шлюзе приложения. В противном случае используйте '127.0.0.1'. Это значение отличается от названия виртуального хоста машины. Его формат \<\>\<протокол ://\<\>\<хост\>\>: путь порта .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Могу ли я разрешить доступ к Порталу приложений только к нескольким IP-адресам источника?

Да. Смотрите [ограничить доступ к определенным испытно-сноттам-источникам.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Могу ли я использовать один и тот же порт как для слушателей, стоящих перед общественностью, так и для частных слушателей?

Нет.

### <a name="does-application-gateway-support-ipv6"></a>Поддерживает ли шлюз приложения IPv6?

Приложение Gateway v2 в настоящее время не поддерживает IPv6. Он может работать в двойном стеке VNet, используя только IPv4, но подсеть шлюза должна быть только IPv4. Application Gateway v1 не поддерживает двойной стек VNets. 

## <a name="configuration---ssl"></a>Конфигурация - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Какие сертификаты поддерживает шлюз приложения?

Приложение Gateway поддерживает самоподписанные сертификаты, сертификаты (CA), сертификаты расширенной валидации (EV) и сертификаты подстановочных знаков.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Какие наборы шифров поддерживает сяочие наборы приложений?

Приложение Gateway поддерживает следующие наборы шифров. 

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

Для получения информации о том, как настроить параметры SSL, [см.](application-gateway-configure-ssl-policy-powershell.md)

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Поддерживает ли Application Gateway повторное шифрование трафика в бэкэнде?

Да. Приложение Gateway поддерживает Разгрузку SSL и сквозной SSL, который повторно шифрует трафик в бэкэнд.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Могу ли я настроить политику SSL для управления версиями протоколов SSL?

Да. Вы можете настроить шлюз приложения, чтобы отказать в TLS1.0, TLS1.1 и TLS1.2. По умолчанию SSL 2.0 и 3.0 уже отключены и не настраиваются.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Можно ли настроить комплекты шифров и порядок политик?

Да. В приложении Gateway можно [настроить наборы шифров.](application-gateway-ssl-policy-overview.md) Чтобы определить пользовательскую политику, включите по крайней мере один из следующих наборов шифров. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Приложение Gateway использует SHA256 для управления бэкэндом.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Сколько SSL-сертификатов поддерживает Шлюз приложения?

Приложение Gateway поддерживает до 100 SSL-сертификатов.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Сколько сертификатов аутентификации для повторного шифрования бэкэнда поддерживает Application Gateway?

Приложение Gateway поддерживает до 100 сертификатов аутентификации.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Интегрируется ли шлюз приложений с Azure Key Vault?

Да, Application Gateway v2 SKU поддерживает Key Vault. Для получения дополнительной [SSL termination with Key Vault certificates](key-vault-certs.md)информации см.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Как настроить httpS слушателей для .com и .net сайтов? 

Для нескольких доменных (хост-ориентированных) реуктов можно создать многоузловые слушатели, настроить слушателей, которые используют HTTPS в качестве протокола, и связать слушателей с правилами разгрома. Для получения дополнительной информации [см. Хостинг нескольких сайтов с помощью приложения шлюз](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Могу ли я использовать специальные символы в моем пароле файлов .pfx?

Нет, используйте только буквенно-цифровые символы в пароле файлов .pfx.

## <a name="configuration---web-application-firewall-waf"></a>Конфигурация - брандмауэр веб-приложений (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Предлагает ли WAF SKU все функции, доступные в Стандартном SKU?

Да. WAF поддерживает все функции в Стандартном SKU.

### <a name="how-do-i-monitor-waf"></a>Как выполнять мониторинг WAF?

Мониторинг WAF с помощью диагностических журналов. Для получения дополнительной [информации см.](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Блокируется ли трафик в режиме обнаружения?

Нет. Режим обнаружения регистрирует только трафик, который запускает правило WAF.

### <a name="can-i-customize-waf-rules"></a>Могу ли я настроить правила WAF?

Да. Для получения дополнительной [информации см.](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available-for-waf"></a>Какие правила в настоящее время доступны для WAF?

WAF в настоящее время поддерживает CRS [2.2.9,](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229) [3.0,](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)и [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Эти правила обеспечивают базовую безопасность от большинства из топ-10 уязвимостей, которые Open Web Application Security Project (OWASP) определяет: 

* Защита от внедрения кода SQL.
* Защита от кросс-сайтов сценариев
* Защита от распространенных веб-атак, таких как инъекция команд, контрабанда запроса HTTP, разделение ответов HTTP и атака удаленного включения файлов
* Защита от нарушений протокола HTTP.
* Защита от аномалий протокола HTTP, например отсутствия агента пользователя узла и заголовков accept.
* Защита от программ-роботов, программ-обходчиков и сканеров.
* Обнаружение распространенных неправильных конфигураций приложений (то есть Apache, IIS и так далее)

Для получения дополнительной [OWASP top-10 vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)информации см.

### <a name="does-waf-support-ddos-protection"></a>Поддерживает ли WAF защиту DDoS?

Да. Вы можете включить DDoS-защиту в виртуальной сети, где развертывается шлюз приложения. Эта настройка гарантирует, что служба DDoS-защиты Azure также защищает виртуальный IP-адрес приложения (VIP).

## <a name="configuration---ingress-controller-for-aks"></a>Конфигурация - всколыхающий контроллер для AKS

### <a name="what-is-an-ingress-controller"></a>Что такое контроллер входа?

Kubernetes позволяет `deployment` создавать `service` и ресурсы подвергать группу стручков внутри кластера. Чтобы разоблачить ту же [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) самую службу извне, определяется ресурс, обеспечивающий балансировку нагрузки, sSL-прекращение и виртуальный хостинг на основе имен.
Для удовлетворения этого `Ingress` ресурса требуется контроллер Ingress, `Ingress` который выслушивает любые изменения ресурсов и настраивает политики балансировщика нагрузки.

Контроллер входа в шлюз приложений позволяет использовать [шлюз приложений Azure](https://azure.microsoft.com/services/application-gateway/) в качестве входа в [службу Azure Kubernetes,](https://azure.microsoft.com/services/kubernetes-service/) также известную как кластер AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Может ли один экземпляр контроллера управлять несколькими шлюзами приложений?

В настоящее время один экземпляр контроллера Ingress может быть связан только с одним шлюзом приложения.

## <a name="diagnostics-and-logging"></a>Диагностика и ведение журнала

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Какие типы журналов предоставляет Портал приложений?

Приложение Шлюз предоставляет три журнала: 

* **ApplicationGatewayAccessLog**: Журнал доступа содержит каждый запрос, представленный в переднюю часть шлюза приложения. Данные включают IP-адрес вызываемого абонента, запрашиваемый URL-адрес, задержку ответа, код возврата и байты в и обратно. Он содержит одну запись на шлюз приложения.
* **ApplicationGatewayPerformanceLog**: Журнал производительности фиксирует информацию о производительности для каждого шлюза приложения. Информация включает пропускную выливку в байты, общее количество поданных запросов, неудавальное количество запросов, а также неработоспособное и нездоровое количество экземпляров бэкэнда.
* **ApplicationGatewayFirewallLog:** Для шлюзов приложений, которые вы настраиваете с помощью WAF, журнал брандмауэра содержит запросы, которые регистрируются либо через режим обнаружения, либо в режиме предотвращения.

Все журналы собираются каждые 60 секунд. Для получения дополнительной информации ознакомьтесь с [журналами backend health, журналами диагностики и метриками для шлюза приложений.](application-gateway-diagnostics.md)

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Как узнать, работоспособны ли участники серверного пула?

Проверьте здоровье с помощью `Get-AzApplicationGatewayBackendHealth` cmdlet PowerShell или портала. Для получения дополнительной [информации](application-gateway-diagnostics.md)см.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Какова политика удержания диагностических журналов?

Диагностические журналы поступают на учетную запись хранения клиента. Клиенты могут устанавливать политику удержания в зависимости от их предпочтений. Диагностические журналы также могут быть отправлены в концентратор событий или журналы Azure Monitor. Для получения дополнительной [информации](application-gateway-diagnostics.md)см.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Как получить журналы аудита для Шлюза приложений?

На портале, в меню blade портала приложения, выберите **журнал активности** для доступа к журналу аудита. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Можно ли настроить оповещения для Шлюза приложений?

Да. В шлюзе приложений оповещения настраиваются на метрики. Для получения дополнительной информации смотрите [метрики шлюза приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) и [получайте уведомления о помесь.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Как анализировать статистику трафика для Шлюза приложений?

Вы можете просматривать и анализировать журналы доступа несколькими способами. Используйте журналы Azure Monitor, Excel, Power BI и так далее.

Вы также можете использовать шаблон resource Manager, который устанавливает и запускает популярный анализатор журнала [GoAccess](https://goaccess.io/) для журналов доступа к access Application Gateway. GoAccess предоставляет ценную статистику трафика HTTP, такие как уникальные посетители, запрошенные файлы, хосты, операционные системы, браузеры и коды статуса HTTP. Для получения дополнительной информации в GitHub смотрите [файл Readme в папке шаблона ресурсного менеджера.](https://aka.ms/appgwgoaccessreadme)

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Что может привести к тому, что здоровье бэкэнда может вернуть неизвестный статус?

Обычно вы видите неизвестный статус, когда доступ к бэкэнду блокируется группой сетевой безопасности (NSG), пользовательской DNS или пользовательской схемой (UDR) в подсети шлюза приложения. Для получения дополнительной [информации](application-gateway-diagnostics.md)см.

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Есть ли случай, когда журналы потока NSG не показывают разрешенный трафик?

Да. Если конфигурация соответствует следующему сценарию, вы не увидите разрешенного трафика в журналах потока NSG:
- Вы развернули шлюз приложений v2
- У вас есть NSG в подсети шлюза приложения
- Вы включили журналы потока NSG на этом NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Как использовать Application Gateway V2 только с частным IP-адресом фронта?

Приложение Gateway V2 в настоящее время поддерживает не только частный IP-режим. Он поддерживает следующие комбинации
* Частные IP и общественного IP
* Только публичный IP

Но если вы хотите использовать Application Gateway V2 только с частным IP, вы можете следить за процессом ниже:
1. Создание шлюза приложения как с государственным, так и с частным IP-адресом
2. Не создавайте слушателей для ip-адреса общественного фронта. Приложение Gateway не будет слушать трафик на общедоступный IP-адрес, если для него не созданы слушатели.
3. Создайте и прикрепите [группу сетевой безопасности](https://docs.microsoft.com/azure/virtual-network/security-overview) для подсети Application Gateway со следующей конфигурацией в порядке приоритета:
    
    а. Разрешить трафик от источника, как **GatewayManager** сервисный тег и назначения, как **любой** и пункт назначения порта как **65200-65535**. Такой диапазон портов требуется для обмена данными в рамках инфраструктуры Azure. Эти порты защищены (заблокированы) проверкой подлинности сертификата. Внешние сущности, включая администраторов пользователей Gateway, не могут инициировать изменения на этих конечных точках без соответствующих сертификатов
    
    b. Разрешить трафик из источника как tag обслуживания **AzureLoadBalancer** и порт назначения как **любой**
    
    c. Запретить весь входящий трафик от источника как **тег интернет-сервиса** и порт назначения как **Any.** Дайте этому правилу *наименьший приоритет* в входящих правилах
    
    d. Сохраняйте правила по умолчанию, такие как разрешение VirtualNetwork на входящие так, чтобы доступ на частный IP-адрес не был заблокирован
    
    д) Исходящее подключение к Интернету не может быть заблокировано. В противном случае вы столкнетесь с проблемами с журналированием, метриками и так далее.

Пример конфигурации NSG для ![частного IP только доступа: Приложение шлюз V2 NSG Конфигурация для частного доступа IP только](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Поддерживает ли маркеры сродства шлюзов application application?
Да, [браузер Chromium](https://www.chromium.org/Home) [v80 обновление](https://chromiumdash.appspot.com/schedule) ввело мандат на HTTP печенье без SameSite атрибут, который будет рассматриваться как SameSite-Lax. Это означает, что cookie-файлы сродства шлюза приложений не будут отправлены браузером в сторонних контексте. Для поддержки этого сценария Application Gateway вводит еще одно файлcookieое печенье *под названием ApplicationGatewayAffinityCORS* в дополнение к существующему *cookie-файлу ApplicationGatewayAffinity.*  Эти файлы cookie похожи, но в *cookie-файлах ApplicationGatewayAffinityCORS* добавлены еще два атрибута: *SameSite-None; Безопасный*. Эти атрибуты поддерживают липкие сеансы даже для запросов кросс-происхождения. Для получения дополнительной информации можно ознакомиться с [разделом сродства на основе файлов cookie.](configuration-overview.md#cookie-based-affinity)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о шлюзе приложений, [см.](overview.md)
