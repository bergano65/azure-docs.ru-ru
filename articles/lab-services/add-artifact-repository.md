---
title: Добавить репозиторий артефактов в лабораторию в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как добавить репозиторий артефактов в лабораторию в Azure DevTest labs.
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
ms.openlocfilehash: 7ff036fbdf3ae9360bed8d728b9bec3a1937b70a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808248"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Добавить репозиторий артефактов в лабораторию в DevTest Labs
DevTest Labs позволяет указать артефакт для добавления к виртуальной Машине во время создания виртуальной Машины или после создания виртуальной Машины. Это средство или приложение, которое вы хотите установить на виртуальной Машине, может быть этого артефакта. Артефакты определены в JSON-файл загружен из репозитория GitHub или Azure DevOps Git. 

[Общедоступным репозиторием артефактов](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), обслуживается DevTest Labs, предоставляет множество распространенных средств для Windows и Linux. Ссылку на этот репозиторий автоматически добавляется в лабораторию. Ваш собственный репозиторий артефактов можно создать с помощью определенных инструментов, которые недоступны в общедоступным репозиторием артефактов. Дополнительные сведения о создании пользовательских артефактов, см. в разделе [Создание пользовательских артефактов](devtest-lab-artifact-author.md).

В этой статье сведения о том, как добавить репозиторий пользовательского артефакта с помощью портала Azure, шаблонов Azure Resource Manager и Azure PowerShell. Вы можете автоматизировать добавление репозитория артефактов в лабораторию путем написания скриптов PowerShell или интерфейса командной строки. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия
Чтобы добавить репозиторий в лабораторию, сначала нужно получить из него основные сведения. В следующих разделах рассматривается для получения необходимых сведений для репозиториев, размещенных на **GitHub** или **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и персонального маркера доступа из репозитория GitHub

