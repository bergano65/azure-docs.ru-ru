---
title: Мобильное приложение, что вызовы, веб-API (перемещение в рабочую среду) - платформой Microsoft identity
description: Сведения о создании мобильного приложения, вызовы веб-API-интерфейсы (перемещение в рабочую среду)
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
ms.openlocfilehash: 5cc8b7c86495c2a60b07bef0a825e3872f787520
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550403"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Мобильное приложение, которое вызывает веб-интерфейсы API — переносу в рабочую среду

Эта статья содержит сведения о том, как улучшить качество и надежность приложения перед перемещением в рабочую среду.

## <a name="handling-errors-in-mobile-applications"></a>Обработка ошибок в мобильных приложениях

На этом этапе количества условий возникновения ошибок могут появляться в вашем приложении. Основные сценарии для обработки: автоматические сбои и резервные варианты для взаимодействия. Другие условия, которые следует учитывать для рабочей среды включают ситуации нет сети, простои служб, требования к согласия администратора и в других случаях зависящих от сценария.

Каждая библиотека MSAL имеет образца кода и вики-содержимого, описывающий способ обработки этих условий:

- [Android MSAL вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Сдерживание и изучения проблем

Для диагностики проблем в приложении, он позволяет собирать данные. Сведения о типах данных можно собирать, см. в статье вики-страницы платформы MSAL.

- Пользователи могут попросить о помощи при обнаружении проблем. Мы рекомендуем для записи и временного хранения журналов и указать расположение, где пользователи могут загружать их. MSAL предоставляет расширения ведения журнала, чтобы записать подробные сведения о проверке подлинности.
- Если это возможно, включите данные телеметрии через MSAL для сбора данных о том, как пользователи выполняют вход в приложение.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
