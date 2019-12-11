---
title: Поиск API для специально разработанного приложения | Службы
description: Как настроить разрешения, необходимые для доступа к определенному интерфейсу API, в пользовательском приложении Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c2b88ef00a8a40134462fdc285f1e4c3f12a0b0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963908"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Как найти интерфейс API, необходимый для пользовательского приложения

Для доступа к интерфейсам API необходимо настроить области доступа и роли. Если вы хотите предоставить веб-API приложения-ресурса клиентским приложениям, необходимо настроить области доступа и роли для интерфейсов API. Если вы хотите, чтобы клиентское приложение получило доступ к веб-API, необходимо настроить разрешения на доступ к API-интерфейсу при регистрации приложения.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Настройка приложения ресурсов для предоставления веб-API

При предоставлении веб-API этот интерфейс API будет отображаться в списке **Выберите API** при добавлении разрешений для регистрации приложения. Чтобы добавить области доступа, выполните действия, описанные в разделе [Настройка приложения для предоставления веб-API](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Настройка клиентского приложения для доступа к веб-API

При добавлении разрешений во время регистрации вашего приложения можно **добавить доступ к API** к предоставляемым веб-API. Чтобы получить доступ к веб-API, выполните действия, описанные в разделе [Настройка клиентского приложения для доступа к веб-API](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Основные сведения о манифесте приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
