---
title: Создание виртуальной машины Linux в Azure с помощью шаблона | Документация Майкрософт
description: Как использовать Azure CLI для создания виртуальной машины Linux с помощью шаблона Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/14/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ff8beb1995359bad93449744718091c338e4994
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226561"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Как создать виртуальную машину Linux с помощью шаблонов Azure Resource Manager

В этой статье показано, как быстро развернуть виртуальную машину Linux с помощью шаблонов Azure Resource Manager и Azure CLI. 

## <a name="templates-overview"></a>Обзор шаблонов

Шаблоны Azure Resource Manager — это файлы JSON, которые определяют инфраструктуру и конфигурацию решения Azure. Этот шаблон можно использовать, чтобы повторно развертывать решение на протяжении всего его жизненного цикла и гарантировать, что ресурсы развертываются в согласованном состоянии. Дополнительные сведения о формате шаблона и его создании см. в разделе [краткое руководство: по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). См. дополнительные сведения о синтаксисе JSON при [определении ресурсов в шаблонах Azure Resource Manager](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создание виртуальной машины Azure обычно состоит из двух этапов:

1. Создайте группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины.
1. Создайте виртуальную машину.

В следующем примере создается виртуальная машина на любой [шаблон быстрого запуска Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Для этого развертывания, разрешена только проверка подлинности SSH. При запросе укажите значение собственного открытого ключа SSH, например содержимое *~/.ssh/id_rsa.pub*. Если вам необходимо создать пару ключей SSH, см. сведения в статье [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](mac-create-ssh-keys.md). Вот копию шаблона:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Чтобы выполнить сценарий CLI, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Последняя команда Azure CLI выводит общедоступный IP-адрес созданной виртуальной Машине. Требуется общедоступный IP-адрес для подключения к виртуальной машине. См. в следующем разделе этой статьи.

В предыдущем примере указан шаблон, хранящийся в GitHub. Вы также можете скачать или создать шаблон и указать локальный путь с параметром `--template-file`.

Ниже приведены некоторые дополнительные ресурсы.

- Чтобы научиться разрабатывать шаблоны Resource Manager, см. в разделе [документация по Azure Resource Manager](/azure/azure-resource-manager/).
- Схемы виртуальной машины Azure, см. в разделе [Справочник по шаблонам Azure](/azure/templates/microsoft.compute/allversions).
- Дополнительные примеры шаблонов виртуальных машин см. в разделе [шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

После этого можно подключиться по SSH к виртуальной машине в обычном режиме. Укажите собственный открытый IP-адрес из предыдущей команды:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Дальнейшие действия

В этом примере вы создали базовую виртуальную машину Linux. Дополнительные шаблоны Resource Manager, включающие платформы приложений и создания более сложных сред, Обзор [шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Дополнительные сведения о создании шаблонов, синтаксисе и свойствах JSON для типов ресурсов, которые вы развернули, см. в этих статьях:

* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
