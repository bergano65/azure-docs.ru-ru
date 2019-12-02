---
title: Начало работы с приложениями Xamarin. iOS
description: Этот учебник поможет приступить к использованию мобильных приложений в разработке для Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 2bb13393a9f348d85ab9fc964b784ba4d4d6a783
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668617"
---
# <a name="create-a-xamarinios-app"></a>Создание приложения Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы, необходимые для разработки мобильных приложений. Разработчики могут использовать службы **Build**, **Test** и **Distribute** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать его состояние и использование с помощью служб **Analytics** и **Diagnostics**, а также взаимодействовать с пользователями с помощью службы **Push**. Разработчики также могут использовать **Auth** для реализации проверки подлинности пользователей и службу **Data** для хранения и синхронизации данных приложений в облаке.
>
> Если вы хотите интегрировать облачные службы с мобильным приложением, зарегистрируйтесь в [Центре приложений](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) сегодня.

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

2. Перейдите к [портал Azure](https://portal.azure.com/) и перейдите к созданному вами мобильному приложению. В колонке `Overview` найдите URL-адрес, который является общедоступной конечной точкой для мобильного приложения. Пример. имя сайта для имени приложения "test123" будет https://test123.azurewebsites.net.

3. Откройте файл `QSTodoService.cs` в этой папке — Xamarin. iOS/ЗУМОАППНАМЕ. Имя приложения — `ZUMOAPPNAME`.

4. В `QSTodoService` классе Замените переменную `ZUMOAPPURL` на общую конечную точку выше.

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