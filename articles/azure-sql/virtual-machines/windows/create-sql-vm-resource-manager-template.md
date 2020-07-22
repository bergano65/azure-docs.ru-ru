---
title: Создание виртуальной машины SQL Server (с помощью шаблона Azure Resource Manager)
description: Сведения о том, как создать SQL Server на виртуальной машине Azure с помощью шаблона Azure Resource Manager.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 8b165f640548f28e5d94e5a791c0fe8545df4d78
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852504"
---
# <a name="create-sql-server-vm-azure-resource-manager-template"></a>Создание виртуальной машины SQL Server (с помощью шаблона Azure Resource Manager)

Используйте этот шаблон Azure Resource Manager для развертывания SQL Server на виртуальной машине Azure. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать шаблон ARM для развертывания виртуальной машины SQL Server, требуется следующее:

- Последняя версия [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и (или) [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7). 
- Предварительно настроенная [группа ресурсов](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) с подготовленной [виртуальной сетью](../../../virtual-network/quick-create-portal.md) и [подсетью](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.


## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json" highlight="169-310":::

В шаблоне определено пять ресурсов Azure: 

- [Microsoft.Network/publicIpAddresses.](/azure/templates/microsoft.network/publicipaddresses) Создает общедоступный IP-адрес. 
- [Microsoft.Network/networkSecurityGroups.](/azure/templates/microsoft.network/networksecuritygroups) Создает группу безопасности сети. 
- [Microsoft.Network/networkInterfaces:](/azure/templates/microsoft.network/networkinterfaces) настраивает сетевой интерфейс. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): создает виртуальную машину в Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): регистрирует виртуальную машину в поставщике ресурсов виртуальной машины SQL. 

Другие шаблоны для SQL Server на виртуальных машинах Azure можно найти в [коллекции шаблонов быстрого запуска](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine).


## <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Шаблон создает виртуальную машину с нужной версией SQL Server и регистрирует ее в поставщике ресурсов виртуальной машины SQL. 

   [![Развертывание в Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Введите или выберите следующие значения.

    * **Подписка**: Выберите подписку Azure.
    * **Группа ресурсов.** Подготовленная группа ресурсов для виртуальной машины SQL Server. 
    * **Регион**. Выберите регион.  Например, **центральная часть США**.
    * **Имя виртуальной машины**. Введите имя виртуальной машины SQL Server. 
    * **Размер виртуальной машины.** Выберите нужный размер виртуальной машины из раскрывающегося списка.
    * **Имя существующей виртуальной сети.** Введите имя подготовленной виртуальной сети для виртуальной машины SQL Server. 
    * **Существующая группа ресурсов виртуальной сети.** Введите имя группы ресурсов, в которой была подготовлена виртуальная сеть. 
    * **Имя существующей подсети.** Имя подготовленной подсети. 
    * **Предложение образа.** Выберите образ SQL Server и Windows Server, соответствующий потребностям вашего бизнеса. 
    * **SKU SQL.** Выберите номер SKU SQL Server, соответствующий потребностям вашего бизнеса. 
    * **Имя администратора**. Имя пользователя администратора виртуальной машины. 
    * **Пароль администратора**. Пароль, используемый для учетной записи администратора виртуальной машины. 
    * **Тип рабочей нагрузки хранилища.**  Тип хранилища для рабочей нагрузки, соответствующий потребностям вашего бизнеса. 
    * **Количество дисков данных для SQL.**  Количество дисков, которые SQL Server использует для файлов данных.  
    * **Путь к данным.**  Путь к файлам данных SQL Server. 
    * **Количество дисков журналов для SQL.**  Количество дисков, которые SQL Server использует для файлов журналов. 
    * **Путь к журналам.**  Путь к файлам журналов SQL Server. 
    * **Расположение.**  Расположение всех ресурсов (оставьте значение по умолчанию для `[resourceGroup().location]`). 

3. Выберите **Review + create** (Просмотреть и создать). После успешного развертывания виртуальной машины SQL Server вы получите уведомление.

Для развертывания шаблона используется портал Azure. Помимо портала Azure, вы также можете использовать Azure PowerShell, Azure CLI и REST API. Дополнительные сведения о других методах развертывания см. в статье о [развертывании с использованием шаблонов](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Для проверки развернутых ресурсов можно использовать Azure CLI. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов вам не нужна, удалите ее с помощью Azure CLI или Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по созданию шаблона см. в следующей статье:

> [!div class="nextstepaction"]
> [Руководство. Создание и развертывание первого шаблона Azure Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Другие способы развертывания виртуальной машины SQL Server см. в следующих статьях: 
- [Портал Azure](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Дополнительные сведения см. в [обзоре SQL Server на виртуальных машинах Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
