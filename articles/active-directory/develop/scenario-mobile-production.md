---
title: Мобильное приложение, вызывающее веб-API (переместить в рабочую среду) — платформа Microsoft Identity
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (переместить в рабочую среду).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e778e55bd87e325b3dcf14a8c9f1616157a420b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320890"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Мобильное приложение, вызывающее веб-API — переместить в рабочую среду

Эта статья содержит сведения о том, как улучшить качество и надежность приложения перед его переносом в рабочую среду.

## <a name="handling-errors-in-mobile-applications"></a>Обработка ошибок в мобильных приложениях

На этом этапе в приложении может произойти ряд ошибок. Основными сценариями, которые необходимо решить, являются автоматические сбои и откат к взаимодействию. Другие условия, которые следует учитывать при работе в рабочей среде, включают в себя несетевые ситуации, простои служб, требования для предоставления прав администратора и другие случаи, связанные с сценариями.

В каждой библиотеке MSAL есть образец кода и вики-содержимое, описывающие способ решения этих условий.

- [Вики-сайт MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Вики-сайт MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Вики-сайт MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Устранение неполадок и их исследование

Чтобы диагностировать проблемы в приложении, он помогает получать данные. Сведения о типах данных, которые можно отслеживать, см. на странице вики-страницы MSAL Platform.

- Пользователи могут запрашивать помощь при возникновении проблем. Рекомендуется записывать и временно сохранять журналы, а также указывать расположение, в котором пользователи могут отправлять их. MSAL предоставляет расширения ведения журнала для записи подробных сведений о проверке подлинности.
- Если он доступен, включите телеметрию с помощью MSAL, чтобы собрать данные о том, как пользователи входят в приложение.

## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
