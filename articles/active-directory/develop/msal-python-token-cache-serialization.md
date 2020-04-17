---
title: Пользовательский сериализации кэша токенов (MSAL Python) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как сериализовать кэш маркеров для MSAL для Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9c6edd0b3cfd6620f04553f9f6dfe89f1c7b7024
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536205"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Пользовательский кэш-кэш-токен в MSAL для Python

В MSAL Python кэш маркеров памяти, сохранящийся в течение сеанса приложения, предоставляется по умолчанию при создании экземпляра [ClientApplication.](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

Сериализация кэша маркеров, чтобы различные сеансы приложения могли получить к нему доступ, не предоставляется "из коробки". Это связано с тем, что MSAL Python может использоваться в типах приложений, которые не имеют доступа к файловой системе, например, в веб-приложениях. Чтобы иметь постоянный кэш маркеров в приложении MSAL Python, необходимо обеспечить сериализацию кэша маркеров.

Стратегии сериализации кэша токенов различаются в зависимости от того, пишете ли вы публичное клиентское приложение (Desktop) или конфиденциальное клиентское приложение (веб-приложение, веб-aPI или приложение daemon).

## <a name="token-cache-for-a-public-client-application"></a>Кэш маркеров для публичного клиентского приложения

Приложения для публичных клиентов работают на устройстве пользователя и управляют токенами для одного пользователя. В этом случае можно выставить весь кэш в файл. Не забудьте предоставить блокировку файлов, если ваше приложение или другое приложение могут одновременно получить доступ к кэшу. Простой пример того, как сериализовать кэш маркера в файл без [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) блокировки, см.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Кэш маркеров для веб-приложения (конфиденциальное клиентское приложение)

Для веб-приложений или web-aI можно использовать сеанс, кэш Redis или базу данных для хранения кэша маркеров. На одну учетную запись должен быть один кэш маркеров (на учетную запись), чтобы обеспечить сериализацию кэша маркеров на учетную запись.

## <a name="next-steps"></a>Следующие шаги

Например, пример использования кэша маркеров для Windows или Linux Web-приложения или web-API можно просмотреть [ms-identity-python-webapp.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) Пример омещен для веб-приложения, которое вызывает API Microsoft Graph.
