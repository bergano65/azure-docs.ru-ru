---
title: Создание мульти-VM-сред и ресурсов PaaS с шаблонами
description: Узнайте, как в Azure DevTest Labs создавать среды со множеством виртуальных машин и ресурсов PaaS, используя шаблон Azure Resource Manager.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169638"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Создание сред со множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager

Среды Azure DevTest Labs позволяют пользователям легко развертывать сложные инфраструктуры на постоянной и стыке лаборатории. Шаблоны [Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) можно использовать для создания сред с наборами ресурсов в DevTest Labs. Эти среды могут содержать любые ресурсы Azure, которые могут создавать шаблоны управления ресурсами.

Вы можете легко [добавить одну виртуальную машину (VM) одновременно в](devtest-lab-add-vm.md) лабораторию с помощью [портала Azure.](https://portal.azure.com) Однако такие сценарии, как многоуровневые веб-приложения или ферма SharePoint, нуждаются в механизме создания нескольких вс-карт за один шаг. Используя шаблоны Azure Resource Manager, можно определить инфраструктуру и конфигурацию решения Azure и повторно развернуть несколько вс-проектов в согласованном состоянии.

Шаблоны Azure Resource Manager также предоставляют следующие преимущества:

- Шаблоны управления ресурсами Azure загружаются непосредственно из репозитория управления исходным элементом GitHub или Azure Repos.
- Пользователи могут создать среду, выбрав настроенный шаблон управления ресурсами Azure с портала Azure, так же, как и с другими типами [баз VM.](devtest-lab-comparing-vm-base-image-types.md)
- Ресурсы Azure PaaS, а также IaaS-вм" можно предоставить в среде из шаблона управления ресурсами Azure.
- Вы можете отслеживать стоимость сред в лаборатории, в дополнение к отдельным VMs, созданным другими типами баз. Ресурсы PaaS создаются и будут отображаться в отслеживании затрат. Однако автоматическое выключение виртуальной машины не применяется к ресурсам PaaS.

Чтобы узнать больше о преимуществах использования шаблонов Resource Manager для развертывания, обновления или удаления многих лабораторных ресурсов в одной операции, [см.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> При использовании шаблона Resource Manager в качестве основы для создания лабораторных вм: мв. Для получения дополнительной информации [см.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Использование общедоступных средах DevTest Labs
В Azure DevTest Labs есть [общедоступное хранилище шаблонов управления ресурсами Azure,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) которые можно использовать для создания сред без подключения к внешнему источнику GitHub самостоятельно. Этот общедоступный репозиторий аналогичен общедоступному репозиторию артефактов, доступному на портале Azure для каждой создаваемых лабораторий. Репозиторий среды позволяет быстро приступить к работе с предварительно авторизированными шаблонами среды, которые имеют несколько параметров ввода. Эти шаблоны обеспечивают вам плавное начало работы для ресурсов PaaS в лабораториях.

В общедоступном репозитории команда DevTest Labs и другие организации создали и поделились часто используемыми шаблонами, такими как Веб-приложения Azure, кластер service Fabric и среда разработки SharePoint Farm. Вы можете использовать эти шаблоны непосредственно, или настроить их в соответствии с вашими потребностями. Дополнительные сведения см. в статье [Настройка и использование общедоступных сред в Azure DevTest Labs](devtest-lab-configure-use-public-environments.md). После создания собственных шаблонов вы можете хранить их в этом репозитории, чтобы поделиться ими с другими пользователями, или настроить свой собственный репозиторий Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Создание собственных репозиториев шаблонов

В качестве одной из лучших практик с инфраструктурой как кодом и конфигурацией как кодом следует управлять шаблонами среды в исходном управлении. Лаборатории Azure DevTest следуют этой практике и загружают все шаблоны менеджера ресурсов Azure непосредственно из репозиториев Репоса GitHub или Azure. В результате можно использовать шаблоны Resource Manager по всему циклу выпуска, от тестовой среды до производственной среды.

Для организации шаблонов управления ресурсами Azure В репозитории необходимо выполнить несколько правил:

- Вы должны назвать файл основного шаблона *azuredeploy.json.*

- Если вы хотите использовать значения параметров, определенные в файле параметра, файл параметра должен быть назван *azuredeploy.parameters.json.*

  Для создания значения URI parametersLink можно использовать параметры `_artifactsLocation` и `_artifactsLocationSasToken`, чтобы служба DevTest Labs автоматически управляла вложенными шаблонами. Для получения дополнительной информации смотрите [шаблоны «Разложить вложенные ресурсы» для тестирования сред.](deploy-nested-template-environments.md)

- Метаданные можно определить, чтобы указать имя и описание отображения шаблона в файле под названием *metadata.json,* следующим образом:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Основные файлы шаблонов Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Добавление репозиториев шаблонов в лабораторию

После создания репозитория можно добавить его в лабораторию с помощью портала Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.
1. Из списка лабораторий выберите лабораторию, которая вам нужна.
1. На панели **обзора** лаборатории выберите **Конфигурацию и политики.**

   ![Configuration and Policies (Конфигурация и политики)](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Из списка параметров **конфигурации и политики** выберите **репозитории.** **Репо-репо «Общественный артефакт»** автоматически генерируется для всех лабораторий и подключается к [публичному репозиторию GitHub DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Чтобы добавить репозиторий шаблона управления ресурсами Azure, выберите **Добавить**.

   ![Общедоступный репозиторий](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. В панели **репозиториев** введите следующую информацию:

   - **Имя**: Введите имя репозитория для использования в лаборатории.
   - **URL-клон аголо:** Введите URL-адрес клона Git HTTPS из GitHub или Azure Repos.
   - **Ветвь** (необязательно): Введите имя ветви для доступа к определениям шаблонов управления ресурсами Azure.
   - **Токен личного доступа:** Введите токен личного доступа, который используется для безопасного доступа к репозиторию.
     - Чтобы получить токен из Azure Repos, под вашим профилем выберите токены > **безопасности персональных** > **книжок безопасности** **пользователя.**
     - Чтобы получить токен от GitHub под вашим профилем, выберите токены **Настройки настройки** > **Developer Settings** > **разработчика.**
   - **Пути Фолдера:** Введите путь папки, который по отношению к URI клона Git, чтобы вы или определения шаблонов шаблонов управления ресурсами Azure.

1. Нажмите кнопку **Сохранить**.

   ![Добавление нового репозитория](./media/devtest-lab-create-environment-from-arm/repo-values.png)

После добавления шаблона Azure Resource Manager в лабораторию пользователи лаборатории могут создавать среды с помощью шаблона.

## <a name="configure-access-rights-for-lab-users"></a>Настройка прав доступа для пользователей лабораторий

Пользователи лаборатории играют роль **Reader** по умолчанию, поэтому они не могут изменять ресурсы в группе ресурсов среды. Например, они не могут остановить или запустить свои ресурсы.

Чтобы дать пользователям лаборатории роль **вкладчика,** чтобы они могли отобразить ресурсы в своих средах, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)на панели **обзора** лаборатории выберите **настройки и политики,** а затем выберите **настройки Лаборатории.**

1. В **настройках настройки лаборатории** выберите **Contributor,** а затем выберите **Сохранить** для предоставления разрешений на запись пользователям лаборатории.

   ![Настройка прав доступа пользователей лаборатории](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Следующий раздел проходит через создание среды из шаблона менеджера ресурсов Azure.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Создание сред из шаблонов на портале Azure

После добавления шаблона Azure Resource Manager в лабораторию пользователи лаборатории могут создавать среды на портале Azure, выполняя следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com).

1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите лабораторию, которая вам нужна.

1. На странице лаборатории выберите **Добавить**.

1. **Выберите базовое** панель отображает базовые изображения, которые можно использовать, с шаблонами менеджера ресурсов Azure, перечисленными в первую очередь. Выберите нужный шаблон менеджера ресурсов Azure.

   ![Выбор основы](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. На панели **Добавить** введите значение **имени среды** для отображения пользователям среды.

   Шаблон менеджера ресурсов Azure определяет остальные поля ввода. Если файл *шаблона azuredeploy.parameter.json* определяет значения по умолчанию, поле ввода показывает эти значения.

   Для параметров *безопасной строки*типа можно использовать секреты из вашего убежища ключей Azure. Чтобы узнать о хранении секретов в хранилище ключей [Store secrets in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md)и использовании их при создании лабораторных ресурсов, см.  

   ![Добавление области](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Следующие значения параметров не отображаются в полях ввода, даже если шаблон определяет их. Вместо этого в форме отображается пустые поля ввода, в которых пользователи лаборатории должны вводить значения при создании среды.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Нажмите кнопку **Добавить**, чтобы создать среду.

   Среда начинает подготовку немедленно, с статусом отображается в списке **Моих виртуальных машин.** Лаборатория автоматически создает новую группу ресурсов для предоставления всех ресурсов, определенных в шаблоне менеджера ресурсов Azure.

1. После создания среды выберите среду в списке **Моих виртуальных машин,** чтобы открыть панель группы ресурсов и просмотреть все ресурсы, подготовленные средой.

   ![Ресурсы окружающей среды](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Можно также расширить среду для просмотра только списка предпосыловных вобий.

   ![Список "My virtual machines" (Мои виртуальные машины)](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Выберите любую среду для просмотра доступных действий, таких как применение артефактов, присоединение дисков данных, изменение времени автоматического отключения и многое другое.

   ![Действия среды](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Автоматизация создания среды с помощью PowerShell

Можно использовать портал Azure для добавления единой среды в лабораторию, но когда сценарий разработки или тестирования должен создать несколько сред, автоматическое развертывание — это лучший опыт.

Прежде чем приступить к работе, убедитесь, что у вас есть шаблон менеджера ресурсов Azure, который определяет ресурсы для создания. [Добавить и настроить шаблон в репозиторий Git](#configure-your-own-template-repositories)и [добавьте репозиторий в лабораторию.](#add-template-repositories-to-the-lab)

Следующий пример сценария создает среду в вашей лаборатории. Комментарии помогут вам лучше понять сценарий.

1. Сохраните следующий образец скрипта PowerShell на жестком диске как *deployenv.ps1.*

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Запустите сценарий следующим образом, используя определенные значения для SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (папка в репо Git) и EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Можно также использовать Azure CLI для развертывания ресурсов с шаблонами управления ресурсами. Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Только пользователь с правами владельца лаборатории может создать виртуальные машины из шаблона Resource Manager с помощью Azure PowerShell. Если вы хотите автоматизировать создание VM с помощью шаблона Resource Manager и у вас есть только разрешения пользователя, вы можете использовать команду CLI [az lab vm.](/cli/azure/lab/vm#az-lab-vm-create)

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Ограничения шаблонов менеджера ресурсов в DevTest Labs

Рассмотрим эти ограничения при использовании шаблонов Resource Manager в DevTest Labs:

- Шаблоны «Менеджер ресурсов» не могут относиться к большинству существующих ресурсов. Они могут создавать только новые ресурсы. Если у вас есть шаблоны менеджера ресурсов, которые используются за пределами DevTest Labs, которые относятся к существующим ресурсам, вы не можете использовать их в DevTest Labs. Единственным исключением является то, что вы можете ссылаться на существующую виртуальную сеть.

- Нельзя создавать формулы или пользовательские изображения из пилотовых новорах лабораторий, созданные из шаблона «Менеджер ресурсов».

- Большинство политик не оцениваются при развертывании шаблонов «Менеджер ресурсов».

  Например, у пользователя может быть лабораторная политика, которую пользователь может создать только пять вс-ми. Тем не менее пользователь может развернуть шаблон Resource Manager, который создает десятки виртуальных машин. Политики, которые не оцениваются, включают:

  - число виртуальных машин на пользователя;

  - число виртуальных машин уровня Premium на пользователя лаборатории;

  - число дисков уровня Premium на пользователя лаборатории.

## <a name="next-steps"></a>Дальнейшие действия
- После создания VM можно подключиться к VM, выбрав **Connect** на панели управления VM.
- Для просмотра и администрирования ресурсов в среде выберите среду в списке **Мои виртуальные машины** в вашей лаборатории.
- Исследуйте [шаблоны менеджера ресурсов Azure из галереи шаблонов Azure quickstart.](https://github.com/Azure/azure-quickstart-templates)
