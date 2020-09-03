---
title: Создание отдельной базы данных
description: Создание отдельной базы данных в службе "База данных SQL Azure" с помощью портала Azure, PowerShell или Azure CLI. Сведения о запросах к базе данных, отправляемых через редактор запросов на портале Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: 6572f2e71b794f9f147278970b3f5f29fceb29d7
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962695"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Краткое руководство. Создание отдельной базы данных в Базе данных SQL Azure

В рамках этого краткого руководства вы создадите отдельную базу данных в службе "База данных SQL Azure" с помощью портала Azure, скрипта PowerShell или скрипта Azure CLI. Затем вы отправите запросы к этой базе данных через **редактор запросов** на портале Azure.

[Отдельная база данных](single-database-overview.md) — это самый быстрый и простой вариант развертывания в Базе данных SQL Azure. Управление отдельной базой данных выполняется на [сервере](logical-servers.md), который существует в [группе ресурсов Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md) в определенном регионе Azure. В этом кратком руководстве объясняется, как создать группу ресурсов и сервер для новой базы данных.

Отдельную базу данных можно создать на уровне *подготовленных* или *бессерверных* вычислительных ресурсов. Подготовленная база данных имеет определенное количество предварительно выделенных вычислительных ресурсов, таких как объем памяти и ЦП, и использует одну из двух [моделей приобретения](purchasing-models.md). В этом кратком руководстве объясняется, как создать подготовленную базу данных с моделью покупки [на основе виртуальных ядер](service-tiers-vcore.md). Но вы можете выбрать модель [на основе DTU](service-tiers-dtu.md).

Уровень бессерверных вычислений доступен только для модели покупки на основе виртуальных ядер. На этом уровне предоставляется автомасштабируемый набор вычислительных ресурсов, таких как объем памяти и ЦП. Чтобы создать отдельную базу данных на уровне бессерверных вычислений, воспользуйтесь статьей [о создании бессерверной базы данных](serverless-tier-overview.md#create-a-new-database-in-the-serverless-compute-tier).

## <a name="prerequisite"></a>Предварительные требования

- Активная подписка Azure. Если ее нет, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Создание отдельной базы данных

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Выполнение запросов к базе данных

Завершив создание базы данных, вы сможете через встроенный **редактор запросов** на портале Azure подключиться к этой базе данных и выполнить запрос по данным.

1. На портале найдите и выберите элемент **Базы данных SQL**, а затем в открывшемся списке выберите нужную базу данных.
1. На странице **База данных SQL** для этой базы данных найдите и выберите элемент **Редактор запросов (предварительная версия)** в меню слева.
1. Введите учетные данные администратора сервера и щелкните **ОК**.

   ![Вход в редактор запросов](./media/single-database-create-quickstart/query-editor-login.png)

1. Введите следующий запрос в области **Редактор запросов**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Щелкните **Выполнить** и просмотрите результаты запроса в области **Результаты**.

   ![Результаты в редакторе запросов](./media/single-database-create-quickstart/query-editor-results.png)

1. Закройте страницу **редактора запросов** и щелкните **ОК**, когда увидите запрос на отмену несохраненных изменений.

## <a name="clean-up-resources"></a>Очистка ресурсов

Сохраните группу ресурсов, сервер и отдельную базу данных, если вы планируете перейти к следующим шагам и изучить разные методы подключения к базе данных и отправки запросов к ней.

Когда вы закончите работу с этими ресурсами, созданную группу ресурсов можно удалить вместе с размещенными в ней сервером и отдельной базой данных.

### <a name="the-azure-portal"></a>[Портал Azure](#tab/azure-portal)

Чтобы удалить **myResourceGroup** и все ее ресурсы с помощью портала Azure, сделайте следующее:

1. На портале Azure найдите и выберите элемент **Группы ресурсов**, а затем выберите в списке элемент **myResourceGroup**.
1. На странице группы ресурсов выберите **Удалить группу ресурсов**.
1. В поле **Введите имя группы ресурсов** введите *myResourceGroup*, а затем щелкните **Удалить**.

### <a name="the-azure-cli"></a>[CLI Azure.](#tab/azure-cli)

Чтобы удалить группу ресурсов и все ресурсы в ней, выполните следующую команду Azure CLI с именем удаляемой группы ресурсов.

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы удалить группу ресурсов и все ресурсы в ней, выполните следующий командлет PowerShell с именем удаляемой группы ресурсов.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Дальнейшие действия

[Создайте подключение и выполните запрос](connect-query-content-reference-guide.md) к базе данных с помощью разных средств и языков.
> [!div class="nextstepaction"]
> [подключиться и создать запрос с помощью SQL Server Management Studio](connect-query-ssms.md);
>
> [подключиться и создать запрос с помощью Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json).

Хотите оптимизировать и сократить ваши расходы на облако?

> [!div class="nextstepaction"]
> [Начните анализировать затраты с помощью службы "Управление затратами"](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
