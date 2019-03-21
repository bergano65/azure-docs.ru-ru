---
title: Создание общедоступной подсистемы балансировки нагрузки с помощью шаблона Azure
titlesuffix: Azure Load Balancer
description: Узнайте, как создать общедоступную подсистему балансировки нагрузки в диспетчере ресурсов с помощью шаблона
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: af72eb0214f46c1e001f9e93934d38cb934503e4
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592491"
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>Создание общедоступной подсистемы балансировки нагрузки с помощью шаблона

> [!div class="op_single_selector"]
> * [Портал](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Развертывание шаблона с помощью кнопки развертывания

Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Чтобы развернуть этот шаблон, перейдите по [данной ссылке](https://go.microsoft.com/fwlink/?LinkId=544801), нажмите **Deploy to Azure**(Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## <a name="deploy-the-template-by-using-powershell"></a>Развертывание шаблона с помощью PowerShell

Чтобы развернуть шаблон, загруженный с помощью PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview) , чтобы выполнить вход в Azure и выбрать подписку.
2. Запустите **New AzResourceGroupDeployment** командлет, чтобы создать группу ресурсов с помощью шаблона.

    ```powershell
    New-AzResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон с помощью интерфейса командной строки Azure, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

    ```azurecli
    azure config mode arm
    ```

    Вот результат, ожидаемый для указанной выше команды:

        info:    New mode is arm

3. В браузере перейдите к [шаблону быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), скопируйте содержимое JSON-файла и вставьте его в новый файл на своем компьютере. В данном сценарии скопируйте указанные ниже значения в файл **c:\lb\azuredeploy.parameters.json**.
4. Выполните командлет **azure group deployment create** , чтобы развернуть новый балансировщик нагрузки с помощью данного шаблона и файлов параметров, которые вы скачали и изменили ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

Синтаксис JSON и свойства подсистемы балансировки нагрузки в шаблоне см. в справочнике по шаблону для ресурса [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
