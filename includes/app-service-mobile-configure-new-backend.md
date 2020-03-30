---
title: включить файл
description: включить файл
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440217"
---
1. Скачать клиент SDK quickstarts для следующих платформ:
    
    [iOS (Объектив-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Свифт)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Окна (C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Если вы используете проект iOS, вам нужно скачать "azuresdk-iOS-\*.zip" из [последнего релиза GitHub.](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest) Расстегайте `MicrosoftAzureMobile.framework` и добавьте файл в корень проекта.
    >

2. Вам придется добавить подключение к базе данных или подключиться к существующему подключению. Во-первых, определите, создадите ли вы хранилище данных или будете использовать существующий.

    - **Создайте новый хранилище данных:** Если вы собираетесь создать хранилище данных, используйте следующий быстрый запуск:

        [Быстрый запуск: Начало работы с единичными базами данных в базе данных Azure S'L](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Существующий источник данных**: Следуйте инструкциям ниже, если вы хотите использовать существующее соединение базы данных

        1. Формат подключения к базе данных S'L -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **(your_SQLServer)** Имя сервера, это можно найти на странице обзора для вашей базы данных и, как правило, в виде "server_name.database.windows.net".
            **(порт)** обычно 1433.
            **(your_catalogue)** Название базы данных.
            **(your_username)** Имя пользователя для доступа к базе данных.
            **(your_password)** Пароль для доступа к базе данных.

            [Узнать больше о формате строки подключения S'L](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Добавление строки соединения в **мобильное приложение** In App Service можно управлять строками соединения для приложения, используя опцию **Конфигурация** в меню.

            Чтобы добавить строку соединения:

            1. Нажмите на вкладку **настроек приложения.**

            2. Нажмите на **строку «Новая строка соединения».**

            3. Вам нужно будет узнать **имя,** **значение** и **тип** строки соединения.

            4. **Введите имя** как`MS_TableConnectionString`

            5. Значение должно быть соединительной строкой, сформированной в шаге раньше.

            6. Если вы добавляете строку соединения в базу данных S'L Azure, выберите **S'LAzure** по **типу.**

3. У мобильных приложений Azure есть SDK для задней части .NET и Node.js.

   - **Серверная часть Node.js**
    
     Если вы собираетесь использовать приложение node.js quickstart, следуйте инструкциям ниже.

     1. На портале Azure перейдите на **простые таблицы,** вы увидите этот экран.
      
        ![Легкие таблицы узлов](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Убедитесь, что строка подключения S'L уже добавлена во вкладке **Конфигурация.** Затем проверьте поле **я признаю, что это будет переписать все содержимое сайта** и нажмите **кнопку таблицы Создать TodoItem.**
     
        ![Конфигурация простых таблиц узлов](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. В **простых таблицах**щелкните кнопку **«Добавить».**
    
        ![Легкие таблицы узла Добавить кнопку](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Создайте `TodoItem` таблицу с анонимным доступом.
      
        ![Легкие таблицы узла Добавить таблицу](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Сервер .NET**
    
        Если вы собираетесь использовать приложение быстрого запуска .NET, следуйте инструкциям ниже.

        1. Скачать проект сервера Azure Mobile Apps .NET из [репозитория azure-mobile-apps-quickstarts.](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)

        2. Создайте проект сервера .NET локально в Visual Studio.

        3. В Visual Studio, open Solution Explorer, справа щелкните `ZUMOAPPNAMEService` `Publish to App Service` по проекту, нажмите **Publish,** вы увидите окно. Если вы работаете над Mac, проверьте другие способы развертывания приложения [здесь](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Визуальное издательство студии](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Выберите **службу app в** качестве целевого доступа, а затем нажмите **Select Existing,** затем нажмите кнопку **«Публикация»** в нижней части окна.

        5. Сначала необходимо войти в Visual Studio с подпиской Azure. Выберите `Subscription` `Resource Group`, а затем выберите имя вашего приложения. Когда вы будете готовы, нажмите **OK,** это развернет проект сервера .NET, который у вас есть локально в бэкэнд службы приложений. По завершении развертывания вы будете `http://{zumoappname}.azurewebsites.net/` перенаправлены в браузере.                   