---
title: 'Azure Resource Manager: Создание отдельной базы данных в Базе данных SQL Azure | Документация Майкрософт'
description: Создание отдельной базы данных в Базе данных SQL с помощью шаблона Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 01e14f86b16db6d998d60e74211ae5ad77381461
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372949"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Краткое руководство. Создание отдельной базы данных в Базе данных SQL с помощью шаблона Azure Resource Manager

Создание [отдельной базы данных](sql-database-single-database.md) — это самый быстрый и простой вариант развертывания для создания базы данных в Базе данных SQL Azure. В этом кратком руководстве описано, как создать отдельную базу данных с помощью шаблона Azure Resource Manager. Дополнительные сведения см. в документации по [Azure Resource Manager](/azure/azure-resource-manager/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Создание отдельной базы данных

Отдельная база данных имеет заданный набор ресурсов вычислений, памяти, операций ввода-вывода и хранения и доступна в [двух моделях приобретения](sql-database-purchase-models.md). При создании отдельной базы данных можете также определить [Сервер Базы данных SQL](sql-database-servers.md), чтобы управлять им и разместить его в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) в указанном регионе.

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/). Следующий файл JSON — это шаблон, который используется в этой статье. Другие примеры шаблонов базы данных SQL Azure можно найти [здесь](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database](~/quickstart-templates/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json)]

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-threat-detection-server-policy-optional-db%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Введите или выберите следующие значения.  

    ![Создание базы данных SQL Azure  с помощью шаблона Azure Resource Manager](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Если значение не указано, используйте значение по умолчанию.

    * **Подписка**. Выберите нужную подписку Azure.
    * **Группа ресурсов**. Щелкните **Создать**, введите уникальное имя новой группы ресурсов и нажмите кнопку **ОК**. 
    * **Расположение**. Выберите расположение.  Например, **центральная часть США**.
    * **Пользователь-администратор.** Укажите имя пользователя администратора сервера базы данных SQL.
    * **Пароль администратора.** Введите пароль администратора. 
    * **I agree to the terms and conditions state above** (Я принимаю указанные выше условия). Установите этот флажок.
3. Щелкните **Приобрести**.

## <a name="query-the-database"></a>Выполнение запросов к базе данных

См. дополнительные сведения о том, как [создать запрос к базе данных](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Очистка ресурсов

Сохраните эту группу ресурсов, сервер базы данных и отдельную базу данных, чтобы перейти к [дальнейшим действиям](#next-steps). Далее показано, как установить подключение и выполнить запрос к базе данных с помощью разных методов.

Чтобы удалить группу ресурсов с помощью Azure CLI или Azure PowerShell, выполните следующие действия.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Дополнительная информация

- Создайте правило брандмауэра на уровне сервера, чтобы подключиться к отдельной базе данных с помощью локальных или удаленных средств. Подробные сведения см. в разделе [Создание правила брандмауэра на уровне сервера](sql-database-server-level-firewall-rule.md).
- Создав правило брандмауэра на уровне сервера, вы сможете [устанавливать подключение и выполнять запросы к базе данных](sql-database-connect-query.md) с помощью разных средств и языков.
  - [подключиться и создать запрос с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md);
  - [подключиться и создать запрос с помощью Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json).
- Чтобы создать отдельную базу данных с помощью Azure CLI, ознакомьтесь с [примерами сценариев Azure CLI](sql-database-cli-samples.md).
- Чтобы создать отдельную базу данных с помощью Azure PowerShell, ознакомьтесь с [примерами сценариев Azure PowerShell](sql-database-powershell-samples.md).
