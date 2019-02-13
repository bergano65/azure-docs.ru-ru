---
title: Недоставленные сообщения и политики повтора для подписок службы "Сетка событий Azure"
description: Описание настройки опций доставки событий для службы "Сетка событий Azure". Задайте назначение недоставленных сообщений, а также укажите количество попыток доставки.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: a15797e9b181aa877b6dfa3350e69b210af5885e
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731773"
---
# <a name="dead-letter-and-retry-policies"></a>Недоставленные сообщения и политики повтора

При создании подписки на события, можно настроить параметры доставки событий. В этой статье показано, как настроить расположение недоставленных сообщений и параметры повторных попыток. Сведения об этих возможностях см. в разделе [Доставка и повторные попытки доставки сообщений сетки событий](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Установка размещения недоставленных сообщений

Чтобы задать расположение недоставленных сообщений, потребуется учетная запись хранения для хранения событий, которые невозможно доставить в конечную точку. В этом примере возвращается идентификатор ресурса существующей учетной записи хранения. Также создается подписка на событие, которая использует контейнер в этой учетной записи хранения для недоставленных сообщений конечной точки.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

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

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
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

### <a name="azure-cli"></a>Инфраструктура CLI Azure

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
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Чтобы установить максимальное количество попыток, отличающееся от 30, используйте следующий код:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Если заданы оба параметра `EventTtl` и `MaxDeliveryAttempt`, служба "Сетка событий Azure" использует первый параметр для указания срока действия и определения того, когда прекращать доставку событий.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомиться с примером приложения, использующего приложение-функцию Azure для обработки событий недоставленных сообщений, можно на странице [примеров недоставленных сообщений Сетки событий Azure для .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
