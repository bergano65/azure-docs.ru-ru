---
title: Начало работы с приложениями Xamarin.iOS
description: Этот учебник поможет приступить к использованию мобильных приложений в разработке для Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459010"
---
# <a name="create-a-xamarinios-app"></a>Создание приложения Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение Xamarin.iOS с помощью серверной части мобильного приложения Azure.  Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.iOS *Todo list*, в котором в Azure хранятся данные приложения.

Завершение этого учебника является необходимым условием для других учебников по Xamarin.iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным руководством вам потребуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, зарегистрируйтесь для получения бесплатной пробной версии Azure и получите до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Для получения подробной информации [см.](https://azure.microsoft.com/pricing/free-trial/)
* Visual Studio для Mac. Смотрите [Настройка и установка для визуальной студии для Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Mac с Xcode 9.0 или более поздно.
  
## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Создание соединения базы данных и настройка проекта клиента и сервера
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Выполнить приложение Xamarin.iOS
1. Откройте проект Xamarin.iOS.

2. Перейдите на [портал Azure](https://portal.azure.com/) и перейдите к созданного вами мобильному приложению. На `Overview` лезвии ищите URL-адрес, который является общедоступной точкой зрения для вашего мобильного приложения. Пример - название сайта для моего приложения имя https://test123.azurewebsites.net"test123" будет .

3. Откройте `QSTodoService.cs` файл в этой папке - xamarin.iOS/ЗУМОАППНАМЕ. Имя приложения — `ZUMOAPPNAME`.

4. В `QSTodoService` классе `ZUMOAPPURL` замените переменную общедоступной конечной точкой выше.

    `const string applicationURL = @"ZUMOAPPURL";`

    becomes
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Нажмите клавишу F5, чтобы развернуть и запустить приложение в эмуляторе iPhone.

6. В приложении введите значимый текст, например, *заполните учебник,* а затем нажмите кнопку «К.».

    ![][10]

    Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются серверной частью мобильного приложения, а данные отображаются в списке.

   > [!NOTE]
   > Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
