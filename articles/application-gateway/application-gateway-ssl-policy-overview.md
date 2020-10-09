---
title: Общие сведения о политике TLS для шлюза приложений Azure
description: Узнайте, как настроить политику TLS для шлюза приложений Azure и сократить издержки шифрования и расшифровки из серверной фермы серверов.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253636"
---
# <a name="application-gateway-tls-policy-overview"></a>Общие сведения о политике TLS для шлюза приложений

Шлюз приложений Azure можно использовать для централизации управления сертификатами TLS/SSL и сокращения нагрузки шифрования и расшифровки из серверной фермы серверов. Централизованная обработка TLS также позволяет указать централизованную политику TLS, которая подходит для требований безопасности Организации. Это помогает обеспечить соответствие требованиям, а также рекомендациям по безопасности и организации работы.

Политика TLS включает контроль версий протокола TLS, а также комплектов шифров и порядок, в котором шифры используются во время подтверждения TLS. Шлюз приложений предлагает два механизма управления политикой TLS. Можно использовать стандартную или пользовательскую политику.

## <a name="predefined-tls-policy"></a>Предопределенная политика TLS

Шлюз приложений имеет три стандартных политики безопасности. Для настройки шлюза можно использовать любую из этих политик, чтобы обеспечить надлежащий уровень безопасности. В названиях политик указаны год и месяц, в которых они были настроены. Каждая политика предлагает разные версии протокола TLS и комплекты шифров. Для обеспечения оптимальной безопасности TLS рекомендуется использовать новейшие политики TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Свойство  |Значение  |
|---|---|
|Имя     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Значение по умолчанию| True (если не указана стандартная политика) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Свойство  |Значение  |
|   ---      |  ---       |
|Имя     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Значение по умолчанию| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Свойство  |Значение  |
|---|---|
|Имя     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Значение по умолчанию| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Настраиваемая политика TLS

Если Предопределенная политика TLS должна быть настроена для ваших требований, необходимо определить собственную настраиваемую политику TLS. Благодаря настраиваемой политике TLS вы полностью контролируете минимальную версию протокола TLS для поддержки, а также поддерживаемые комплекты шифров и их порядок приоритета.
 
### <a name="tlsssl-protocol-versions"></a>Версии протокола TLS/SSL

* Протоколы SSL 2.0 и 3.0 отключены по умолчанию для всех шлюзов приложений. Эти версии протокола не подлежат настройке.
* Настраиваемая политика TLS позволяет выбрать один из следующих трех протоколов в качестве минимальной версии протокола TLS для шлюза: TLSv1_0, TLSv1_1 и TLSv1_2.
* Если политика TLS не определена, включаются все три протокола (TLSv1_0, TLSv1_1 и TLSv1_2).

### <a name="cipher-suites"></a>Комплекты шифров

Шлюз приложений поддерживает следующие комплекты шифров, из которых можно выбрать пользовательскую политику. Порядок наборов шифров определяет приоритет при согласовании TLS.


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
> Наборы шифров TLS, используемые для соединения, также основываются на типе используемого сертификата. В подключениях клиента к шлюзу приложений используемые комплекты шифров основываются на типе сертификатов сервера в прослушивателе шлюза приложений. В шлюзе приложений к подключениям к серверному пулу используемые комплекты шифров основываются на типе сертификатов сервера на серверах серверного пула.

## <a name="known-issue"></a>Известная проблема
В настоящее время шлюз приложений версии 2 не поддерживает следующие шифры:
- ДХЕ-RSA-AES128-GCM-SHA256
- ДХЕ-RSA-AES128-SHA
- ДХЕ-RSA-AES256-GCM-SHA384
- ДХЕ-RSA-AES256-SHA
- ДХЕ-DSS-AES128-SHA256
- ДХЕ-DSS-AES128-SHA
- ДХЕ-DSS-AES256-SHA256
- ДХЕ-DSS-AES256-SHA

## <a name="next-steps"></a>Дальнейшие действия

Сведения о настройке политики TLS см. в статье [Настройка версий политик TLS и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).
