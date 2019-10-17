---
title: Приступая к работе с мобильными приложениями службы приложений Azure для приложений Xamarin.iOS | Документация Майкрософт
description: Этот учебник поможет приступить к использованию мобильных приложений в разработке для Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 14fb925896fc94def59b753711eb74f155b2e139
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388354"
---
# <a name="create-a-xamarinios-app"></a>Создание приложения Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы для разработки мобильных приложений. Разработчики могут использовать службы **сборки**, **тестирования** и **распространения** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать состояние и использование своих приложений с помощью служб **аналитики** и **диагностики** , а также привлекать пользователей с помощью службы **push-уведомлений** . Разработчики также **могут использовать проверку подлинности** для аутентификации пользователей и службы **данных** , чтобы сохранять и синхронизировать данные приложений в облаке.
>
> Если вы хотите интегрировать облачные службы в мобильное приложение, зарегистрируйтесь в [центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) уже сегодня.

## <a name="overview"></a>Краткое описание
В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение Xamarin.iOS с помощью серверной части мобильного приложения Azure.  Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.iOS *Todo list*, в котором в Azure хранятся данные приложения.

Завершение этого учебника является необходимым условием для других учебников по Xamarin.iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

## <a name="prerequisites"></a>Технические условия
Для работы с данным руководством вам потребуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, зарегистрируйтесь для получения бесплатной пробной версии Azure и получите до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/);
* Visual Studio для Mac. См. раздел [Установка и установка для Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Компьютер Mac с Xcode 9,0 или более поздней версии.
  
## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание подключения к базе данных и настройка клиентского и серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Запуск приложения Xamarin. iOS
1. Откройте проект Xamarin. iOS.

2. Перейдите к [портал Azure](https://portal.azure.com/) и перейдите к созданному вами мобильному приложению. В колонке `Overview` найдите URL-адрес, который является общедоступной конечной точкой для мобильного приложения. Пример. имя сайта для имени моего приложения "test123" будет https://test123.azurewebsites.net.

3. Откройте файл `QSTodoService.cs` в этой папке — Xamarin. iOS/ЗУМОАППНАМЕ. Имя приложения — `ZUMOAPPNAME`.

4. В классе `QSTodoService` Замените переменную `ZUMOAPPURL` общедоступной конечной точкой выше.

    `const string applicationURL = @"ZUMOAPPURL";`

    обретает
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Нажмите клавишу F5, чтобы развернуть и запустить приложение в эмуляторе iPhone.

6. В приложении введите содержательный текст, например *Завершение работы с руководством* , а затем нажмите кнопку +.

    ![][10]

    Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются серверной частью мобильного приложения, а данные отображаются в списке.

   > [!NOTE]
   > Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png