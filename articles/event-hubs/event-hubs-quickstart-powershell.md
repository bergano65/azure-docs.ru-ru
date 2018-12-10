---
title: Краткое руководство по Azure. Создание концентратора событий с помощью PowerShell | Документация Майкрософт
description: В этом кратком руководстве объясняется, как создать концентратор событий с помощью Azure PowerShell и как затем отправлять и получать данные, используя .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 4605ef3876d7a21f99e775b49d7da30ff947fa8e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877931"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Краткое руководство. Создание концентратора событий с помощью Azure PowerShell

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом кратком руководстве вы создадите концентратор событий с помощью Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][], прежде чем начать работу.
- [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](https://www.visualstudio.com/vs) или более новая версия.
- [Пакет SDK для .NET Standard](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы используете PowerShell локально, то для работы с этим кратким руководством необходимо запускать последнюю версию PowerShell. Если вам нужно выполнить установку или обновление, см. руководство по [установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логическая коллекция ресурсов Azure, она необходима для создания концентратора событий. 

В следующем примере создается группа ресурсов в регионе "Восточная часть США". Замените значение `myResourceGroup` именем вашей группы ресурсов:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

После создания группы ресурсов создайте пространство имен Центров событий в этой группе ресурсов. Пространство имен Центров событий предоставляет уникальное полное доменное имя, в котором можно создать концентратор событий. Замените значение `namespace_name` уникальным именем вашего пространства имен:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Создание концентратора событий

Теперь, когда у вас есть пространство имен Центров событий, создайте концентратор событий в этом пространстве имен:  
Допустимый период для `MessageRetentionInDays` — от 1 до 7 дней.

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Поздравляем! Вы создали пространство имен Центров событий и концентратор событий в этом пространстве имен с помощью Azure PowerShell. 

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали пространство имен Центров событий и использовали примеры приложений для отправки событий в созданный концентратор и получения событий из него. Пошаговые инструкции по отправке событий в концентратор и получении событий из него см. в следующих руководствах: 

- **Отправка событий в концентратор**: [Приступая к отправке событий в Центры событий Azure на платформе .NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [Отправка событий в концентраторы событий Azure с помощью платформы .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Отправка событий в Центры событий Azure с помощью Java](event-hubs-java-get-started-send.md), [Отправка событий в Центры событий с помощью Python](event-hubs-python-get-started-send.md), [Отправка событий в Центры событий Azure с помощью Node.js](event-hubs-node-get-started-send.md), [Отправка событий в Центры событий с помощью Go](event-hubs-go-get-started-send.md), [Отправка событий в Центры событий Azure с помощью C](event-hubs-c-getstarted-send.md)
- **Получение событий из концентратора**: [Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [Получение событий от Центров событий Azure с помощью платформы .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Получение событий от Центров событий Azure с помощью Java](event-hubs-java-get-started-receive-eph.md), [Получение событий из концентраторов событий с помощью Python](event-hubs-python-get-started-receive.md), [Получение событий от Центров событий Azure с помощью Node.js](event-hubs-node-get-started-receive.md), [Получение событий из концентраторов событий с помощью Go](event-hubs-go-get-started-receive-eph.md), [Получение событий из Центров событий с помощью Apache Storm](event-hubs-storm-getstarted-receive.md)

[создайте бесплатную учетную запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
