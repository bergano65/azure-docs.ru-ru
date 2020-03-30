---
title: Добавление репозитория артефактов в лабораторию в Лабораториях Azure DevTest Labs (англ.) Документы Майкрософт
description: Узнайте, как добавить репозиторий артефактов в лабораторию в лабораториях Azure DevTest.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: a0dbd92533703a56f1ec2478fab8944656129247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295505"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Добавление репозитория артефактов в лабораторию в DevTest Labs
DevTest Labs позволяет указать артефакт, который будет добавлен в VM во время создания VM или после создания VM. Этот артефакт может быть инструментом или приложением, которое вы хотите установить на VM. Артефакты определяются в файле JSON, загруженном из репозитория GitHub или Azure DevOps Git.

[Публичное хранилище артефактов,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)поддерживаемое DevTest Labs, предоставляет множество общих инструментов как для Windows, так и для Linux. Ссылка на этот репозиторий автоматически добавляется в лабораторию. Вы можете создать свой собственный репозиторий артефактов с помощью определенных инструментов, которые недоступны в общедоступном репозитории артефактов. Чтобы узнать о создании пользовательских артефактов, [см.](devtest-lab-artifact-author.md)

В этой статье приводится информация о том, как добавить пользовательский репозиторий артефактов с помощью портала Azure, шаблонов управления ресурсами Azure и Azure PowerShell. Вы можете автоматизировать добавление репозитория артефактов в лабораторию, написав скрипты PowerShell или CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования
Чтобы добавить репозиторий в лабораторию, сначала нужно получить из него основные сведения. В следующих разделах описывается, как получить необходимую информацию для репозиториев, размещенных на **GitHub** или **Azure DevOps.**

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и персонального маркера доступа из репозитория GitHub

1. Перейдите на домашнюю страницу репозитория GitHub, который содержит определения артефактов или шаблонов Resource Manager.
2. Выберите **Clone or download**(Клонировать или скачать).
3. Нажмите кнопку **HTTPS clone url** (URL-адрес клона HTTPS), чтобы скопировать URL-адрес в буфер обмена. Сохраните URL-адрес для последующего использования.
4. В правом верхнем углу GitHub выберите изображение профиля и щелкните **Параметры**.
5. В меню **«Личные настройки»** слева выберите **настройки разработчика.**
6. Выберите **токены «Личный доступ»** в левом меню.
7. Выберите **Создать новый маркер**.
8. На странице **New personal access token** (Новый личный маркер доступа) заполните поле **Token description** (Описание маркера). Примите элементы по умолчанию в разделе **Выберите области** и щелкните **Создать токен**.
9. Сохраните созданный маркер — он потребуется позже.
10. Закройте GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и персонального маркера доступа из Azure Repos
1. Перейдите на главную страницу коллекции `https://contoso-web-team.visualstudio.com`группы (например), а затем выберите проект.
2. На домашней странице проекта выберите **Код**.
3. Чтобы увидеть URL-адрес клона, на странице **Код** проекта выберите **Клон**.
4. Сохраните URL-адрес — он потребуется позже.
5. Для создания личного маркера доступа выберите в раскрывающемся меню учетной записи пользователя **Мой профиль**.
6. На странице сведений о профиле выберите **Безопасность**.
7. На вкладке **Безопасность** щелкните **Добавить**.
8. На странице **Создать личный маркер доступа**:
   1. Введите **описание** маркера.
   2. В списке **Срок действия истекает через** выберите **180 дней**.
   3. В списке **Учетные записи** выберите **Все доступные учетные записи**.
   4. Выберите **Все области**.
   5. Щелкните **Создать маркер**.
