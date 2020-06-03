---
title: Создание Центра уведомлений Azure с помощью шаблона Azure Resource Manager
description: Узнайте, как создать Центр уведомлений Azure с помощью шаблона Azure Resource Manager.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: aefccb831fe35898962893a173c5bd1125877def
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743533"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>Краткое руководство по созданию Центра уведомлений с помощью шаблона Azure Resource Manager

Центры уведомлений Azure обеспечивают простой в использовании и масштабируемый механизм отправки push-уведомлений, который позволяет отправлять уведомления на любую платформу (iOS, Android, Windows, Kindle и т. д.) c любой серверной части (облачной или локальной). Дополнительные сведения о службе см. в статье [Что такое Центры уведомлений Azure?](notification-hubs-push-notification-overview.md).

В этом кратком руководстве используется шаблон Azure Resource Manager для создания пространства имен Центров уведомлений Azure и центра уведомлений MyHub в этом пространстве имен.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Нет.

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Создание пространства имен Центров уведомлений и концентраторов

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Справочник по шаблонам пространства имен Microsoft.NotificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces).
* [Справочник по шаблонам Microsoft.NotificationHubs namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs).

## <a name="deploy-the-template"></a>Развертывание шаблона

Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Шаблон принимает имя пространства имен Центров уведомлений в качестве параметра. Затем он создает пространство имен с этим именем и Центр уведомлений **MyHub** в этом пространстве имен.

[![Развертывание в Azure](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Вы можете использовать портал Azure для проверки развернутых ресурсов или использовать скрипт Azure CLI или Azure PowerShell, чтобы получить список развернутых пространств имен Центров уведомлений и концентраторов:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужную группу ресурсов. Ресурсы в ней будут также удалены.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по созданию шаблона см. в следующей статье:

> [!div class="nextstepaction"]
> [Руководство. Создание и развертывание первого шаблона Azure Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
