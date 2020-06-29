---
title: Создание службы приватного канала в Приватном канале Azure
description: В этом кратком руководстве показано, как создать службу приватного канала с помощью шаблона Azure Resource Manager.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817621"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>Краткое руководство. Создание службы приватного канала с помощью шаблона Azure Resource Manager

В этом кратком руководстве показано, как создать службу приватного канала с помощью шаблона Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Инструкции в этом кратком руководстве можно также выполнить с помощью [портала Azure](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) или [Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisite"></a>Предварительные требования

Вам потребуется учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.

## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"

Этот шаблон создает службу приватного канала.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

В шаблоне определено несколько ресурсов Azure:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks). Для каждой виртуальной машины существует одна виртуальная сеть.
- [**Microsoft.Network/loadBalancers.** ](/azure/templates/microsoft.network/loadBalancers) Подсистема балансировки нагрузки, предоставляющая виртуальные машины с размещенной службой.
- [**Microsoft.Network/networkInterfaces.** ](/azure/templates/microsoft.network/networkinterfaces) Две сетевых интерфейса — по одному для каждой виртуальной машины.
- [**Microsoft.Compute/virtualMachines.** ](/azure/templates/microsoft.compute/virtualmachines) Две виртуальные машины — на одной размещена служба, а другая используется для проверки подключения к частной конечной точке.
- [**Microsoft.Compute/virtualMachines/extensions.** ](/azure/templates/Microsoft.Compute/virtualMachines/extensions) Расширение, устанавливающее веб-сервер.
- [**Microsoft.Network/privateLinkServices.** ](/azure/templates/microsoft.network/privateLinkServices) Служба приватного канала для предоставления службы.
- [**Microsoft.Network/publicIpAddresses.** ](/azure/templates/microsoft.network/publicIpAddresses) Два общедоступных IP-адреса, по одному для каждой виртуальной машины.
- [**Microsoft.Network/privateendpoints.** ](/azure/templates/microsoft.network/privateendpoints) Частная конечная точка для обращения к службе.

### <a name="deploy-the-template"></a>Развертывание шаблона

Ниже показано, как развернуть шаблон Azure Resource Manager в Azure.

1. Выберите элемент **Развертывание в Azure**, чтобы войти в Azure и открыть шаблон. Шаблон создает виртуальную машину, стандартную подсистему балансировки нагрузки, службу приватного канала, частную конечную точку, сеть и виртуальную машину для проверки.

   [![Развертывание в Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Выберите или создайте группу ресурсов.
3. Введите имя и пароль администратора для виртуальной машины.
4. Ознакомьтесь с условиями. Если вы согласны с ними, выберите **Я принимаю указанные выше условия** > **Покупка**.

## <a name="validate-the-deployment"></a>Проверка развертывания

> [!NOTE]
> Шаблон Azure Resource Manager создает уникальное имя для ресурса виртуальной машины myConsumerVm<b>{uniqueid}</b>. Подставьте созданное значение вместо **{uniqueid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Подключитесь к виртуальной машине _myConsumerVm{uniqueid}_ из Интернета, выполнив следующие действия:

1.  В строке поиска на портале введите _myConsumerVm{uniqueid}_ .

2.  Выберите **Подключиться**. Откроется страница **Подключение к виртуальной машине**.

3.  Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (_RDP_).

4.  Откройте загруженный RDP-файл.

    а. При появлении запроса выберите **Подключиться**.

    b. Введите имя пользователя и пароль, которые вы указали при создании виртуальной машины.
    
    > [!NOTE]
    > Возможно, потребуется выбрать элементы **Дополнительные варианты** > **Использовать другую учетную запись**, чтобы указать учетные данные, введенные при создании виртуальной машины.

5.  Щелкните **ОК**.

6.  При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

7.  Когда откроется рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.

### <a name="access-the-http-service-privately-from-the-vm"></a>Частный доступ к службе HTTP с виртуальной машины

Ниже показано, как подключиться к службе HTTP с виртуальной машины с помощью частной конечной точки.

1.  Перейдите на удаленный рабочий стол _myConsumerVm{uniqueid}_ .
2.  Откройте браузер и введите адрес частной конечной точки: http://10.0.0.5/.
3.  Откроется страница IIS по умолчанию.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам уже не нужны ресурсы, созданные вместе со службой приватного канала, удалите группу ресурсов. Это действие удаляет службу приватного канала и все связанные с ней ресурсы.

Чтобы удалить группу ресурсов, вызовите командлет `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о службе [Приватный канал Azure](private-link-overview.md).
