---
title: 'Azure Resource Manager: Создание Управляемого экземпляра SQL Azure'
description: Узнайте, как создать Управляемый экземпляр SQL Azure с помощью шаблона Azure Resource Manager.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256078"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>Краткое руководство. Создание Управляемого экземпляра SQL Azure с помощью шаблона Azure Resource Manager

В этом кратком руководстве рассматривается процесс развертывания шаблона Resource Manager для создания Управляемого экземпляра SQL Azure и виртуальной сети.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

Нет.

## <a name="create-an-azure-sql-managed-instance"></a>Создание Управляемого экземпляра SQL Azure

[Управляемый экземпляр SQL Azure](sql-managed-instance-paas-overview.md) — это полностью управляемая и масштабируемая интеллектуальная облачная база данных с почти полным равенством функций с ядром СУБД SQL Server.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

В шаблоне определены следующие ресурсы:

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks);
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups;** ](/azure/templates/microsoft.Network/networkSecurityGroups)



На странице [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular) доступны также другие примеры шаблонов.

## <a name="deploy-the-template"></a>Развертывание шаблона

Выберите **Попробовать** из следующего блока кода PowerShell, чтобы открыть Azure Cloud Shell.

> [!IMPORTANT]
> Развертывание управляемого экземпляра — это длительная операция. Развертывание первого экземпляра в подсети обычно занимает гораздо больше времени, чем развертывание в подсети с существующими управляемыми экземплярами. Среднее время подготовки см. в информации об [операциях управления Управляемыми экземплярами SQL](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Перейдите на [портал Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) и убедитесь, что управляемый экземпляр находится в выбранной группе ресурсов. Поскольку создание управляемого экземпляра может занять некоторое время, возможно, на странице **Обзор** группы ресурсов потребуется перейти по ссылке **Развертывания**.

- См. статью [Краткое руководство. Настройка виртуальной машины Azure для подключения к Управляемому экземпляру SQL](connect-vm-instance-configure.md).
- См. статью [Краткое руководство. Настройка подключения "точка — сеть" к Управляемому экземпляру SQL с локального клиентского компьютера](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите выполнить [дальнейшие действия](#next-steps), сохраните управляемый экземпляр, но после выполнения инструкций во всех дополнительных учебниках удалите его вместе со связанными с ним ресурсами. После удаления управляемого экземпляра выполните инструкции из статьи [Удаление подсети после удаления управляемого экземпляра](virtual-cluster-delete.md).


Чтобы удалить группу ресурсов, сделайте следующее:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Настройка виртуальной машины Azure для подключения к Управляемому экземпляру SQL Azure](connect-vm-instance-configure.md)
