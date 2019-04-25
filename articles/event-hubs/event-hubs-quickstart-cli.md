---
title: Создание концентратора событий с помощью Azure CLI в Центрах событий Azure| Документация Майкрософт
description: В этом кратком руководстве описано создание концентратора событий с помощью Azure CLI, а также последующая отправка и получение событий с использованием Java.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3bc7b409d590ac096b70431ae009fed36c968307
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680015"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Краткое руководство. Создание концентратора событий с помощью Azure CLI

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве вы создадите концентратор событий с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][], прежде чем начать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Выполните команду `az --version`, чтобы узнать номер версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Вход в Azure

Следующие действия не требуются, если вы выполняете команды в Cloud Shell. Если вы используете CLI локально, выполните следующие действия, чтобы войти в учетную запись Azure и выбрать текущую подписку:

Выполните следующую команду, чтобы войти в Azure:

```azurecli-interactive
az login
```

Задайте контекст текущей подписки. Замените `MyAzureSub` идентификатором именем подписки Azure, которую вы хотите использовать:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логическая коллекция ресурсов Azure. Все ресурсы развертываются и управляются в группе ресурсов. Выполните следующую команду, чтобы создать группу ресурсов:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий
Пространство имен Центров событий предоставляет уникальный контейнер, ограничивающий область действия. Вы можете обращаться к этому контейнеру по полному доменному имени и создавать в нем концентраторы событий. Чтобы создать пространство имен в группе ресурсов, выполните следующую команду:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Создание концентратора событий
Выполните следующую команду, чтобы создать концентратор событий:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

Поздравляем! Вы создали пространство имен Центров событий и концентратор событий в этом пространстве имен с помощью Azure CLI. 

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали группу ресурсов, пространство имен Центров событий и концентратор событий. Пошаговые инструкции по отправке событий в концентратор и получении событий из него см. в следующих руководствах по **отправке и получению событий**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [GO](event-hubs-go-get-started-send.md)
- [C (только отправка)](event-hubs-c-getstarted-send.md)
- [Apache Storm (только получение)](event-hubs-storm-getstarted-receive.md)

[создайте бесплатную учетную запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