9. Новый маркер появится в списке **Личные маркеры доступа**. Выберите **Скопировать маркер**и сохраните значение маркера, так как оно вам потребуется позднее.
10. Перейдите к разделу Подключение лаборатории к репозиторию.

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе представлены шаги по добавлению репозитория артефактов в лабораторию на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Больше служб**, а затем выберите в списке служб **DevTest Labs**.
3. В списке лабораторий выберите свою лабораторию.
4. Выберите **конфигурацию и политики** в левом меню.
5. Выберите **репозитории** в разделе **Внешние ресурсы** в левом меню.
6. Выберите **и добавьте** на панель инструментов.

    ![Кнопка добавления репозитория](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. На странице **репозиториев** укажите следующую информацию:
   1. **Имя**. Введите имя репозитория.
   2. **URL-адрес клона Git.** Введите URL-адрес клона HTTPS Git, скопированный ранее из GitHub или Azure DevOps Services.
   3. **Филиал**. Укажите ветвь для получения определений.
   4. **Токен индивидуального доступа**. Укажите личный маркер доступа, полученный ранее из GitHub или Azure DevOps Services.
   5. **Путь к папке.** Введите по меньшей мере один путь к папке относительно URL-адреса клона, содержащего определения артефактов или шаблонов Resource Manager. При указании подкаталога обязательно включите косую черту в путь к папке.

        ![Область "Репозитории"](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Нажмите кнопку **Сохранить**.

## <a name="use-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager
Шаблоны Управления ресурсами Azure (Azure Resource Manager) — это файлы JSON, описывающие ресурсы в Azure, которые требуется создать. Для получения дополнительной информации [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md)об этих шаблонах см.

В этом разделе представлены шаги по добавлению репозитория артефактов в лабораторию с помощью шаблона Azure Resource Manager.  Шаблон создает лабораторию, если она еще не существует.

### <a name="template"></a>Шаблон
Шаблон образца, используемый в этой статье, собирает следующую информацию по параметрам. Большинство параметров имеют интеллектуальные значения, но есть несколько значений, которые должны быть указаны. Необходимо указать название лаборатории, URI для репозитория артефактов и маркер безопасности для репозитория.

- Название лаборатории.
- Отобразить имя репозитория артефактов в пользовательском интерфейсе DevTest Labs (UI). Значение по умолчанию: `Team Repository`.
- URI в репозиторий `https://github.com/<myteam>/<nameofrepo>.git` (Пример: или `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Ветвь в репозитории, содержащей артефакты. Значение по умолчанию: `master`.
- Название папки, содержащей артефакты. Значение по умолчанию: `/Artifacts`.
- Тип репозитория. Разрешенные значения `VsoGit` `GitHub`или .
- Токен доступа для репозитория.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Развертывание шаблона
Существует несколько способов развертывания шаблона в Azure и создания ресурса, если он не существует или обновлен, если он существует. Подробности см. в следующих статьях:

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/templates/deploy-rest.md)

Давайте разместим шаблон в PowerShell. Cmdlets, используемые для развертывания шаблона, специфичны для контекста, поэтому используются текущий арендатор и текущая подписка. Используйте [Set-AzContext](/powershell/module/az.accounts/set-azcontext) перед развертыванием шаблона, если это необходимо, чтобы изменить контекст.

Во-первых, создать группу ресурсов с помощью [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Если группа ресурсов, используемая, уже существует, пропустите этот шаг.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Затем создайте развертывание группы ресурсов с помощью [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Этот cmdlet применяет изменения ресурса к Azure. Несколько развертываний ресурсов могут быть сделаны для любой группы ресурсов. Если вы развертываете несколько раз в одну и ту же группу ресурсов, убедитесь, что название каждого развертывания уникально.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

После успешного запуска New-AzResourceGroupDeployment команда выводит важную информацию, например состояние подготовки (должно быть выполнено) и любые выходы для шаблона.

## <a name="use-azure-powershell"></a>Использование Azure PowerShell
В этом разделе предоставляется пример сценария PowerShell, который может быть использован для добавления репозитория артефактов в лабораторию. Если у вас нет Azure PowerShell, см. [Как установить и настроить Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) для подробных инструкций по его установке.

### <a name="full-script"></a>Полный сценарий
Вот полный сценарий, в том числе некоторые многословные сообщения и комментарии:

**New-Dev-DevTestArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Запуск сценария PowerShell
Ниже приводится следующий пример, как запустить сценарий:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Параметры
Пример сценария PowerShell в этой статье использует следующие параметры:

| Параметр | Описание |
| --------- | ----------- |
| LabName | Название лаборатории. |
| ArtifactRepositoryName | Название нового репозитория артефактов. Скрипт создает случайное имя для хранилища, если оно не указано. |
| ArtifactRepositoryDisplayName | Отобразить имя репозитория артефактов. Это имя отображается на портале Azure (приhttps://portal.azure.com) просмотре всех репозиторий артефактов для лаборатории. |
| Репозитори | Ури в хранилище. `https://github.com/<myteam>/<nameofrepo>.git` Примеры: `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`или .|
| RepositoryBranch | Филиал, в котором можно найти файлы артефактов. По умолчанию для 'мастер'. |
| FolderPath | Фондер, под которым можно найти артефакты. По умолчанию к '/Артефакты' |
| PersonalAccessToken | Токен безопасности для доступа к репозиторию GitHub или VSOGit. Ознакомьтесь с разделом предпосылок для инструкций по набору токенов личного доступа |
| Тип источника | Является ли артефакт репозиторием VSOGit или GitHub. |

Самому репозиторию требуется внутреннее имя для идентификации, которое отличается от имени отображения, которое отображается на портале Azure. Внутреннее имя не просматривается с помощью портала Azure, но видите его при использовании APИ Azure REST или Azure PowerShell. Скрипт предоставляет имя, если он не указан пользователем нашего скрипта.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Команды PowerShell, используемые в скрипте

| Команда PowerShell | Примечания |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Эта команда используется для получения сведений о лаборатории, таких как ее местоположение. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Нет конкретной команды для добавления репозиториев артефактов. Общий [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet выполняет свою работу. Для того, чтобы узнать тип ресурса, нужна пара **ResourceId** или **ResourceName** и **ResourceType.** В этом примере скрипт используется имя ресурса и пара типов ресурсов. <br/><br/>Обратите внимание, что вы создаете источник репозитория артефактов в том же месте и под той же группой ресурсов, что и лаборатория.|

Скрипт добавляет новый ресурс к текущей подписке. Для просмотра этой информации используйте [Get-AzContext.](/powershell/module/az.accounts/get-azcontext) Используйте [Set-AzContext](/powershell/module/az.accounts/set-azcontext) для настройки текущего арендатора и подписки.

Лучший способ узнать имя ресурса и информацию о типе ресурса — использовать веб-сайт [Test Drive Azure REST AIS.](https://azure.github.io/projects/apis/) Ознакомьтесь с поставщиком [DevTest Labs - 2016-05-15,](https://aka.ms/dtlrestapis) чтобы увидеть доступные REST AA для поставщика DevTest Labs. Скрипт пользователя следующего идентификатора ресурса.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Тип ресурса — это все, что перечислено после «поставщиков» в URI, за исключением элементов, перечисленных в фигурных скобках. Название ресурса - все, что видно в фигурных скобках. Если для имени ресурса ожидается несколько элементов, разделите каждый элемент с чертой, как мы это сделали.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Дальнейшие действия
- [Укажите обязательные артефакты для лаборатории в Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Создание пользовательских артефактов для виртуальной машины DevTest Labs](devtest-lab-artifact-author.md)
- [Диагностика сбоев артефактов в лаборатории](devtest-lab-troubleshoot-artifact-failure.md)
