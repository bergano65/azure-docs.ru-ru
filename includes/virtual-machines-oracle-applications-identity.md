---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361561"
---
### <a name="identity-tier"></a>Уровень удостоверений 

Партнерство Microsoft-Oracle позволяет настроить единую идентификацию в Azure, OCI и приложении Oracle. Для настройки единого входа между Azure AD и Oracle OHS для JD Edwards EnterpriseOne или PeopleSoft Application Suite необходим экземпляр сервера Oracle HTTP (ИДКС).

OHS действует как обратный прокси-сервер уровня приложения, что означает, что все запросы к конечным приложениям проходят через него. Веб-шлюз Oracle Access Manager — это подключаемый модуль OHS Web Server, который перехватывает все запросы к конечному приложению. Если доступ к ресурсу защищен (требуется сеанс с проверкой подлинности), веб-шлюз инициирует поток проверки подлинности OIDC с помощью облачной службы Identity через браузер пользователя. Дополнительные сведения о потоках, поддерживаемых OpenID Connect Connects, см. в документации по [диспетчеру доступа Oracle](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

При такой настройке пользователь, уже вошедший в Azure AD, может переходить к приложению JD Edwards EnterpriseOne или PeopleSoft без повторного входа с помощью облачной службы Identity Oracle. Клиенты, которые развертывают это решение, получают преимущества единого входа, в том числе единый набор учетных данных, Улучшенный интерфейс входа в систему, Улучшенный уровень безопасности и сокращенные затраты на службу технической поддержки.

Дополнительные сведения о настройке единого входа для JD Edwards EnterpriseOne или PeopleSoft с помощью Azure AD см. в соответствующем [техническом документе Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).