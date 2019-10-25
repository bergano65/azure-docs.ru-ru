---
title: Политики недоставленных сообщений и повторных попыток в службе "Сетка событий Azure"
description: Описание настройки опций доставки событий для службы "Сетка событий Azure". Задайте назначение недоставленных сообщений, а также укажите количество попыток доставки.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794038"
---
# <a name="dead-letter-and-retry-policies"></a>Недоставленные сообщения и политики повтора

При создании подписки на события, можно настроить параметры доставки событий. В этой статье показано, как настроить расположение недоставленных сообщений и параметры повторных попыток. Сведения об этих возможностях см. в разделе [Доставка и повторные попытки доставки сообщений сетки событий](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Установка размещения недоставленных сообщений

Чтобы задать расположение недоставленных сообщений, потребуется учетная запись хранения для хранения событий, которые невозможно доставить в конечную точку. В этом примере возвращается идентификатор ресурса существующей учетной записи хранения. Также создается подписка на событие, которая использует контейнер в этой учетной записи хранения для недоставленных сообщений конечной точки.

> [!NOTE]
> - Перед выполнением команд в этой статье создайте учетную запись хранения и контейнер больших двоичных объектов в хранилище.
> - Служба "Сетка событий" создает большие двоичные объекты в этом контейнере. Имена больших двоичных объектов будут иметь имя подписки на сетку событий со всеми буквами в верхнем регистре. Например, если имя подписки — My-BLOB-, то имена больших двоичных объектов недоставленных данных будут иметь мою подписку MY-BLOB (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111 -111111111111. JSON). Это поведение позволяет защититься от различий в обработке обращений между службами Azure.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Для отключения сохранения недоставленных сообщений выполните эту команду повторно, чтобы создать подписку на события, но не указывайте значение для `deadletter-endpoint`. Удалять подписку на события не нужно.

> [!NOTE]
> Если вы используете Azure CLI на локальном компьютере, используйте Azure CLI 2.0.56 или более поздней версии. Инструкции по установке последней версии Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Для отключения сохранения недоставленных сообщений выполните эту команду повторно, чтобы создать подписку на события, но не указывайте значение для `DeadLetterEndpoint`. Удалять подписку на события не нужно.

> [!NOTE]
> Если вы используете Azure PowerShell на локальном компьютере, используйте Azure Poweshell 1.1.0 или более поздней версии. Скачайте и установите последнюю версию Azure PowerShell из [загрузок Azure](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Установка политики повтора

При создании подписки на сетку событий, можно задать значения для продолжительности попыток доставки события службой "Сетка событий Azure". По умолчанию служба "Сетка событий Azure" осуществляет не более 30 попыток в течение 24 часов (1440 минут). В подписке на сетку событий можно установить любое из этих значений. Значение для срока жизни события должно представлять собой целое число от 1 до 1440. Максимальное значение для попыток должно представлять собой целое число от 1 до 30.

Настроить [интервал повтора](delivery-and-retry.md#retry-schedule-and-duration) невозможно.

### <a name="azure-cli"></a>Azure CLI

Чтобы установить время жизни события на значение, отличное от 1440 минут, используйте следующий код.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Чтобы установить максимальное количество попыток, отличающееся от 30, используйте следующий код:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Если заданы оба параметра `event-ttl` и `max-deliver-attempts`, служба "Сетка событий Azure" использует первый параметр для указания срока действия и определения того, когда прекращать доставку событий.

### <a name="powershell"></a>PowerShell

Чтобы установить время жизни события на значение, отличное от 1440 минут, используйте следующий код.

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Чтобы установить максимальное количество попыток, отличающееся от 30, используйте следующий код:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Если заданы оба параметра `EventTtl` и `MaxDeliveryAttempt`, служба "Сетка событий Azure" использует первый параметр для указания срока действия и определения того, когда прекращать доставку событий.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомиться с примером приложения, использующего приложение-функцию Azure для обработки событий недоставленных сообщений, можно на странице [примеров недоставленных сообщений Сетки событий Azure для .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о сетке событий см. в статье [Сведения о сетке событий](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
