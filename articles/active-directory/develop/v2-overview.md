---
title: Сведения о версии 2.0 | Azure
description: Сведения о платформе и конечной точке версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1f707351f5f5887155148b58d5ed145dde785b40
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095420"
---
# <a name="about-v20"></a>Сведения о версии 2.0

Платформа и конечная точка версии 2.0 находились в режиме предварительной версии и постоянно улучшались. Теперь добавлены все функции для сценариев с одностраничными приложениями (SPA) JavaScript. Мы предлагаем вам с помощью MSAL.js создавать браузерные приложения, оставляя свои отзывы, чтобы мы могли обновить предварительную версию до общедоступной (GA).

> [!NOTE]
> Функции для MSAL Android, iOS и .NET все еще разрабатываются. Вы можете использовать их для создания приложений с последующей отправкой отзыва.

На портале Azure значительно улучшен интерфейс функции [Регистрация приложений (предварительная версия)](quickstart-register-app.md), которая теперь включает все приложения, созданные с помощью ADAL или MSAL, и обеспечивает дополнительные возможности отображения.

Раньше разработчикам приложений для реализации поддержки личных учетных записей Майкрософт и рабочих учетных записей Azure Active Directory (Azure AD) необходимо было обеспечивать интеграцию с двумя отдельными системами. Платформа и конечная точка версии 2.0 включают версию API для аутентификации, которая упрощает этот процесс. Это обеспечивает возможность входа из обоих типов учетных записей с применением интеграции. Приложения, использующие конечную точку версии 2.0, также могут использовать интерфейсы REST API из [API Microsoft Graph](https://developer.microsoft.com/graph) с помощью учетной записи любого из этих типов.

## <a name="getting-started"></a>Приступая к работе

Выберите платформу в следующем списке, чтобы создать приложение, используя платформы и библиотеки Майкрософт с открытым исходным кодом.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>См. дополнительные сведения о платформе и конечной точке версии 2.0.

Изучите возможные действия с конечной точкой Azure AD 2.0:

* Узнайте о [типах приложений, которые можно создавать с помощью конечной точки Azure AD 2.0](v2-app-types.md).
* Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.

## <a name="additional-resources"></a>Дополнительные ресурсы

Ниже приведены ресурсы с подробными сведениями о версии 2.0:

* [Общие сведения о платформе удостоверений Майкрософт](about-microsoft-identity-platform.md)
* [Справочник по протоколам версии 2.0](active-directory-v2-protocols.md)
* [Справочник по маркерам доступа](access-tokens.md)
* [Справочник по токенам идентификаторов](id-tokens.md)
* [Справочник по библиотекам проверки подлинности версии 2.0](reference-v2-libraries.md)
* [Разрешения и согласие в версии 2.0](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Если вам нужно использовать вход только с рабочими или учебными учетными записями из Azure Active Directory, начните изучение с [руководства разработчика Azure AD](v1-overview.md). Конечная точка версии 2.0 предназначена для разработчиков, которым необходимо явно выполнять вход с личными учетными записями Майкрософт.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
