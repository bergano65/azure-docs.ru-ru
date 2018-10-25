---
title: Недоставленные сообщения и политики повтора для подписок службы "Сетка событий Azure"
description: Описание настройки опций доставки событий для службы "Сетка событий Azure". Задайте назначение недоставленных сообщений, а также укажите количество попыток доставки.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077788"
---
# <a name="dead-letter-and-retry-policies"></a>Недоставленные сообщения и политики повтора

При создании подписки на события, можно настроить параметры доставки событий. В этой статье показано, как настроить расположение недоставленных сообщений и параметры повторных попыток. Сведения об этих возможностях см. в разделе [Доставка и повторные попытки доставки сообщений сетки событий](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Установка размещения недоставленных сообщений

Чтобы задать расположение недоставленных сообщений, потребуется учетная запись хранения для хранения событий, которые невозможно доставить в конечную точку. Следующий скрипт возвращает идентификатор ресурса для существующей учетной записи хранения и создает подписку на событие, используя контейнер в этой учетной записи хранения для недоставленных сообщений конечной точки.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Для отключения сохранения недоставленных сообщений выполните эту команду повторно, чтобы создать подписку на события, но не указывайте значение для `deadletter-endpoint`. Удалять подписку на события не нужно.

## <a name="set-retry-policy"></a>Установка политики повтора

При создании подписки на сетку событий, можно задать значения для продолжительности попыток доставки события службой "Сетка событий Azure". По умолчанию служба "Сетка событий Azure" осуществляет не более 30 попыток в течение 24 часов (1440 минут). В подписке на сетку событий можно установить любое из этих значений. Значение для срока жизни события должно представлять собой целое число от 1 до 1440. Значение максимального количества попыток доставки должно представлять собой целое число от 1 до 30.

Настроить [интервал повтора](delivery-and-retry.md#retry-schedule-and-duration) невозможно.

Чтобы установить время жизни события на значение, отличное от 1440 минут, используйте следующий код.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Чтобы установить максимальное количество попыток, отличное от 30, используйте следующий код.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Если заданы оба параметра `event-ttl` и `max-deliver-attempts`, служба "Сетка событий Azure" использует первый для истечения повторных попыток.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомиться с примером приложения, использующего приложение-функцию Azure для обработки событий недоставленных сообщений, можно на странице [примеров недоставленных сообщений Сетки событий Azure для .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
