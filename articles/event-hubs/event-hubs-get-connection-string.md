---
title: Получение строки подключения к службе "Центры событий Azure" | Документация Майкрософт
description: В этой статье приводятся инструкции по получению строки подключения, с помощью которой клиенты могут установить соединение со службой "Центры событий Azure".
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ee4bd5d2acf1a029486f83ee721b9e1f72347958
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238153"
---
# <a name="get-an-event-hubs-connection-string"></a>Получение строки подключения Центров событий

Чтобы использовать Центры событий, следует создать пространство имен Центров событий. Пространство имен — это определенная область, в которой могут размещаться несколько Центров событий и темы Kafka. Это пространство имен предоставляет уникальное [полное доменное имя (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). После создания пространства имен можно получить строку подключения, необходимую для взаимодействия с Центрами событий.

Строка подключения для Центров событий Azure состоит из следующих компонентов, внедренных в него.

* FQDN = FQDN созданного пространства имен EventHubs (будет включать имя пространства имен EventHubs, за которым следует servicebus.windows.net).
* SharedAccessKeyName = имя, выбранное для ключей SAS вашего приложения.
* SharedAccessKey = сформированное значение ключа.

Строка подключения выглядит следующим образом.
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Пример строки подключения может выглядеть `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`.

Получения строки подключения различными способами описано в этой статье.

## <a name="get-connection-string-from-the-portal"></a>Получение строки подключения на портале

Получив пространство имен Центров событий, раздел "Обзор" портала может предоставить строку подключения, как показано ниже.

![Строки подключения Центров событий](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Если щелкнуть ссылку строки подключения в разделе "Обзор", откроется вкладка политики SAS, как показано на рисунке ниже.

![Политики SAS Центров событий](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Можно добавить новую политику SAS и получить строку подключения или использовать по умолчанию уже созданную политику. Когда политика открыта, получается строка подключения, как показано на рисунке ниже.

![Получение строки подключения для Центров событий](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Получение строки подключения с помощью Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Get-AzEventHubNamespaceKey можно использовать для получения строки подключения для указанного имени политики или правила, как показано ниже.

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Для дополнительной информации см. статью [Get-AzureRmEventHubKey](https://docs.microsoft.com/powershell/module/az.eventhub/get-azeventhubkey).

## <a name="getting-the-connection-string-with-azure-cli"></a>Получение строки подключения с помощью Azure CLI
Получение строки подключения см. ниже.

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Для дополнительной информации см. [az eventhubs](https://docs.microsoft.com/cli/azure/eventhubs).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Центрах событий см. в следующих источниках:

* [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
