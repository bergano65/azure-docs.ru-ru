---
title: включение файла
description: включение файла
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 99ca7e82a11687d25355589e7ea539a14cdb493b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420803"
---
1. Скачайте клиент примеры использования пакета SDK для следующих платформ:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Если вы используете проект iOS необходимо скачать «azuresdk-iOS -\*ZIP-файл» из [последний выпуск GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Распакуйте и добавьте `MicrosoftAzureMobile.framework` файл в корне проекта.
    >

2. Необходимо добавить подключение к базе данных или подключиться к существующему подключению. Сначала определите, можно ли будет создание хранилища данных или использовать уже существующий.

    - **Создать новое хранилище данных**: Если вы собираетесь создать хранилище данных, используйте инструкции из краткого руководства:

        [Краткое руководство Приступая к работе с отдельных баз данных в базе данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Источник данных**: Выполните приведенные ниже инструкции, если вы хотите использовать существующее подключение базы данных

        1. Формат строки подключения базы данных SQL — `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Имя сервера, это можно найти на странице "Обзор" для базы данных и обычно указывается в виде «server_name.database.windows.net».
            **{port}**  обычно 1433.
            **{your_catalogue}**  Имя базы данных.
            **{ваше_имя_пользователя}**  Имя пользователя для доступа к базе данных.
            **{ваш_пароль}**  Пароль для доступа к базе данных.

            [Дополнительные сведения о формате строки подключения SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Добавьте строку подключения к вашей **мобильное приложение** в службе приложений вы можете управлять строки подключения для приложения с помощью **конфигурации** в меню.

            Чтобы добавить строку подключения:

            1. Щелкните **параметры приложения** вкладки.

            2. Щелкните **[+] новая строка подключения**.

            3. Вам нужно будет предоставлять **имя**, **значение** и **типа** для строки подключения.

            4. Тип **имя** как `MS_TableConnectionString`

            5. Значение должно быть, сформированным на шаге перед строку подключения.

            6. При добавлении строки подключения к базе данных SQL Azure выберите **SQLAzure** под **тип**.

3. Мобильные приложения Azure есть пакеты SDK для .NET и Node.js серверных систем.

   - **Сервер Node.js**
    
     Если вы собираетесь использовать приложение быстрого запуска Node.js, следуйте приведенным ниже инструкциям.

     1. На портале Azure перейдите к **простые таблицы**, отобразится следующий экран.
      
        ![Узел простых таблиц](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Убедитесь, что строка подключения SQL уже добавлен в **конфигурации** вкладки. Затем установите флажок для **я подтверждаю, что это перезапишет все содержимое сайта** и нажмите кнопку **создание таблицы TodoItem** кнопки.
     
        ![Конфигурация узла простые таблицы](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. В **простые таблицы**, нажмите кнопку **+ добавить** кнопки.
    
        ![Кнопка "Добавить" простые таблицы узла](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Создание `TodoItem` таблицы с анонимным доступом.
      
        ![Простые таблицы узла добавить таблицу](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Сервер .NET**
    
        Если вы собираетесь использовать приложение быстрого запуска .NET, следуйте приведенным ниже инструкциям.

        1. Скачайте серверный проект .NET для мобильных приложений Azure из [репозитория azure-mobile-apps-краткие руководства](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Создается серверный проект .NET локально в Visual Studio.

        3. В Visual Studio откройте обозреватель решений, щелкните правой кнопкой мыши `ZUMOAPPNAMEService` проекта, щелкните **публикации**, вы увидите `Publish to App Service` окна. Если вы работаете на компьютере Mac, просмотрите другие способы развертывания приложения [здесь](https://docs.microsoft.com/en-us/azure/app-service/deploy-local-git).
        
           ![Публикации в Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Выберите **службы приложений** как целевого объекта публикации, нажмите кнопку **выбрать существующее**, затем нажмите кнопку **публикации** расположенную в нижней части окна.

        5. Необходимо будет войти в Visual Studio с подпиской Azure, сначала. Выберите `Subscription`, `Resource Group`, а затем выберите имя приложения. Когда будете готовы, нажмите кнопку **ОК**, это будет развертывание серверного проекта .NET, у вас есть локально в серверную часть службы приложений. После завершения развертывания вы будете перенаправлены к `http://{zumoappname}.azurewebsites.net/` в браузере.
        
           ![Серверная часть является вверх](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
