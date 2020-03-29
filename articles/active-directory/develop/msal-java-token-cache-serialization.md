---
title: Пользовательский сериализация кэша токенов (MSAL4j)
titleSuffix: Microsoft identity platform
description: Узнайте, как выставить кэш маркеров для MSAL для Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696169"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Пользовательский сериализация кэша токенов в MSAL для Java

Чтобы сохранить кэш маркеров между экземплярами приложения, необходимо настроить сериализацию. Классы Java и интерфейсы, участвующие в сериализации кэша токенов, следующие:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Интерфейс, представляющий кэш маркеров безопасности.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Интерфейс, представляющий работу выполнения кода до и после доступа. Вы @Override бы *доCacheAccess* и *послеCacheAccess* с логикой, ответственной за сериализации и десериализации кэша.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Интерфейс, представляющий контекст, в котором доступ к кэшу маркеров. 

Ниже приведена наивная реализация пользовательской сериализации сериализации/десериализации кэша токенов. Не копируйте и вставьте это в производственную среду.

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

## <a name="learn-more"></a>Дополнительные сведения

Узнайте о том, как [получить и удалить учетные записи из кэша маркеров с помощью MSAL для Java.](msal-java-get-remove-accounts-token-cache.md)
