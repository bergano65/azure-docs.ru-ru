---
title: Краткое руководство. Создание кластера и базы данных Azure Data Explorer с помощью PowerShell
description: Сведения о создании кластера и базы данных Azure Data Explorer с помощью PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287385"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Создание кластера и базы данных Azure Data Explorer с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


В этом кратком руководстве описано создание кластера и базы данных Azure Data Explorer с использованием PowerShell.

Вы можете выполнять командлеты и скрипты PowerShell в системе Windows и Linux или в [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) для создания и настройки [Azure Data Explorer](https://docs.microsoft.com/azure/kusto/ ).

[**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). С помощью Azure PowerShell и **Az.Kusto** можно выполнять следующие задачи:

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

## <a name="configure-parameters"></a>Настройка параметров

Следующие действия не требуются, если вы выполняете команды в Azure Cloud Shell. Если вы используете CLI локально, выполните следующие действия, чтобы войти в учетную запись Azure и выбрать текущую подписку:

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Укажите подписку, в которой необходимо создать кластер.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Установите на своем устройстве модуль Az.Kusto:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

1. Создайте кластер, используя приведенную ниже команду:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | ИМЯ | *mykustocluster* | Необходимое имя кластера.|
   | Sku | *D13_v2* | Номер SKU, который будет использоваться для кластера. |
   | ResourceGroupName | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |

    Можно использовать дополнительные необязательные параметры, например емкость кластера и т. д.

2. Чтобы проверить, успешно ли создан кластер, можно выполнить следующую команду:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Если результат содержит параметр `provisioningState` со значением `Succeeded`, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

1. Создайте базу данных, используя приведенную ниже команду:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Имя кластера, в котором необходимо создать базу данных.|
   | ИМЯ | *mykustodatabase* | Имя базы данных.|
   | ResourceGroupName | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | SoftDeletePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться таким образом, чтобы они были доступны для запроса. |
   | HotCachePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться в кэше. |

2. Выполните следующую команду, чтобы просмотреть созданную базу данных:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

* Если вы планируете работать с другими нашими краткими и подробными руководствами, сохраните созданные ресурсы.
* Чтобы очистить ресурсы, удалите кластер. При удалении кластера также удаляются все содержащиеся в нем базы данных. Для удаления кластера используйте следующую команду:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные команды Az.Kusto см. [**здесь**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ).

> [!div class="nextstepaction"]
> [Краткое руководство Прием данных с помощью пакета SDK .NET Standard для Azure Data Explorer (предварительная версия)](net-standard-ingest-data.md)
