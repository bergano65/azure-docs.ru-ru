---
title: Подготовка мобильных приложений — вызов веб-API для рабочей среды | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, вызывающее веб-API. (Подготовка приложений для рабочей среды.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882545"
---
# <a name="prepare-mobile-apps-for-production"></a>Подготовка мобильных приложений для рабочей среды

В этой статье содержатся сведения о том, как улучшить качество и надежность мобильного приложения перед его переносом в рабочую среду.

## <a name="handle-errors"></a>Обработка ошибок

При подготовке мобильного приложения для рабочей среды может возникнуть несколько причин возникновения ошибки. К основным случаям, которые вы будете справляться, относятся аварийные сбои и переходы на взаимодействие. В число других условий, которые следует учитывать, входят ситуации отсутствия сети, простои служб, требования для предоставления прав администратора и другие случаи, связанные с сценариями.

Для каждого типа библиотеки проверки подлинности Майкрософт (MSAL) можно найти образцы кода и вики-страницы, описывающие способы устранения ошибок.

- [Вики-сайт MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Вики-сайт MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Вики-сайт MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Устранение неполадок и их анализ

Чтобы лучше диагностировать проблемы в приложении, собирайте данные. Сведения о типах данных, которые можно отслеживать, см. в разделе [ведение журнала в приложениях MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Ниже приведены некоторые рекомендации по сбору данных.

- Пользователи могут запросить помощь в случае возникновения проблем. Рекомендуется записывать и временно сохранять журналы. Укажите расположение, в котором пользователи могут отправлять журналы. MSAL предоставляет расширения ведения журнала для записи подробных сведений о проверке подлинности.

- Если данные телеметрии доступны, включите ее через MSAL для сбора данных о том, как пользователи входят в приложение.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Чтобы испытать дополнительные примеры, см. раздел [общедоступные клиентские приложения для настольных систем и мобильных устройств](sample-v2-code.md#desktop-and-mobile-public-client-apps).
