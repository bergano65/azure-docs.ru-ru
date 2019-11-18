---
title: Общие сведения о политике SSL для шлюза приложений Azure
description: Узнайте, как настроить политику SSL для шлюза приложений Azure и сократить издержки шифрования и расшифровки из серверной фермы серверов.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 6b3d52f1f6bd0390ab6ccafa80b2979cb0e498fd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130405"
---
# <a name="application-gateway-ssl-policy-overview"></a>Общие сведения о политике SSL шлюза приложений

Шлюз приложений Azure можно использовать, чтобы централизовать управление SSL-сертификатами и сократить нагрузку на внутренний сервер фермы, связанную с шифрованием данных и их расшифровкой. Такая централизованная обработка SSL также дает возможность задать центральную политику SSL в соответствии с требованиями безопасности организации. Это помогает обеспечить соответствие требованиям, а также рекомендациям по безопасности и организации работы.

Политика SSL включает управление версией протокола SSL, комплектами шифров, а также порядком использования шифров во время подтверждения SSL. Шлюз приложений предоставляет два механизма управления политикой SSL. Можно использовать стандартную или пользовательскую политику.

## <a name="predefined-ssl-policy"></a>Стандартная политика SSL

Шлюз приложений имеет три стандартных политики безопасности. Для настройки шлюза можно использовать любую из этих политик, чтобы обеспечить надлежащий уровень безопасности. В названиях политик указаны год и месяц, в которых они были настроены. Каждая политика предлагает разные версии протокола SSL и комплекты шифров. Мы рекомендуем использовать новейшие политики SSL, чтобы обеспечить наилучшую безопасность SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Свойство  |Значение  |
|---|---|
|имя     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|значение по умолчанию| True (если не указана стандартная политика) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Свойство  |Значение  |
|   ---      |  ---       |
|имя     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|значение по умолчанию| Ложь |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Свойство  |Значение  |
|---|---|
|имя     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|значение по умолчанию| Ложь |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Пользовательская политика SSL

Если стандартную политику SSL необходимо настроить в соответствии с требованиями, нужно определить собственную пользовательскую политику SSL. В этом случае вы получаете полный контроль над минимальной версией протокола SSL, которую следует поддерживать, а также над поддерживаемыми комплектами шифров и порядком их приоритета.
 
### <a name="ssl-protocol-versions"></a>Версии протокола SSL

* Протоколы SSL 2.0 и 3.0 отключены по умолчанию для всех шлюзов приложений. Эти версии протокола не подлежат настройке.
* При использовании пользовательской политики SSL в качестве минимальной версии протокола SSL для шлюза можно выбрать любой из следующих трех протоколов: TLSv1_0, TLSv1_1 и TLSv1_2.
* Если политика SSL не определена, то будут включены все три протокола (TLSv1_0, TLSv1_1 и TLSv1_2).

### <a name="cipher-suites"></a>Комплекты шифров

Шлюз приложений поддерживает следующие комплекты шифров, из которых можно выбрать пользовательскую политику. Порядок комплектов шифров определяет порядок приоритета во время согласования SSL.


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

> [!NOTE]
> Используемые для соединения наборы шифров SSL также основываются на типе используемого сертификата. В подключениях клиента к шлюзу приложений используемые комплекты шифров основываются на типе сертификатов сервера в прослушивателе шлюза приложений. В шлюзе приложений к подключениям к серверному пулу используемые комплекты шифров основываются на типе сертификатов сервера на серверах серверного пула.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о настройке политики SSL см. в статье [Настройка версий политики SSL и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).
