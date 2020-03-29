---
title: Создание ресурса иммерсивного средства чтения
titleSuffix: Azure Cognitive Services
description: В этой статье будет показан способ создания нового ресурса Immersive Reader с пользовательским поддоменом, а затем настроить Azure AD в вашем арендаторе Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330725"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Создайте ресурс Immersive Reader и наверскакните активную проверку каталога Azure

В этой статье мы предоставляем скрипт, который создаст ресурс Immersive Reader и наставит аттенцитарную проверку Azure Active Directory (Azure AD). Каждый раз, когда создается ресурс Immersive Reader, будь то с помощью этого скрипта или на портале, он также должен быть настроен с разрешениями Azure AD. Этот скрипт поможет вам в этом.

Скрипт предназначен для создания и настройки всех необходимых ресурсов Immersive Reader и Azure AD для всех вас в один шаг. Однако можно также просто настроить аутентификацию Azure AD для существующего ресурса Immersive Reader, если, например, вы уже создали его на портале Azure.

Для некоторых клиентов может потребоваться создание нескольких ресурсов Immersive Reader для разработки по сравнению с производством или, возможно, для нескольких различных регионов, в которых развернута служба. В этих случаях можно вернуться и использовать сценарий несколько раз, чтобы создать различные ресурсы Immersive Reader и настроить их с разрешениями Azure AD.

Скрипт разработан, чтобы быть гибким. Сначала он будет искать существующие ресурсы Immersive Reader и Azure AD в вашей подписке и создавать их по мере необходимости, если они еще не существуют. Если вы впервые создаете ресурс Immersive Reader, сценарий сделает все, что вам нужно. Если вы хотите использовать его только для настройки Azure AD для существующего ресурса Immersive Reader, который был создан на портале, он также сделает это. Он также может быть использован для создания и настройки нескольких ресурсов Immersive Reader.

## <a name="set-up-powershell-environment"></a>Настройка среды PowerShell

1. Начните с открытия [облачной оболочки Azure.](https://docs.microsoft.com/azure/cloud-shell/overview) Убедитесь, что облачная оболочка установлена на PowerShell в `pwsh`верхней левой руке выпадения или набрав .

1. Копируйте и вставьте следующий фрагмент кода в оболочку.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Выполнить `Create-ImmersiveReaderResource`функцию, поставляя параметры по мере необходимости.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Параметр | Комментарии |
    | --- | --- |
    | Параметр SubscriptionName |Название подписки Azure для использования для ресурса Immersive Reader. Для создания ресурса необходимо иметь подписку. |
    | ResourceName |  Должно быть алфавитным, и может содержать '-', до тех пор, как '-' не первый или последний символ. Длина не может превышать 63 символов.|
    | РесурсСубдомен |Пользовательский субдомен необходим для вашего ресурса Immersive Reader. Поддомен используется SDK при вызове службы Immersive Reader для запуска Reader. Поддомен должен быть глобально уникальным. Поддомен должен быть алфавитным и может содержать '-', до тех пор, пока '-' не является первым или последним символом. Длина не может превышать 63 символов. Этот параметр не является обязательным, если ресурс уже существует. |
    | РесурсСКУ |Варианты: `S0`. Посетите нашу [страницу ценообразования когнитивных услуг,](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) чтобы узнать больше о каждом доступном SKU. Этот параметр не является обязательным, если ресурс уже существует. |
    | ResourceLocation |`eastus`Варианты: `eastus2` `southcentralus`, `westus` `westus2`, `australiaeast` `southeastasia`, `centralindia` `japaneast`, `northeurope` `uksouth`, `westeurope`, , , . Этот параметр не является обязательным, если ресурс уже существует. |
    | ResourceGroupName |Ресурсы создаются в ресурсных группах в рамках подписки. Предоставируйте название существующей группы ресурсов. Если группа ресурсов еще не существует, будет создан новый с этим именем. |
    | ResourceGroupLocation |Если группа ресурсов не существует, необходимо предоставить место для создания группы. Чтобы найти список мест, запустите `az account list-locations`. Используйте свойство *имени* (без пробелов) возвращенного результата. Этот параметр не является обязательным, если ваша группа ресурсов уже существует. |
    | AADAppDisplayname |Имя отображения приложения Azure Active Directory. Если существующее приложение Azure AD не найдено, будет создано новое приложение с этим именем. Этот параметр не является обязательным, если приложение Azure AD уже существует. |
    | AADAppИтиверури |URI для приложения Azure AD. Если существующее приложение Azure AD не найдено, будет создано новое приложение с этим URI. Например, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Создаваемый пароль будет позже использован для проверки подлинности при приобретении токена для запуска Immersive Reader. Пароль должен быть не менее 16 символов длиной, содержать по крайней мере 1 специальный символ, и содержат по крайней мере 1 числовой символ. |

1. Копирование вывода JSON в текстовый файл для последующего использования. Полученный результат должен выглядеть примерно так:

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [кратким руководством для разработчиков Node.js](./quickstart-nodejs.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Node.js
* Ознакомьтесь с [руководством для разработчиков Python](./tutorial-python.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Python.
* Посмотреть [учебник Swift,](./tutorial-ios-picture-immersive-reader.md) чтобы узнать, что еще вы можете сделать с Immersive Reader SDK с помощью Swift
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).




