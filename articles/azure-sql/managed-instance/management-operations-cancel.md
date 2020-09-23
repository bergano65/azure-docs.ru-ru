---
title: Отмена операций управления
titleSuffix: Azure SQL Managed Instance
description: Узнайте, как отменить операции управления Управляемый экземпляр SQL Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 4ec999cc35e7d18287679c74c6d45a5aa2ecb9e7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997244"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Отмена операций управления Управляемый экземпляр SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Управляемый экземпляр Azure SQL предоставляет возможность отмены некоторых [операций управления](management-operations-overview.md), таких как развертывание нового управляемого экземпляра или обновление свойств экземпляра. 

## <a name="overview"></a>Обзор

 Все операции управления можно классифицировать следующим образом:

- Развертывание экземпляра (создание нового экземпляра).
- Обновление экземпляра (изменение свойств экземпляра, например виртуальных ядер или зарезервированное хранилище).
- Удаление экземпляра.

Вы можете [отслеживать ход выполнения и состояние операций управления](management-operations-monitor.md) и при необходимости отменять некоторые из них. 

В следующей таблице перечислены операции управления, которые можно отменить, а также типичная Общая длительность:

Категория  |Операция  |Отменяемого  |Предполагаемая длительность отмены  |
|---------|---------|---------|---------|
|Развертывание |Создание экземпляра |Да |90% операций завершается за 5 минут. |
|Update |Увеличение/уменьшение масштаба хранилища экземпляров (общего назначения) |Нет |  |
|Update |Увеличение/уменьшение масштаба хранилища экземпляров (критически важный для бизнеса) |Да |90% операций завершается за 5 минут. |
|Update |Вычисление экземпляра (виртуальных ядер) увеличение и уменьшение масштаба (общего назначения) |Да |90% операций завершается за 5 минут. |
|Update |Вычисление экземпляра (виртуальных ядер) увеличение и уменьшение масштаба (критически важный для бизнеса) |Да |90% операций завершается за 5 минут. |
|Update |Изменение уровня служб экземпляра (общего назначения для критически важный для бизнеса и наоборот) |Да |90% операций завершается за 5 минут. |
|Кнопка "Удалить" |Удаление экземпляра |Нет |  |
|Кнопка "Удалить" |Удаление виртуального кластера (в качестве операции, инициированной пользователем) |Нет |  |

## <a name="cancel-management-operation"></a>Отменить операцию управления

# <a name="portal"></a>[Портал](#tab/azure-portal)

Чтобы отменить операции управления с помощью портал Azure, выполните следующие действия.

1. Перейдите к [портал Azure](https://portal.azure.com)
1. Перейдите в колонку **обзор** управляемый экземпляр SQL. 
1. Щелкните поле **уведомления** рядом с текущей операцией, чтобы открыть страницу **текущей операции** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Выберите поле текущая операция, чтобы открыть страницу текущей операции.":::

1. Выберите **отменить операцию** в нижней части страницы. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Нажмите кнопку Отмена, чтобы отменить операцию.":::

1. Подтвердите, что вы хотите отменить операцию. 


Если запрос на отмену выполнен успешно, операция управления отменяется и происходит сбой. Вы получите уведомление о том, что отмена будет выполнена успешно или неудачно.

![Результат операции отмены](./media/management-operations-cancel/canceling-operation-result.png)


Если запрос Cancel завершается ошибкой или кнопка Отмена неактивна, это означает, что операция управления перешла в недоступное состояние и вскоре завершится.  Операция управления продолжит выполнение до ее завершения.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Если у вас еще не установлен Azure PowerShell, см. раздел [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Чтобы отменить операцию управления, необходимо указать имя операции управления. Поэтому сначала используйте команду Get, чтобы получить список операций, а затем отмените определенную операцию.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Подробные сведения о командах см. в разделе [Get-азсклинстанцеоператион](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) и [останавливает-азсклинстанцеоператион](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Если вы еще не установили Azure CLI, обратитесь к разделу [Установка Azure CLI](/cli/azure/install-azure-cli).

Чтобы отменить операцию управления, необходимо указать имя операции управления. Поэтому сначала используйте команду Get, чтобы получить список операций, а затем отмените определенную операцию.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Подробное описание команд см. в разделе [AZ SQL MI Op](https://docs.microsoft.com/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Отмененный запрос на развертывание

При использовании API версии 2020-02-02, как только запрос на создание экземпляра будет принят, экземпляр начнет существовать как ресурс, независимо от хода выполнения процесса развертывания (состояние управляемого экземпляра — **Подготовка**). Если отменить запрос на развертывание экземпляра (создание нового экземпляра), управляемый экземпляр перейдет из состояния **подготовки** в **фаиледтокреате**.

Экземпляры, которые не удалось создать, по-прежнему находятся в виде ресурса и: 

- Не начисляются
- Не подсчитайте ограничения ресурсов (подсеть или квота Виртуальное ядро).
- Не заполняйте зарезервированное имя экземпляра. чтобы развернуть экземпляр с тем же именем, удалите неудачный экземпляр, чтобы освободить имя.


> [!NOTE]
> Чтобы максимально ограничить шум в списке ресурсов или управляемых экземплярах, удалите экземпляры, которые не удалось развернуть или экземпляры с отмененными развертываниями. 


## <a name="next-steps"></a>Дальнейшие действия

- Сведения о создании первого управляемого экземпляра см. в разделе [Краткое руководство](instance-create-quickstart.md).
- Список функций и сравнительных списков см. в разделе [Общие функции SQL](../database/features-comparison.md).
- Дополнительные сведения о конфигурации виртуальной сети см. в разделе [Конфигурация SQL управляемый экземпляр vnet](connectivity-architecture-overview.md).
- Краткое руководство по созданию управляемого экземпляра и восстановлению базы данных из файла резервной копии см. в разделе [Создание управляемого экземпляра](instance-create-quickstart.md).
- Руководство по использованию Azure Database Migration Service для миграции см. в разделе [Миграция SQL управляемый экземпляр с помощью Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
