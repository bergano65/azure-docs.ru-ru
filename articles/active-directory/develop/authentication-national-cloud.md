---
title: Проверка подлинности Azure AD & национальных облаках Azure
titleSuffix: Microsoft identity platform
description: Узнайте о конечных точках для регистрации и проверки подлинности приложений в национальных облаках.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263001"
---
# <a name="national-clouds"></a>Национальные облака

Национальные облака являются физически изолированными экземплярами Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

Включая глобальное облако, Активный каталог Azure (Azure AD) развертывается в следующих национальных облаках:  

- Azure для государственных организаций
- Azure для Германии
- Azure China 21Vianet

Национальные облака уникальны и являются отдельной средой от Azure global. Важно быть в курсе ключевых различий при разработке приложения для этих сред. Различия включают регистрацию приложений, приобретение токенов и настройку конечных точек.

## <a name="app-registration-endpoints"></a>Конечные точки регистрации приложений

Для каждого из национальных облаков есть отдельный портал Azure. Для интеграции приложений с платформой идентификации Майкрософт в национальном облаке необходимо зарегистрировать приложение отдельно на каждом портале Azure, специфичном для среды.

В следующей таблице перечислены базовые URL-адреса для конечных точек Azure AD, используемые для регистрации приложения для каждого национального облака.

| Национальное облако | Конечная точка портала Azure AD |
|----------------|--------------------------|
| Azure AD для US Gov организаций | `https://portal.azure.us` |
| Azure AD для Германии | `https://portal.microsoftazure.de` |
| Azure AD для Китая через 21Vianet | `https://portal.azure.cn` |
| Azure AD (глобальная служба) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Конечные точки Azure AD для проверки подлинности

Все национальные облака проводят проверку подлинности пользователей в каждой среде отдельно и имеют отдельные конечные точки для проверки подлинности.

В следующей таблице перечислены базовые URL-адреса для конечных точек Azure AD, используемых для приобретения токенов для каждого национального облака.

| Национальное облако | Конечная точка проверки подлинности Azure AD |
|----------------|-------------------------|
| Azure AD для US Gov организаций | `https://login.microsoftonline.us` |
| Azure AD для Германии| `https://login.microsoftonline.de` |
| Azure AD для Китая через 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (глобальная служба)| `https://login.microsoftonline.com` |

Вы можете формировать запросы в авторизацию Azure AD или конечные точки токенов, используя соответствующий базовый URL-адрес для конкретных регионов. Например, для Azure для Германии:

  - стандартная конечная точка авторизации — `https://login.microsoftonline.de/common/oauth2/authorize`;
  - стандартная конечная точка токена — `https://login.microsoftonline.de/common/oauth2/token`.

Для приложений с одним арендатором замените "общие" в предыдущих URL-адресах идентификатором или идентификатором клиента. Например, `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Чтобы узнать, как вызывать AIS Microsoft Graph в национальной облачной среде, перейдите на [Microsoft Graph в национальных облачных развертываниях.](https://developer.microsoft.com/graph/docs/concepts/deployments)

> [!IMPORTANT]
> Некоторые услуги и функции, которые находятся в конкретных регионах глобальной службы, могут быть доступны не во всех национальных облаках. Чтобы узнать, какие услуги доступны, перейдите на [Продукты, доступные по регионам.](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)

Чтобы узнать, как создать приложение с помощью платформы идентификации Майкрософт, следуйте [учебнику Microsoft Authentication Library (MSAL).](msal-national-cloud.md) В частности, это приложение будет войти в пользователя и получить маркер доступа для вызова Microsoft Graph API.

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/)
- [Лазурный Китай 21Вианет](https://docs.microsoft.com/azure/china/)
- [Azure для Германии](https://docs.microsoft.com/azure/germany/)
- [Основы аутентификации Azure AD](authentication-scenarios.md)
