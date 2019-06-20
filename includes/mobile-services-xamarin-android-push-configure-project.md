---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185793"
---
1. В представлении решения (или **обозревателе решений** в Visual Studio) щелкните правой кнопкой мыши **Компоненты**, выберите пункт **Получить больше компонентов...** , найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.
2. Откройте файл проекта ToDoActivity.cs и добавьте в класс следующую инструкцию using:

    ```csharp
    using Gcm.Client;
    ```

3. Добавьте в класс **ToDoActivity** следующий новый код: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Это позволяет получать доступ к экземпляру мобильного клиента, используя процесс службы обработчика push-действий.
4. После создания **MobileServiceClient** добавьте в метод **OnCreate** следующий код:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Ваш класс **ToDoActivity** теперь готов для добавления push-уведомлений.