1. Перейдите на домашнюю страницу репозитория GitHub, который содержит определения артефактов или шаблонов Resource Manager.
2. Выберите **Clone or download**(Клонировать или скачать).
3. Нажмите кнопку **HTTPS clone url** (URL-адрес клона HTTPS), чтобы скопировать URL-адрес в буфер обмена. Сохраните URL-адрес для последующего использования.
4. В правом верхнем углу GitHub выберите изображение профиля и щелкните **Параметры**.
5. В **личные настройки** меню слева, выберите **параметров разработчика**.
6. Выберите **личные маркеры доступа** в меню слева.
7. Выберите **Создать новый маркер**.
8. На странице **New personal access token** (Новый личный маркер доступа) заполните поле **Token description** (Описание маркера). Примите элементы по умолчанию в разделе **Выберите области** и щелкните **Создать токен**.
9. Сохраните созданный маркер — он потребуется позже.
10. Закройте GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и персонального маркера доступа из Azure Repos
1. Перейдите на домашнюю страницу групповой коллекции (например, https://contoso-web-team.visualstudio.com) ) и выберите свой проект.
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
10. По-прежнему подключение лаборатории к разделу репозитория.

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе описано, как добавить репозиторий артефактов в лабораторию на портале Azure. 

1. Войдите на [портале Azure](https://portal.azure.com).
2. Щелкните **Больше служб**, а затем выберите в списке служб **DevTest Labs**.
3. В списке лабораторий выберите свою лабораторию. 
4. Выберите **конфигурация и политики** в меню слева.
5. Выберите **репозиториев** под **внешние ресурсы** раздел, в меню слева.
6. Выберите **+ добавить** на панели инструментов.

    ![Кнопка добавления репозитория](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. На **репозиториев** укажите следующие сведения:
   1. **Имя**. Введите имя репозитория.
   2. **URL-адрес клона Git.** Введите URL-адрес клона HTTPS Git, скопированный ранее из GitHub или Azure DevOps Services.
   3. **Ветвь.** Укажите ветвь для получения определений.
   4. **Личный маркер доступа.** Укажите личный маркер доступа, полученный ранее из GitHub или Azure DevOps Services.
   5. **Путь к папке.** Введите по меньшей мере один путь к папке относительно URL-адреса клона, содержащего определения артефактов или шаблонов Resource Manager. При указании подкаталога обязательно включите косую черту в путь к папке.

        ![Область "Репозитории"](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Щелкните **Сохранить**.

## <a name="use-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager
Шаблоны Azure Resource Management (Azure Resource Manager) — это файлы JSON, описывающие ресурсы в Azure, вы хотите создать. Дополнительные сведения об этих шаблонах см. в разделе [шаблонов разработки Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

В этом разделе описано, как добавить репозиторий артефактов в лабораторию с помощью шаблона Azure Resource Manager.  Этот шаблон создает лаборатории, если он еще не существует. 

### <a name="template"></a>Шаблон
Пример шаблона, используемом в этой статье собирает следующие сведения через параметры. Большинство параметров у стандартных интеллектуальных значений, но есть несколько значений, которые должны быть указаны. Необходимо указать имя лаборатории, URI для репозитория артефактов, а также маркер безопасности для репозитория. 

- Имя лаборатории.
- Отображаемое имя для репозитория артефактов в DevTest Labs пользовательский интерфейс (UI). Значение по умолчанию —: `Team Repository`.
- URI в репозиторий (пример: `https://github.com/<myteam>/<nameofrepo>.git` или `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Ветвь в репозиторий, который содержит артефакты. Значение по умолчанию —: `master`.
- Имя папки, которая содержит артефакты. Значение по умолчанию —: `/Artifacts`.
- Тип репозитория. Допустимые значения: `VsoGit` или `GitHub`.
- Маркер доступа для репозитория. 

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
Существует несколько способов для развертывания шаблона в Azure и иметь ресурс создан, если он не существует, или обновления, в том случае, если он существует. Дополнительные сведения см. в разделе со следующими статьями:

- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)

Давайте продолжим и указания по развертыванию шаблона в PowerShell. Командлеты, которые используются для развертывания шаблона зависят от контекста, таким образом, используются текущий клиент и текущей подписке. Используйте [AzContext набора](/powershell/module/az.accounts/set-azcontext) перед развертыванием шаблона, при необходимости, чтобы изменить контекст.

Сначала создайте группу ресурсов с помощью [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Если группа ресурсов, которую вы хотите использовать уже существует, пропустите этот шаг.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Затем создайте развертывание в группу ресурсов с помощью [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Этот командлет применяет изменения ресурсов в Azure. Несколько развертываний ресурсов можно применить к любой указанной группы ресурсов. Если вы развертываете несколько раз к одной и той же группе ресурсов, убедитесь, что для каждого развертывания используется уникальное имя.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

После успешного выполнения New-AzResourceGroupDeployment команда выводит важные сведения, такие как состояние подготовки (должно быть выполнено успешно) и выходные данные для шаблона.
 
## <a name="use-azure-powershell"></a>Использование Azure PowerShell 
Этот раздел содержит пример сценария PowerShell, который может использоваться, чтобы добавить репозиторий артефактов в лабораторию. Если у вас нет Azure PowerShell, см. в разделе [описывается установка и настройка Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) подробные инструкции установить его.

### <a name="full-script"></a>Полный сценарий
Ниже приведен полный сценарий, включая некоторые подробные сообщения и комментарии.

**New-DevTestLabArtifactRepository.ps1**:

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

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
В следующем примере показано, как для запуска сценария: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Параметры
Пример сценария PowerShell в этой статье принимает следующие параметры:

| Параметр | Описание | 
| --------- | ----------- | 
| LabName | Имя лабораторной среде. |
| ArtifactRepositoryName | Имя для нового репозитория артефактов. Если он не указан, сценарий создает случайное имя для репозитория. |
| ArtifactRepositoryDisplayName | Отображаемое имя для репозитория артефактов. Это имя, которое отображается на портале Azure (https://portal.azure.com) при просмотре всех репозиториев артефактов в лаборатории. |
| RepositoryUri | URI в репозиторий. Примеры: `https://github.com/<myteam>/<nameofrepo>.git` или `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Ветви, в какие артефакта можно найти файлы. По умолчанию используется «master». | 
| FolderPath | Папка, в котором можно найти артефакты. По умолчанию используется "/ артефактов |
| PersonalAccessToken | Токен безопасности для доступа к репозиторию GitHub или VSOGit. См. в разделе предварительные требования, инструкции получить маркер личного доступа |
| Тип источника | Является ли артефакт репозитория VSOGit или GitHub. |

Сам репозиторий требуется внутренний имя для идентификации, который отличается, отображаемое имя, которое будет отображаться на портале Azure. Вы не видите внутреннее имя, с помощью портала Azure, но видите, при использовании интерфейсов REST API Azure или Azure PowerShell. Сценарий предоставляет имя, если тип не задан пользователем нашего сценария.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Команды PowerShell, используемых в скрипте

| Команды PowerShell | Примечания |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Эта команда используется для получения сведений о лаборатории, такие как его расположение. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Нет определенной команды для добавления репозитории артефактов. Универсальный [New AzResource](/powershell/module/az.resources/new-azresource) командлет выполняет свою задачу. Этот командлет должен либо **ResourceId** или **ResourceName** и **ResourceType** пары знать тип создаваемого ресурса. Этот пример сценария использует имя ресурса и парой тип ресурса. <br/><br/>Обратите внимание на то, что вы создаете источник артефакта репозитория, в том же расположении и в той же группе ресурсов, что и лаборатория.|

Сценарий добавляет новый ресурс в текущей подписке. Используйте [Get-AzContext](/powershell/module/az.accounts/get-azcontext) для просмотра этих сведений. Используйте [AzContext набора](/powershell/module/az.accounts/set-azcontext) для задания текущего клиента и подписки.

Лучший способ обнаружить имя ресурса и сведения о типе ресурсов является использование [теста Azure REST API-интерфейсов диска](https://azure.github.io/projects/apis/) веб-сайта. Ознакомьтесь с [DevTest Labs — 2016-05-15](https://aka.ms/dtlrestapis) поставщика, чтобы просмотреть доступные интерфейсы API REST для DevTest Labs поставщика. Внести в скрипт пользователей следующий идентификатор ресурса. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Тип ресурса — все знаки, указанные после «providers» в URI, за исключением элементов, перечисленных в фигурные скобки. Имя ресурса является все, что в фигурные скобки. Если имя ресурса ожидается более одного элемента, как было показано разделения элементов с косой чертой. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Дальнейшие действия
- [Укажите обязательные артефакты для лаборатории в Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Создание пользовательских артефактов для виртуальной машины DevTest Labs](devtest-lab-artifact-author.md)
- [Диагностика сбоев артефактов в лаборатории](devtest-lab-troubleshoot-artifact-failure.md)

