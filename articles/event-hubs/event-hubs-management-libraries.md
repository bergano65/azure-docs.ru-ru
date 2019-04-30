---
title: Библиотеки управления Центров событий Azure | Документация Майкрософт
description: В этой статье приведены сведения о библиотеке, с помощью которой можно управлять пространствами имен и сущностями службы "Центры событий Azure" с помощью .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746664"
---
# <a name="event-hubs-management-libraries"></a>Библиотеки управления Центров событий

Библиотеки управления Центров событий Azure можно использовать, чтобы динамически подготавливать пространства имен и сущности Центров событий. Это дает возможность реализовывать сложные развертывания и сценарии обмена сообщениями, позволяя программно определять, какие сущности следует подготовить. В настоящее время эти библиотеки доступны для .NET.

## <a name="supported-functionality"></a>Поддерживаемые функции

* Создание, обновление, удаление пространства имен.
* Создание, обновление, удаление концентраторов событий в службе "Центры событий".
* Создание, обновление, удаление группы потребителей.

## <a name="prerequisites"></a>Технические условия

Чтобы приступить к работе с библиотеками управления Центров событий, нужно пройти аутентификацию в Azure Active Directory (AAD). AAD требует аутентификации в качестве субъекта-службы, предоставляющего доступ к вашим ресурсам Azure. Сведения о создании субъекта-службы см. в одной из приведенных ниже статей:  

* [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md)
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

В этих руководствах вы получите `AppId` (идентификатор клиента), `TenantId` и `ClientSecret` (ключ аутентификации), которые используются библиотеками управления для аутентификации. Необходимо иметь разрешения роли **Владелец** для группы ресурсов, которую вы хотите использовать.

## <a name="programming-pattern"></a>Шаблон программирования

Шаблон обработки любого ресурса Центров событий придерживается общего протокола.

1. Получение токена из AAD с использованием библиотеки `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Создание объекта `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Присвоение параметрам `CreateOrUpdate` указанных значений.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Выполнение вызова.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Пример управления для .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Справочник по Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
