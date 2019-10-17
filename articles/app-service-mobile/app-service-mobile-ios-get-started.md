---
title: Создание мобильного приложения для iOS в службе мобильных приложений Azure | Документация Майкрософт
description: Изучите этот учебник, чтобы начать работу с серверным частями мобильных приложений Azure для разработки приложений iOS на Objective-C или Swift
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a0b73ba248ae9a12f9ee01f591ddbbedecc25f70
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388683"
---
# <a name="create-an-ios-app"></a>Создание приложения для iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

## <a name="overview"></a>Краткое описание

В этом руководстве показано, как добавить облачную серверную службу [Мобильные приложения службы приложений](app-service-mobile-value-prop.md) в приложение iOS. Сначала в Azure создается мобильный внутренний сервер. Затем скачивается простой пример приложения iOS со *списком задач*, в котором хранятся данные в Azure.

Для работы с этим учебником требуется [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание подключения к базе данных и настройка клиентского и серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Запуск приложения iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
