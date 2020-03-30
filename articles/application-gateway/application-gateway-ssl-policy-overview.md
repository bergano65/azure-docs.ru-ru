---
title: Обзор политики TLS для шлюза приложений Azure
description: Узнайте, как настроить политику TLS для Azure Application Gateway и уменьшить накладные расходы на шифрование и расшифровку с серверной фермы.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257382"
---
# <a name="application-gateway-tls-policy-overview"></a>Обзор политики приложения Gateway TLS

Вы можете использовать Azure Application Gateway для централизации управления сертификатами TLS/SSL и сокращения накладных расходов на шифрование и расшифровку с серверной фермы. Эта централизованная обработка TLS также позволяет указать центральную политику TLS, соответствующую вашим требованиям организационной безопасности. Это помогает обеспечить соответствие требованиям, а также рекомендациям по безопасности и организации работы.

Политика TLS включает в себя управление версией протокола TLS, а также наборы шифров и порядок использования шифров во время рукопожатия TLS. Application Gateway предлагает два механизма управления политикой TLS. Можно использовать стандартную или пользовательскую политику.

## <a name="predefined-tls-policy"></a>Предопределенная политика TLS

Шлюз приложений имеет три стандартных политики безопасности. Для настройки шлюза можно использовать любую из этих политик, чтобы обеспечить надлежащий уровень безопасности. В названиях политик указаны год и месяц, в которых они были настроены. Каждая политика предлагает различные версии протоколов TLS и наборы шифров. Мы рекомендуем использовать новейшие политики TLS для обеспечения наилучшей безопасности TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Свойство  |Значение  |
|---|---|
|name     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Значение по умолчанию| True (если не указана стандартная политика) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Свойство  |Значение  |
|   ---      |  ---       |
|name     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Значение по умолчанию| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Свойство  |Значение  |
|---|---|
|name     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Значение по умолчанию| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Пользовательская политика TLS

Если необходимо настроить предопределенную политику TLS для ваших требований, необходимо определить свою собственную политику TLS. С помощью пользовательской политики TLS у вас есть полный контроль над минимальной версией протокола TLS для поддержки, а также поддерживаемыми наборами шифров и их приоритетным заказом.
 
### <a name="tlsssl-protocol-versions"></a>Версии протокола TLS/SSL

* Протоколы SSL 2.0 и 3.0 отключены по умолчанию для всех шлюзов приложений. Эти версии протокола не подлежат настройке.
* Пользовательская политика TLS дает вам возможность выбрать любой из следующих трех протоколов в качестве минимальной версии протокола TLS для вашего шлюза: TLSv1_0, TLSv1_1 и TLSv1_2.
* Если политика TLS не определена, все три протокола (TLSv1_0, TLSv1_1 и TLSv1_2) включены.

### <a name="cipher-suites"></a>Комплекты шифров

Шлюз приложений поддерживает следующие комплекты шифров, из которых можно выбрать пользовательскую политику. Порядок наборов шифров определяет приоритетный порядок во время переговоров TLS.


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
> Пакеты шифров TLS, используемые для соединения, также основаны на типе используемого сертификата. В клиентских соединениях шлюза используемые наборы шифров основаны на типе серверных сертификатов на слушателе шлюза приложения. В шлюзе приложений для бэкэнда соединения пула, используемые наборы шифров основаны на типе серверных сертификатов на серверах пула бэкэнда.

## <a name="known-issue"></a>Известная проблема
Приложение Gateway v2 в настоящее время не поддерживает следующие шифры:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите научиться настраивать политику TLS, [см.](application-gateway-configure-ssl-policy-powershell.md)
