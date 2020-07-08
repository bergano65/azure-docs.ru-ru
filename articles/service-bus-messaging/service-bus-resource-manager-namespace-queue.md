---
title: Создание пространства имен и очереди Служебной шины Azure с помощью шаблона Azure
description: Краткое руководство. Создание пространства имен и очереди служебной шины с помощью шаблона диспетчера ресурсов Azure
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: e382d39e10e1907c9892f03a3da64945f3049fee
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337018"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Краткое руководство. Создание пространства имен и очереди служебной шины с помощью шаблона диспетчера ресурсов Azure

В этой статье показывается, как использовать шаблон Azure Resource Manager, создающий пространство имен служебной шины и очередь в нем. Здесь объясняется, как указать развертываемые ресурсы и определить параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

None

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Создайте пространство имен и очередь служебной шины.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

В шаблоне определены следующие ресурсы:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure.
>
> * [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
> * [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
> * [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
> * [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)

См. [примеры шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Развертывание шаблона

С помощью этого шаблона вы развернете пространство имен служебной шины с очередью.

[Очереди служебной шины](service-bus-queues-topics-subscriptions.md#queues) доставляют сообщения конкурирующим получателям по типу FIFO (в порядке очереди).

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку. Создайте группу ресурсов для развертывания, чтобы потом вы могли легко было выполнить очистку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Проверка развертывания

1. Выберите **Уведомления** вверху, чтобы просмотреть состояние развертывания. Подождите, пока развертывание успешно завершится. Затем в сообщении уведомления выберите **Перейти к группе ресурсов**, чтобы открыть страницу группы ресурсов, содержащую пространство имен Служебной шины. 

    ![Уведомление о развертывании](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Проверьте, отображается ли пространство имен Служебной шины в списке ресурсов. 

    ![Группа ресурсов — пространство имен](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Выберите пространство имен в списке, чтобы открылась страница **Пространство имен Служебной шины**. 

## <a name="cleanup-resources"></a>Очистка ресурсов

1. На портале Azure перейдите на страницу **Группа ресурсов** для своей группы ресурсов.
2. На панели инструментов выберите **Удалить группу ресурсов**. 
3. Введите имя группы ресурсов и щелкните **Удалить**. 

    ![Группа ресурсов — удаление](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Дальнейшие действия

См. следующий раздел, в котором показано, как создать правило авторизации для пространства имен или очереди:

[Создание правила авторизации Служебной шины для пространства имен и очереди с помощью шаблона Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Узнайте, как управлять этими ресурсами, просмотрев следующие статьи:

* [Управление служебной шиной с помощью PowerShell](service-bus-manage-with-ps.md)
* [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
