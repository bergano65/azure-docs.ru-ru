---
title: Проверка подлинности с помощью Azure AD в национальных облаках
description: Узнайте о конечных точках для регистрации и проверки подлинности приложений в национальных облаках.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.openlocfilehash: dfca40c14ad0da3a3e3a8a32757ec40ace9acf6a
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200570"
---
# <a name="national-clouds"></a>Национальные облака

Национальные облака — это физически изолированные экземпляры Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

Кроме глобального облака, Azure Active Directory развертывается в следующих национальных облаках:  

- Azure для государственных организаций США
- Azure для Германии
- Azure China 21Vianet

Национальные облака являются уникальными и отличаются от глобального Azure средой. Поэтому важно учитывать некоторые ключевые различия при разработке приложения для этих сред, например, регистрация приложений, получения токенов и настройка конечных точек.

## <a name="app-registration-endpoints"></a>Конечные точки регистрации приложений

Для каждого национального облака существует отдельный портал Azure. Чтобы интегрировать приложения с платформой удостоверений Майкрософт в национальном облаке, необходимо зарегистрировать каждое приложение отдельно на соответствующем портале Azure в зависимости от среды.

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD), используемых для регистрации приложения в каждом из национальных облаков.

| Национальное облако | Конечная точка портала Azure AD
| --- | --- |
| Azure AD для государственных организаций США |https://portal.azure.us
|Azure AD для Германии |https://portal.microsoftazure.de
|Azure AD для Китая через 21Vianet |https://portal.azure.cn
|Azure AD (глобальная служба)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Конечные точки Azure AD для проверки подлинности

Все национальные облака проводят проверку подлинности пользователей в каждой среде отдельно и имеют отдельные конечные точки для проверки подлинности.

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD) для получения токенов для каждого национального облака.

| Национальное облако | Конечная точка проверки подлинности Azure AD
| --- | --- |
| Azure AD для государственных организаций США |`https://login.microsoftonline.us`
|Azure AD для Германии| `https://login.microsoftonline.de`
|Azure AD для Китая через 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (глобальная служба)|`https://login.microsoftonline.com`

- Запросы к конечным точкам Azure AD для авторизации или получения маркеров проверки подлинности можно составлять, используя базовый URL-адрес соответствующего региона. Например, для Azure для Германии:

  - стандартная конечная точка авторизации — `https://login.microsoftonline.de/common/oauth2/authorize`;
  - стандартная конечная точка токена — `https://login.microsoftonline.de/common/oauth2/token`.

- В приложениях с одним клиентом замените общее в указанных выше URL-адресах идентификатором или именем клиента, например `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> Конечные точки [Azure AD версии 2.0 для авторизации]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) и получения маркеров проверки подлинности доступны только в глобальной службе. Они пока недоступны для служб, развернутых в национальных облаках.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Для дополнительных сведений см. статью [Национальные облачные развертывания](https://developer.microsoft.com/graph/docs/concepts/deployments).



>[!IMPORTANT]
Некоторые функции и службы, существующие в отдельных регионах глобальной службы, могут быть недоступными для всех национальных облаков Azure. Какие службы доступны, см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Дополнительная информация

- [Документация по Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/).
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/).
- [Документация по Azure для Германии](https://docs.microsoft.com/azure/germany/).
- [Аутентификация в Azure Active Directory](authentication-scenarios.md).
