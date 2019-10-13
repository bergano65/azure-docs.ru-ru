---
title: Проверка подлинности Azure Active Directory (Azure AD)
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как создать новый иммерсивное устройство чтения с помощью пользовательского поддомена, а затем настроить Azure AD в клиенте Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d51c27b90113679c1547f2d030459a03cc22c80c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299805"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Использование проверки подлинности Azure Active Directory (Azure AD) с помощью службы иммерсивное средство чтения

В следующих разделах вы будете использовать Azure Cloud Shellную среду или Azure PowerShell, чтобы создать новый иммерсивное устройство чтения с пользовательским поддоменом, а затем настроить Azure AD в клиенте Azure. После завершения начальной настройки вы назовите Azure AD, чтобы получить маркер доступа, аналогично тому, как он будет выполняться при использовании пакета SDK для иммерсивное средство чтения. Если вы зависает, в каждом разделе содержатся ссылки со всеми доступными параметрами для каждой из Azure PowerShellных команд.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Создание иммерсивного ресурса чтения с помощью пользовательского поддомена

1. Для начала откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Затем [выберите подписку](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Затем [Создайте иммерсивное устройство чтения](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) с пользовательским поддоменом.

   >[!NOTE]
   > Имя поддомена используется в пакете SDK для иммерсивного чтения при запуске средства чтения с помощью функции Лаунчасинк.

   -SkuName может быть F0 (бесплатный уровень) или S0 (уровень "Стандартный", также бесплатный в рамках общедоступной предварительной версии). Уровень S0 имеет более высокий предел скорости вызовов и не имеет месячной квоты на количество вызовов.

   -Location может быть любым из следующих: `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`, `westeurope`

   -Кустомсубдомаиннаме должен быть глобально уникальным и не может содержать специальные символы, например ".", "!", ",".


   ```azurepowershell-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   В случае успеха **Конечная точка** ресурса должна отображать имя поддомена, уникальное для ресурса.

   Здесь мы записываем созданный объект ресурса в переменную **$Resource** , так как она будет использоваться позже при предоставлении доступа к этому ресурсу.


   >[!NOTE]
   > При создании ресурса в портал Azure ресурс "имя" используется в качестве пользовательского поддомена. Чтобы проверить имя поддомена на портале, перейдите на страницу обзора ресурсов и найдите поддомен в указанной конечной точке, например `https://[SUBDOMAIN].cognitiveservices.azure.com/`. Вы также можете проверить это позже, когда необходимо получить поддомен для интеграции с пакетом SDK.

   Если ресурс был создан на портале, можно также [получить существующий ресурс](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) .

   ```azurepowershell-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Назначение роли субъекту-службе.

Теперь, когда у вас есть пользовательский поддомен, связанный с ресурсом, необходимо назначить роль субъекту-службе.

1. Сначала создадим [приложение Azure AD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > Пароль, также известный как "Секрет клиента", будет использоваться при получении маркеров проверки подлинности.

   ```azurepowershell-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Здесь мы записываем созданный объект приложения Azure AD в переменную **$aadApp** для использования на следующем шаге.

2. Далее необходимо [создать субъект-службу](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) для приложения Azure AD.

   ```azurepowershell-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Здесь мы записываем созданный объект субъекта-службы в переменную **$Principal** для использования на следующем шаге.


3. Последним шагом является [назначение роли "Cognitive Services пользователя"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) субъекту-службе (областью действия ресурса). Назначая роль, вы предоставляете субъекту-службе доступ к этому ресурсу. Вы можете предоставить одному субъекту-службе доступ к нескольким ресурсам в подписке.

   ```azurepowershell-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Этот шаг необходимо выполнить для каждого создаваемого иммерсивного ресурса чтения. Например, если вы создадите несколько ресурсов для разных глобальных регионов, вам потребуется выполнить этот шаг для каждого из этих региональных ресурсов, чтобы проверка подлинности Azure AD работала для всех. Кроме того, если вы создадите новый ресурс позже, вам потребуется также выполнить этот шаг назначения роли для этого нового ресурса.


## <a name="obtain-an-azure-ad-token"></a>Получение маркера Azure AD

В этом примере пароль используется для проверки подлинности субъекта-службы с целью получения маркера Azure AD.

1. Получите **ИД**клиента:
   ```azurepowershell-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Получение маркера:
   ```azurepowershell-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > В пакете SDK для иммерсивного чтения используется свойство маркера AccessToken, например $token. AccessToken. Дополнительные сведения см . в [справочнике](reference.md) по SDK и [примерах](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) кода.

Кроме того, можно проверить подлинность субъекта-службы с помощью сертификата. Кроме субъекта-службы, субъекты-пользователи также поддерживаются, если разрешения делегированы через другое приложение Azure AD. В этом случае вместо паролей или сертификатов пользователям будет предложено использовать двухфакторную проверку подлинности при получении маркеров.

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь с [руководством для разработчиков Node.js](./tutorial-nodejs.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Node.js.
* Ознакомьтесь с [руководством для разработчиков Python](./tutorial-python.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Python.
* Просмотрите [учебник по SWIFT](./tutorial-ios-picture-immersive-reader.md) , чтобы узнать, что еще можно сделать с помощью пакета SDK для иммерсивное средство чтения, используя SWIFT.
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).