---
title: Проверка подлинности с помощью Azure Active Directory в национальных облаках
description: Узнайте о конечных точках для регистрации и проверки подлинности приложений в национальных облаках.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235492"
---
# <a name="national-clouds"></a>Национальные облака

Национальные облака — это физически изолированный экземпляры Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

Включая глобальное облако Azure Active Directory (Azure AD) развертывается в следующие национальных облаках:  

- Azure Government
- Azure для Германии
- Azure China 21Vianet

Национальные облака уникальны и отдельную среду из глобально в Azure. Важно учитывать основные отличия при разработке приложения для этих сред. Различия включают в себя регистрации приложений, получения токенов и настройка конечных точек.

## <a name="app-registration-endpoints"></a>Конечные точки регистрации приложений

Имеется отдельный портал Azure для каждого из национальных облаках. Чтобы интегрировать приложения с платформой Microsoft identity в национальном облаке, вам потребуется зарегистрировать приложение отдельно в каждой портала Azure, которое относится к среде.

В следующей таблице перечислены базовых URL-адресов для конечных точек Azure AD, используемый для регистрации приложения для каждого национального облака.

| Национальное облако | Конечная точка портала Azure AD |
|----------------|--------------------------|
| Azure AD для государственных организаций США | `https://portal.azure.us` |
| Azure AD для Германии | `https://portal.microsoftazure.de` |
| Azure AD для Китая через 21Vianet | `https://portal.azure.cn` |
| Azure AD (глобальная служба) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Конечные точки Azure AD для проверки подлинности

Все национальные облака проводят проверку подлинности пользователей в каждой среде отдельно и имеют отдельные конечные точки для проверки подлинности.

В следующей таблице перечислены базовых URL-адресов для конечных точек Azure AD, используемый для получения маркеров для каждого национального облака.

| Национальное облако | Конечная точка проверки подлинности Azure AD |
|----------------|-------------------------|
| Azure AD для государственных организаций США | `https://login.microsoftonline.us` |
| Azure AD для Германии| `https://login.microsoftonline.de` |
| Azure AD для Китая через 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (глобальная служба)| `https://login.microsoftonline.com` |

Процесс формирования запросов к авторизации Azure AD или маркеров конечных точек можно с помощью соответствующего конкретного региона базовый URL-адрес. Например, для Azure для Германии:

  - стандартная конечная точка авторизации — `https://login.microsoftonline.de/common/oauth2/authorize`;
  - стандартная конечная точка токена — `https://login.microsoftonline.de/common/oauth2/token`.

Для приложений с одним клиентом замените «общим» в предыдущие URL-адреса с помощью идентификатора клиента или имя. Например, `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> [Авторизации Azure AD версии 2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) и маркеров конечные точки доступны только для глобальной службы. Они не поддерживаются для развертываний в национальном облаке.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Чтобы узнать, как вызывать API-интерфейсов Microsoft Graph в национальном облаке среде, перейдите к [Microsoft Graph в национальном облаке развертываниях](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Некоторые функции, которые в некоторых регионах глобальной службы и службы могут оказаться недоступными во всех национальных облаках. Чтобы узнать, какие службы доступны, перейдите к статье [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Чтобы узнать, как создать приложение с помощью платформы удостоверений, следуйте [библиотеки аутентификации Майкрософт (MSAL) руководства](msal-national-cloud.md). В частности это приложение будет входа пользователей и получение маркера доступа для вызова Microsoft Graph API.

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet в Azure для Китая](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)
- [Основные сведения об аутентификации Azure AD](authentication-scenarios.md)
