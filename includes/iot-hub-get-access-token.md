---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803012"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Получение маркера Azure Resource Manager
Все задачи, выполняемые с ресурсами с помощью диспетчера ресурсов Azure, должны пройти проверку подлинности Azure Active Directory. В приведенном ниже примере используется проверка подлинности с помощью пароля. другие подходы см. в разделе [Проверка подлинности запросов Azure Resource Manager][lnk-authenticate-arm].

1. Добавьте в метод **Main** в файле Program.cs приведенный ниже код, позволяющий получить из системы Azure AD маркер с помощью идентификатора приложения и пароля.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Создайте объект **ResourceManagementClient**, который использует полученный маркер, добавив следующий код в конец метода **Main**:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Создайте группу ресурсов, которую будете использовать, или получите ссылку на нее:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx