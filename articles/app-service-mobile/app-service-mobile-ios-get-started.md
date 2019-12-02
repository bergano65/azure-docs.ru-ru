---
title: Создание приложения для iOS
description: Следуйте указаниям этого руководства, чтобы приступить к работе с мобильное приложение Azure серверной стороной для разработки iOS в цели-C или SWIFT.
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d933319d22fe7622f0409e8931c41a801fcd18ca
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668791"
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
