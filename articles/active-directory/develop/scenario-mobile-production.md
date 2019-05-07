---
title: Мобильное приложение, что вызовы, веб-API (перемещение в рабочую среду) - платформой Microsoft identity
description: Узнайте, как создать мобильное приложение, которое вызывает веб-API (перемещение в рабочую среду)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074955"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Мобильное приложение, которое вызывает веб-интерфейсы API — переносу в рабочую среду

Эта статья содержит сведения о повышении качества и надежности вашего приложения, чтобы переместить его в рабочую среду.

## <a name="handling-errors-in-mobile-applications"></a>Обработка ошибок в мобильных приложениях

В разных потоков, которые мы до сих выделен существует множество ошибок, вызванных. Автоматические сбои и Базовое взаимодействие, является основным сценарием для обработки. Существуют дополнительные условия, которые также следует учитывать для рабочей среды, включая ситуации нет сети, простои служб, требуется согласие администратора и в других случаях зависящих от сценария.

Каждая библиотека MSAL образец кода и вики-содержимое включает подробно рассказывает о глубже обработка этих условий.

- [Android MSAL вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Сдерживание и изучения проблем

Сбор данных поможет диагностировать проблемы приложения. Дополнительные сведения о тип данных можно собирать, каждый вики-сайте MSAL платформ см. в разделе.

- Пользователям может потребоваться помощь при обнаружении проблемы. Мы рекомендуем для записи и хранения журналов временно и разрешить пользователям отправлять их где-нибудь. MSAL предоставляют модули ведения журнала для записи подробных сведений о проверки подлинности
- Если он доступен, включите данные телеметрии через MSAL для сбора данных о том, как пользователи входят в ваше приложение.

## <a name="testing-your-app"></a>Тестирование приложения

Не забудьте протестировать приложение с [контрольный список для интеграции](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
