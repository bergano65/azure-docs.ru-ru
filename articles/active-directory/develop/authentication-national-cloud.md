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
ms.openlocfilehash: a0d4586df23548854f4acbfefd32081a36906097
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067902"
---
# <a name="national-clouds"></a>Национальные облака

Национальные облака — это физически изолированный экземпляры Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

Кроме глобального облака, Azure Active Directory развертывается в следующих национальных облаках:  

- Azure для государственных организаций США
- Azure для Германии
- Azure China 21Vianet

Национальные облака являются уникальными и отличаются от глобального Azure средой. Поэтому важно учитывать некоторые ключевые различия при разработке приложения для этих сред, например, регистрация приложений, получения токенов и настройка конечных точек.

## <a name="app-registration-endpoints"></a>Конечные точки регистрации приложений

Имеется отдельный портал Azure для каждого из национальных облаках. Чтобы интегрировать приложения с платформой удостоверений Майкрософт в национальном облаке, необходимо зарегистрировать каждое приложение отдельно на соответствующем портале Azure в зависимости от среды.

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD), используемых для регистрации приложения в каждом из национальных облаков.

| Национальное облако | Конечная точка портала Azure AD |
|----------------|--------------------------|
| Azure AD для государственных организаций США | `https://portal.azure.us` |
| Azure AD для Германии | `https://portal.microsoftazure.de` |
| Azure AD для Китая через 21Vianet | `https://portal.azure.cn` |
| Azure AD (глобальная служба) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Конечные точки Azure AD для проверки подлинности

Все национальные облака проводят проверку подлинности пользователей в каждой среде отдельно и имеют отдельные конечные точки для проверки подлинности.

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD) для получения токенов для каждого национального облака.

| Национальное облако | Конечная точка проверки подлинности Azure AD |
|----------------|-------------------------|
| Azure AD для государственных организаций США | `https://login.microsoftonline.us` |
| Azure AD для Германии| `https://login.microsoftonline.de` |
| Azure AD для Китая через 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (глобальная служба)| `https://login.microsoftonline.com` |

- Запросы к конечным точкам Azure AD для авторизации или получения маркеров проверки подлинности можно составлять, используя базовый URL-адрес соответствующего региона. Например, для Azure для Германии:

  - стандартная конечная точка авторизации — `https://login.microsoftonline.de/common/oauth2/authorize`;
  - стандартная конечная точка токена — `https://login.microsoftonline.de/common/oauth2/token`.

- В приложениях с одним клиентом замените общее в указанных выше URL-адресах идентификатором или именем клиента, например `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Конечные точки [Azure AD версии 2.0 для авторизации]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) и получения маркеров проверки подлинности доступны только в глобальной службе. Они пока недоступны для служб, развернутых в национальных облаках.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Для дополнительных сведений см. статью [Национальные облачные развертывания](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Некоторые функции и службы, существующие в отдельных регионах глобальной службы, могут быть недоступными для всех национальных облаков Azure. Какие службы доступны, см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Выполните это [библиотеки аутентификации Майкрософт (MSAL) руководства](msal-national-cloud.md) вы научитесь создавать приложения с помощью платформы удостоверений Microsoft. В частности это приложение будет входа пользователей, получите маркер доступа для вызова Microsoft Graph API.

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet в Azure для Китая](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)
- [Основные сведения об аутентификации Azure AD](authentication-scenarios.md)
