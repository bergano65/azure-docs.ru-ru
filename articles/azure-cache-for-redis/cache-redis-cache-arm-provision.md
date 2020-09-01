---
title: Развертывание кэша Azure для Redis с помощью шаблона Azure Resource Manager
description: Узнайте, как использовать шаблон Azure Resource Manager для развертывания ресурса кэша Azure для Redis. Для распространенных сценариев предусмотрены шаблоны.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 3c2d13794f2fc3af4541032d1f94967681c0deee
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078424"
---
# <a name="create-an-azure-cache-for-redis-using-a-resource-manager-template"></a>Создание кэша Azure для Redis с помощью шаблона диспетчер ресурсов

Узнайте, как создать шаблон Azure Resource Manager, который развертывает кэш Azure для Redis. Кэш можно использовать с существующей учетной записи хранения для размещения данных диагностики. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. В настоящее время параметры диагностики являются общими для всех кэшей в одном регионе подписки. Обновление одного кэша в регионе влияет на все кэши в нем.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Если среда соответствует предварительным требованиям и вы знакомы с использованием шаблонов ARM, нажмите кнопку **Развертывание в Azure**. Шаблон откроется на портале Azure.

[![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**: Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
* **Учетная запись хранения**. чтобы создать ее, см. статью [Создание учетной записи хранения Azure](/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=template). Учетная запись хранения используется для диагностических данных.

## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

В шаблоне определены следующие ресурсы:

* [Microsoft.Cache/Redis;](/azure/templates/microsoft.cache/redis)
* [Microsoft. Insights/DiagnosticSettings](/azure/templates/diagnosticsettings)

Также доступны шаблоны диспетчер ресурсов для нового [уровня "Премиум](cache-overview.md#service-tiers) ".

* [Создание кэша Azure для Redis уровня "Премиум" с кластеризацией](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Создание кэша Redis для Azure уровня "Премиум" с сохранением данных](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Создание кэша Premium Redis, развернутого в виртуальной сети](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Чтобы узнать о новых шаблонах, обратитесь к статье [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/) и выполните поиск по критерию `Azure Cache for Redis`.

## <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон.

    [![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Введите или выберите следующие значения.

    * **Подписка**. Выберите подписку Azure, используемую для создания общего ресурса данных и других ресурсов.
    * **Группа ресурсов**. Выберите **Создать**, чтобы создать новую группу ресурсов или выберите имеющуюся.
    * **Расположение**. Выберите расположение группы ресурсов. Учетная запись хранения и кэш Redis должны находиться в одном регионе. По умолчанию кэш Redis использует то же расположение, что и группа ресурсов. Поэтому укажите то же расположение, что и у учетной записи хранения.
    * **Имя кэша Redis**: введите имя для кэша Redis.
    * **Существующая учетная запись хранения диагностики**: введите идентификатор ресурса для учетной записи хранения. Синтаксис: **/Subscriptions/ &lt; SUBSCRIPTION ID>/RESOURCEGROUPS/ &lt; имя группы ресурсов>/провидерс/Микрософт.стораже/сторажеаккаунтс/ &lt; имя учетной записи хранения>**.

    Для остальных параметров используйте значение по умолчанию.
1. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

1. Войдите на [портал Azure](https://portal.azure.com).
1. Откройте созданный кэш Redis.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужную группу ресурсов. Ресурсы в ней будут также удалены.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнаете, как создать шаблон Azure Resource Manager, который развертывает кэш Azure для Redis. Чтобы узнать, как создать шаблон Azure Resource Manager, который развертывает веб-приложение Azure с кэшем Azure для Redis, см. статью [Создание веб-приложения и кэша Azure для Redis с помощью шаблона](./cache-web-app-arm-with-redis-cache-provision.md).