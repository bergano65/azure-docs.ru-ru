---
title: Создание пула SQL с помощью шаблона Azure Resource Manager
description: Узнайте, как создать пул SQL Azure Synapse Analytics с помощью шаблона Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 3f84cd92a4c4f96831d39cac783df6710c5ea5f4
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513416"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>Краткое руководство. Создание пула SQL Azure Synapse Analytics с помощью шаблона ARM

Этот шаблон Azure Resource Manager (шаблон ARM) создает пул SQL Azure Synapse Analytics с включенным прозрачным шифрованием данных. Название "пул SQL Synapse" относится к функциям хранения корпоративных данных, которые стали общедоступны в Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Если среда соответствует предварительным требованиям и вы знакомы с использованием шаблонов ARM, нажмите кнопку **Развертывание в Azure**. Шаблон откроется на портале Azure.

[![Развертывание в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json" highlight="57-97":::

Шаблон определяет один ресурс:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Этот шаблон создает пул Synapse SQL.
   
   [![Развертывание в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Введите или измените следующие значения:

   * **Подписка**: Выберите подписку Azure.
   * **Группа ресурсов.** Выберите **Создать**, введите уникальное имя группы ресурсов и щелкните **ОК**. Использование новой группы ресурсов упростит очистку ресурсов.
   * **Регион**. Выберите регион.  Например, **центральная часть США**.
   * **Имя сервера SQL Server.** Примите имя по умолчанию или введите имя сервера SQL Server.
   * **Имя входа администратора SQL.** Введите имя пользователя администратора для сервера SQL Server.
   * **Пароль администратора SQL.** Введите пароль администратора для сервера SQL Server.
   * **Имя хранилища данных.** Введите имя пула SQL.
   * **Прозрачное шифрование данных.** Оставьте значение по умолчанию ("Включено"). 
   * **Целевой уровень обслуживания.** Примите значение по умолчанию (DW400c).
   * **Расположение.** Примите расположение группы ресурсов по умолчанию.
   * **Просмотр и создание.** Установите этот флажок.
   * **Создание**. Установите этот флажок.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Вы можете использовать портал Azure, чтобы проверить развернутые ресурсы, либо скрипт Azure CLI или Azure PowerShell, чтобы получить список развернутых ресурсов.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

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

В этом кратком руководстве показано, как создать пул SQL Azure Synapse Analytics с помощью шаблона ARM и проверить развертывание. Дополнительные сведения об Azure Synapse Analytics и Azure Resource Manager см. в статьях ниже.

- См. статью [Сведения об Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Сведения об [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Создание и развертывание первого шаблона ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
