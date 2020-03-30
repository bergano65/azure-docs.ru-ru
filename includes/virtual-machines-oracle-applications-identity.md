---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361561"
---
### <a name="identity-tier"></a>Уровень идентификации 

Партнерство Microsoft-Oracle позволяет создать единую идентификацию в Azure, OCI и приложении Oracle. Для JD Edwards EnterpriseOne или пакета приложений PeopleSoft экземпляр Oracle HTTP Server (OHS) необходим для настройки единого входного пакета между Azure AD и Oracle IDCS.

OHS действует как обратный прокси на уровень приложения, что означает, что все запросы в конечные приложения проходят через него. Oracle Access Manager WebGate — это плагин веб-сервера OHS, который перехватывает каждый запрос, идущий в конце приложения. Если доступ к ресурсу защищен (требует проверки подлинности), WebGate инициирует поток аутентификации OIDC с помощью облачного сервиса identity данных через браузер пользователя. Для получения дополнительной информации о потоках, поддерживаемых [Oracle Access Manager documentation](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)OpenID Connect WebGate, см.

С помощью этой настройки пользователь, уже вошедшая в Azure AD, может перемещаться в приложение JD Edwards EnterpriseOne или PeopleSoft без повторного входа в систему через облачный сервис Oracle Identity. Клиенты, развертывающие это решение, получают преимущества единого включения, включая единый набор учетных данных, улучшенный опыт регистрации, повышение безопасности и снижение стоимости справочной службы.

Чтобы узнать больше о настройке единого ввоза для JD Edwards EnterpriseOne [Oracle whitepaper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)или PeopleSoft с Помощью Azure AD, см.