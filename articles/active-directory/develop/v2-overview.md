---
title: Сведения о версии 2.0 | Azure
description: Сведения о платформе и конечной точке версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ecb95f0440751a6cdbf81dbf02c62bed6b5e780b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286696"
---
# <a name="about-v20"></a>Сведения о версии 2.0

Платформа и конечная точка версии 2.0 находились в режиме предварительной версии и постоянно улучшались. Теперь добавлены все функции для сценариев с одностраничными приложениями (SPA) JavaScript. Мы предлагаем вам с помощью MSAL.js создавать браузерные приложения, оставляя свои отзывы, чтобы мы могли обновить предварительную версию до общедоступной (GA).

> [!NOTE]
> Функции для MSAL Android, iOS и .NET все еще разрабатываются. Вы можете использовать их для создания приложений с последующей отправкой отзыва.

На портале Azure значительно улучшен интерфейс для разработчиков, который теперь включает все приложения, созданные с помощью ADAL или MSAL, и предоставляет дополнительные функции отображения.

Раньше разработчикам приложений для реализации поддержки личных учетных записей Майкрософт и рабочих учетных записей Azure Active Directory (Azure AD) необходимо было обеспечивать интеграцию с двумя отдельными системами. Платформа и конечная точка версии 2.0 включают версию API для аутентификации, которая упрощает этот процесс. Это обеспечивает возможность входа из обоих типов учетных записей с применением интеграции. Приложения, использующие конечную точку версии 2.0, также могут использовать интерфейсы REST API из [API Microsoft Graph](https://developer.microsoft.com/graph) с помощью учетной записи любого из этих типов.

## <a name="getting-started"></a>Приступая к работе

Выберите платформу в следующем списке, чтобы создать приложение, используя платформы и библиотеки Майкрософт с открытым исходным кодом. Также можно использовать протоколы OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую без использования библиотеки аутентификации.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>См. дополнительные сведения о платформе и конечной точке версии 2.0.

Изучите возможные действия с конечной точкой Azure AD 2.0:

* Узнайте о [типах приложений, которые можно создавать с помощью конечной точки Azure AD 2.0](v2-app-types.md).
* Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.
* Просмотрите обзорное видео о конечной точке Azure AD версии 2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Дополнительные ресурсы

Ниже приведены ресурсы с подробными сведениями о версии 2.0:

* [Справочник по протоколам версии 2.0](active-directory-v2-protocols.md)
* [Справочник по маркерам доступа](access-tokens.md)
* [Справочник по токенам идентификаторов](id-tokens.md)
* [Справочник по библиотекам проверки подлинности версии 2.0](reference-v2-libraries.md)
* [Разрешения и согласие в версии 2.0](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Если вам нужно использовать вход только с рабочими или учебными учетными записями из Azure Active Directory, начните изучение с [руководства разработчика Azure AD](v1-overview.md). Конечная точка версии 2.0 предназначена для разработчиков, которым необходимо явно выполнять вход с личными учетными записями Майкрософт.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
