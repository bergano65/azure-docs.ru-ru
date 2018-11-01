---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: ab9d059ede2464318205a90b8ac738727e8d89a4
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164520"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Получение маркера Azure Resource Manager
Все задачи, выполняемые с ресурсами с помощью диспетчера ресурсов Azure, должны пройти проверку подлинности Azure Active Directory. В примере ниже демонстрируется проверка подлинности с использованием пароля (другие способы см. в [справочнике по Azure REST API][lnk-authenticate-arm]).

1. Добавьте в метод **Main** в файле Program.cs приведенный ниже код, позволяющий получить из системы Azure AD маркер с помощью идентификатора приложения и пароля.
   
    ```
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
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Создайте группу ресурсов, которую будете использовать, или получите ссылку на нее:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx