---
title: Создание Linux VM в Azure из шаблона
description: Как использовать Azure CLI для создания виртуальной машины Linux с помощью шаблона Resource Manager
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969531"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Как создать виртуальную машину Linux с помощью шаблонов Azure Resource Manager

Узнайте, как создать виртуальную машину Linux (VM) с помощью шаблона Azure Resource Manager и CLI Azure из оболочки Облака Azure. Чтобы создать виртуальную машину Windows, смотрите [Создать виртуальную машину Windows из шаблона управления ресурсами.](../windows/ps-template.md)

## <a name="templates-overview"></a>Обзор шаблонов

Шаблоны Azure Resource Manager — это файлы JSON, которые определяют инфраструктуру и конфигурацию решения Azure. Этот шаблон можно использовать, чтобы повторно развертывать решение на протяжении всего его жизненного цикла и гарантировать, что ресурсы развертываются в согласованном состоянии. Чтобы узнать больше о формате шаблона и о том, как его построить, [см.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) См. дополнительные сведения о синтаксисе JSON при [определении ресурсов в шаблонах Azure Resource Manager](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создание виртуальной машины Azure обычно включает в себя два этапа:

1. Создайте группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины.
1. Создайте виртуальную машину.

Следующий пример создает VM из [шаблона Azure квикстарта.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) Для этого развертывания допускается только проверка подлинности SSH. При запросе укажите значение собственного открытого ключа SSH, например содержимое *~/.ssh/id_rsa.pub*. Если вам необходимо создать пару ключей SSH, см. сведения в статье [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](mac-create-ssh-keys.md). Ниже приведена копия шаблона:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Чтобы запустить скрипт CLI, **выберите его,** чтобы открыть оболочку облака Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

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
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Последняя команда Azure CLI показывает общедоступный IP-адрес недавно созданного VM. Для подключения к виртуальной машине нужен общедоступный IP-адрес. Смотрите следующий раздел этой статьи.

В предыдущем примере указан шаблон, хранящийся в GitHub. Вы также можете скачать или создать шаблон и указать локальный путь с параметром `--template-file`.

Ниже приведены некоторые дополнительные ресурсы.

- Чтобы узнать, как создавать шаблоны Resource Manager, ознакомьтесь с [документацией по Azure Resource Manager](/azure/azure-resource-manager/).
- Чтобы увидеть схемы виртуальной машины [Azure template reference](/azure/templates/microsoft.compute/allversions)Azure, см.
- Чтобы увидеть больше образцов виртуальных [шаблонов](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)машины, см.

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

После этого можно подключиться по SSH к виртуальной машине в обычном режиме. Укажите собственный открытый IP-адрес из предыдущей команды:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Дальнейшие действия

В этом примере вы создали базовую виртуальную машину Linux. Для получения дополнительных шаблонов управления ресурсами, включающие в себя платформы приложений или создающие более сложные среды, просмотрите [шаблоны Azure quickstart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)

Дополнительные сведения о создании шаблонов, синтаксисе и свойствах JSON для типов ресурсов, которые вы развернули, см. в этих статьях:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
