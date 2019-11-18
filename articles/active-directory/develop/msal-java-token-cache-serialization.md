---
title: Сериализация пользовательского кэша маркеров в MSAL для Java
titleSuffix: Microsoft identity platform
description: Сведения о сериализации кэша маркеров для MSAL для Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7274a1b6b5ebf5b55c2cad4b52dfe4c997e26314
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145899"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Сериализация пользовательского кэша маркеров в MSAL для Java (MSAL4J)

Чтобы сохранить кэш маркеров между экземплярами приложения, необходимо настроить сериализацию. Классы и интерфейсы Java, участвующие в сериализации кэша маркеров, следующие:

- [Итокенкаче](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): интерфейс, представляющий кэш маркеров безопасности.
- [Итокенкачеакцессаспект](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): интерфейс, представляющий операцию выполнения кода до и после доступа. Вы @Override *бефорекачеакцесс* и *афтеркачеакцесс* с логикой, отвечающей за сериализацию и десериализацию кэша.
- [Итокенкачеконтекст](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): интерфейс, представляющий контекст, в котором осуществляется доступ к кэшу маркера. 

Ниже приведена упрощенная реализация пользовательской сериализации сериализации и десериализации кэша маркеров. Не копируйте и не вставляйте его в рабочую среду.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Подробнее

Узнайте [, как получать и удалять учетные записи из кэша маркеров с помощью MSAL для Java](msal-java-get-remove-accounts-token-cache.md).
