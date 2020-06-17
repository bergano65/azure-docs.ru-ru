---
title: Краткое руководство. Развертывание шаблона Resource Manager для создания виртуальной машины
description: Из этого краткого руководства вы узнаете, как с помощью шаблона Resource Manager создать виртуальную машину Windows.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 6ef929a2934d8480ce6d1eca8bb7ba3b70580110
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552001"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-a-resource-manager-template"></a>Краткое руководство. Создание виртуальной машины Windows с помощью шаблона Resource Manager

В этом кратком руководстве показано, как с помощью шаблона Resource Manager развернуть в Azure виртуальную машину Windows. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Нет.

## <a name="create-a-windows-virtual-machine"></a>Создание виртуальной машины Windows

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json" range="1-225" highlight="67-224":::


В шаблоне определены следующие ресурсы.

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): создание подсети.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): создание учетной записи хранения.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): создание общедоступного IP-адреса.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): создание группы безопасности сети.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): создание виртуальной сети.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): создание сетевой карты.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): создание виртуальной машины.



### <a name="deploy-the-template"></a>Развертывание шаблона

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон. Шаблон создает хранилище ключей и секрет.

    [![Развертывание в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Введите или выберите следующие значения. По возможности используйте значения по умолчанию.

    - **Подписка**. Выберите нужную подписку Azure.
    - **Группа ресурсов**. Выберите группу ресурсов в раскрывающемся списке или нажмите **Создать**, введите уникальное имя группы ресурсов и нажмите кнопку **ОК**.
    - **Расположение**. Выберите расположение.  Например, **центральная часть США**.
    - **Имя пользователя администратора**. Укажите имя пользователя, например *azureuser*.
    - **Пароль администратора**. Укажите пароль, который будет использоваться для учетной записи администратора. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Префикс метки DNS**. Введите уникальный идентификатор, который будет использоваться как префикс метки DNS.
    - **Версия ОС Windows**. Выберите версию Windows, которую требуется запустить на виртуальной машине.
    - **Размер виртуальной машины**. Выберите [размер](sizes.md), который будет использоваться для виртуальной машины.
    - **Расположение**. По умолчанию совпадает с расположением группы ресурсов, если она уже существует.
1. Выберите **Review + create** (Просмотреть и создать). После завершения проверки нажмите **Создать**, чтобы создать и развернуть виртуальную машину.


Для развертывания шаблона используется портал Azure. В дополнение к порталу Azure можно также использовать Azure PowerShell, Azure CLI и REST API. Дополнительные сведения о других методах развертывания см. в статье о [развертывании с использованием шаблонов](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Вы можете использовать портал Azure для проверки виртуальной машины и других созданных ресурсов. После завершения развертывания нажмите **Перейти к группе ресурсов**, чтобы просмотреть виртуальную машину и другие ресурсы.


## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов станет не нужна, удалите ее. При этом будет удалена виртуальная машина и все ресурсы из этой группы ресурсов. 

1. Выберите пункт **Группа ресурсов**.
1. На странице этой группы ресурсов нажмите **Удалить**.
1. При появлении запроса введите имя группы ресурсов, которую необходимо удалить, и нажмите **Удалить**.


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы развернули простую виртуальную машину с помощью шаблона Resource Manager. Дополнительные сведения о виртуальных машинах Azure см. в руководстве для виртуальных машин Linux.


> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)
