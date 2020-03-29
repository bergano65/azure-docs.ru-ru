---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262026"
---
## <a name="authenticate-with-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory

> [!IMPORTANT]
> 1. В настоящее время **только** API компьютерного зрения, Face API, Text Analytics API, Immersive Reader, Распознавание форм, детектор аномалий и все службы Bing Custom Search поддерживают аутентификацию с помощью Azure Active Directory (AAD).
> 2. Аутентификация AAD должна всегда использоваться вместе с пользовательским поддоменным именем ресурса Azure. [Региональные конечные точки](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) не поддерживают аутентификацию AAD.

В предыдущих разделах мы показали, как проверить подлинность в отношении когнитивных служб Azure с помощью ключа подписки с одним сервисом или несколькими службами. Хотя эти клавиши обеспечивают быстрый и простой путь для начала разработки, они не отвечают более сложным сценариям, которые требуют элементов управления доступом на основе ролей. Давайте взглянем на то, что требуется для проверки подлинности с помощью Active Directory Azure (AAD).

В следующих разделах необходимо использовать среду обложечной оболочки Azure или ClI Azure для создания поддояна, присвоения ролей и получения маркера носителя для вызова когнитивных служб Azure. При застрять в каждом разделе предоставляются ссылки со всеми доступными опциями для каждой команды в Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Создание ресурса с пользовательским поддоменом

Первым шагом является создание пользовательского поддомена. Если вы хотите использовать существующий ресурс Cognitive Services, который не имеет пользовательского названия поддоменов, следуйте инструкциям в [пользовательских поддоменах Cognitive Services Custom Subdomains,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) чтобы включить пользовательский поддомен для вашего ресурса.

1. Начните с открытия облачной оболочки Azure. Затем [выберите подписку:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Затем [создайте ресурс Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) с пользовательским поддоменом. Название поддомена должно быть глобально уникальным и не может включать в себя специальные символы, такие как: ".,", "!", "."

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. В случае **успеха, конечная точка** должна показать имя поддомена, уникальное для вашего ресурса.


### <a name="assign-a-role-to-a-service-principal"></a>Назначение роли субъекту-службе.

Теперь, когда у вас есть пользовательский поддомен, связанный с вашим ресурсом, вам нужно будет назначить роль директору службы.

> [!NOTE]
> Имейте в виду, что для распространения заданий ролей AAD может потребоваться до пяти минут.

1. Во-первых, давайте зарегистрируем [приложение AAD.](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   На следующем этапе вам понадобится **ApplicationId.**

2. Далее необходимо [создать принцип службы](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) для приложения AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Если вы регистрируете заявку на портале Azure, этот шаг завершен для вас.

3. Последним шагом является [присвоение роли "Пользователе когнитивных услуг"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) директору службы (скругу по ресурсу). Назначая роль, вы предоставляете основной доступ к этому ресурсу службы. Вы можете предоставить один и тот же основной доступ к нескольким ресурсам в подписке.
   >[!NOTE]
   > Для приложения используется ObjectId основного обслуживания, а не ObjectId.
   > ACCOUNT_ID будет идентификатором ресурса Azure созданной вами учетной записи Когнитивных Служб. Идентификатор ресурса Azure можно найти на портале Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Пример запроса

В этом примере пароль используется для проверки подлинности основного обслуживания. Предоставленный токен затем используется для вызова API компьютерного зрения.

1. Получите ваш **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Получить токен:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Позвоните в API компьютерного зрения:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Кроме того, директор службы может быть проверен сертификатом. Помимо основного обслуживания, основной пользователь также поддерживается, если разрешения делегируются через другое приложение AAD. В этом случае вместо паролей или сертификатов пользователям будет предложено провести двухфакторную аутентификацию при приобретении токена.
