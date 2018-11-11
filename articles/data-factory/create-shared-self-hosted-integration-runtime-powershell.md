---
title: Создание общей локальной среды выполнения интеграции в Фабрике данных Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как в Фабрике данных Azure создать общую локальную среду выполнения интеграции, обеспечивающую доступ к среде выполнения интеграции другим фабрикам данных.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: d7f3fbcb3235c8c620876e68a62f3e491770779d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252036"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Создание общей локальной среды выполнения интеграции в Фабрике данных Azure с помощью PowerShell

В этом пошаговом руководстве показано, как создать общую локальную среду выполнения интеграции в Фабрике данных Azure с помощью Azure PowerShell. Общую локальную среду выполнения интеграции затем можно использовать в другой фабрике данных. Вот какие шаги выполняются в этом руководстве: 

1. Создали фабрику данных. 
1. Создайте локальную среду выполнения интеграции.
1. Предоставьте общий доступ к локальной среде выполнения интеграции другим фабриками данных.
1. Создайте связанную среду выполнения интеграции.
1. Отмените общий доступ.

## <a name="prerequisites"></a>Предварительные требования 

- **Подписка Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу. 

- **Azure PowerShell**. Выполните инструкции из руководства по [установке Azure PowerShell в Windows](/powershell/azure/install-azurerm-ps). С помощью PowerShell выполните скрипт, чтобы создать локальную среду выполнения интеграции, которую можно использовать совместно с другими фабриками данных. 

> [!NOTE]
> Чтобы получить список регионов Azure, в которых сейчас доступна Фабрика данных, выберите интересующие вас регионы на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Запустите интегрированную среду сценариев Windows PowerShell.

1. Создайте переменные.

    Скопируйте и вставьте следующий скрипт и замените переменные (SubscriptionName, ResourceGroupName и т.д.) соответствующими значениями. 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Войдите в систему и выберите подписку.

    Добавьте следующий код в скрипт, чтобы войти и выбрать подписку Azure.

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Создайте группу ресурсов и Фабрику данных.

    *(Этот шаг не является обязательным. Если у вас уже есть фабрика данных, пропустите этот шаг.)* 

    Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении WestEurope. 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Чтобы создать фабрику данных, выполните следующие команды: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Создание локальной среды выполнения интеграции

*(Этот шаг не является обязательным. Если у вас уже есть локальная среда выполнения интеграции, которую вы хотите использовать совместно с другими фабриками данных, пропустите этот шаг.)*

Выполните следующую команду, чтобы создать локальную среду выполнения интеграции.

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Получение ключа проверки подлинности для среды выполнения интеграции и регистрация узла

Выполните следующую команду, чтобы получить ключ проверки подлинности для локальной среды выполнения интеграции.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Ответ содержит ключ для локальной среды выполнения интеграции. Используйте этот ключ при регистрации узла среды выполнения интеграции.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Установка и регистрация локальной среды выполнения интеграции

1. Скачайте установщик локальной среды выполнения интеграции из [Integration Runtime Фабрики данных Azure](https://aka.ms/dmg).

2. Запустите установщик, чтобы установить локальную среду выполнения интеграции на локальном компьютере.

3. Зарегистрируйте новую локальную среду выполнения интеграции с помощью ключа проверки подлинности, полученного на предыдущем этапе.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Предоставление общего доступа к локальной среде выполнения интеграции другой фабрике данных

### <a name="create-another-data-factory"></a>Создание другой фабрикой данных

*(Этот шаг не является обязательным. Если у вас уже есть фабрика данных, которой вы хотите предоставить общий доступ, пропустите этот шаг.)*

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Предоставление разрешения

Предоставьте разрешение фабрике данных, которой требуется доступ к созданной и зарегистрированной локальной среде выполнения интеграции.

> [!IMPORTANT]
> Не пропускайте этот шаг!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Создание связанной локальной среды выполнения интеграции

Выполните следующую команду, чтобы создать связанную локальную среду выполнения интеграции.

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Теперь можно использовать эту связанную среду выполнения интеграции в любой связанной службе. Связанная среда выполнения интеграции использует общую среду выполнения интеграции для выполнения действий.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Отмена для фабрики данных общего доступа к среде выполнения интеграции

Выполните следующую команду, чтобы отменить для фабрики данных доступ к общей среде выполнения интеграции.

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Выполните следующую команду, чтобы удалить существующую связанную среду выполнения интеграции.

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [среде выполнения интеграции в Фабрике данных Azure](concepts-integration-runtime.md).

- См. дополнительные сведения о [создании и настройке локальной среды выполнения интеграции на портале Azure](create-self-hosted-integration-runtime.md).
