---
title: Библиотеки управления служебной шины Azure | Документация Майкрософт
description: В этой статье объясняется, как использовать библиотеки управления служебной шины Azure для динамической инициализации пространств имен и сущностей служебной шины.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 63a8f0f8ef299f9d27d3c1be2746052536c4e5e8
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066174"
---
# <a name="service-bus-management-libraries"></a>Библиотеки управления служебной шины

Библиотеки управления служебной шины Azure могут динамически подготавливать пространства имен и сущности служебной шины. Это дает возможность реализовывать сложные развертывания и сценарии обмена сообщениями и позволяет программно определять, какие сущности следует подготовить. В настоящее время эти библиотеки доступны для .NET.

## <a name="supported-functionality"></a>Поддерживаемые функции

* Создание, обновление, удаление пространства имен.
* Создание, обновление, удаление очереди.
* Создание, обновление, удаление раздела.
* Создание, обновление, удаление подписки.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе с библиотеками управления служебной шины, нужно пройти аутентификацию в службе Azure Active Directory (Azure AD). Azure AD требует выполнять аутентификацию в качестве субъекта-службы, который предоставляет доступ к вашим ресурсам Azure. Сведения о создании субъекта-службы см. в одной из приведенных ниже статей:  

* [Используйте портал Azure, чтобы создать Active Directory приложения и субъекта-службы, которые могут получать доступ к ресурсам.](../active-directory/develop/howto-create-service-principal-portal.md)
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

В этих руководствах вы получите `AppId` (идентификатор клиента), `TenantId` и `ClientSecret` (ключ аутентификации), которые используются библиотеками управления для аутентификации. Необходимо иметь по крайней мере разрешения владельца данных или [**участника**](../role-based-access-control/built-in-roles.md#contributor) [**служебной шины Azure**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) для группы ресурсов, которую вы хотите запустить.

## <a name="programming-pattern"></a>Шаблон программирования

Шаблон обработки любого ресурса служебной шины соответствует общему протоколу.

1. Получите маркер из Azure AD с помощью библиотеки **Microsoft.IdentityModel.Clients.ActiveDirectory**:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Создайте объект `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Присвойте параметрам `CreateOrUpdate` указанные значения:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Выполните вызов:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Завершение кода для создания очереди
Ниже приведен полный код для создания очереди служебной шины. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Полный пример см. в [примере управления .NET на сайте GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Дальнейшие действия
[Справочник по API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)