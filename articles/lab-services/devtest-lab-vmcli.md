---
title: Создание и управление виртуальными машинами в DevTest Labs с помощью Azure CLI
description: Узнайте, как использовать Azure DevTest Labs для создания виртуальных машин и управления ими с помощью Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167055"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Создание виртуальных машин и управление ими с DevTest Labs с использованием Azure CLI
Этот быстрый запуск поможет вам создать, начать, подключить, обновить и очистить машину разработки в вашей лаборатории. 

Перед началом работы

* Если лаборатория еще не создана, ознакомьтесь с инструкциями в [этой статье](devtest-lab-create-lab.md).

* [Установите Azure CLI](/cli/azure/install-azure-cli). Перед началом выполните команду az login, чтобы создать подключение к Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Создание и проверка виртуальной машины 
Перед выполнением команд, связанных с DevTest Labs, `az account set` установите соответствующий контекст Azure с помощью команды:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Команда для создания виртуальной `az lab vm create`машины: . Требуется группа ресурсов для лаборатории, название лаборатории и название виртуальной машины. Остальные аргументы меняются в зависимости от типа виртуальной машины.

Следующая команда создает изображение на основе Windows с Azure Market Place. Название изображения такое же, как вы могли бы видеть при создании виртуальной машины с помощью портала Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Следующая команда создает виртуальную машину на основе пользовательского изображения, доступного в лаборатории:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Аргумент **типа изображения** изменился из **галереи** в **пользовательский**. Название изображения соответствует тому, что вы видите, если вы должны были создать виртуальную машину на портале Azure.

Следующая команда создает VM из изображения маркетплейса с проверкой подлинности ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Вы также можете создавать виртуальные машины на основе формул, установив параметр **типа изображения** в **формулу.** Если вам нужно выбрать конкретную виртуальную сеть для вашей виртуальной машины, используйте параметры **vnet-name** и **subnet.** Для получения дополнительной информации, см [Az лаборатории vm создать](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Убедитесь, что виртуальная машина доступна.
Используйте `az lab vm show` команду, чтобы убедиться, что VM доступен перед запуском и подключением к нему. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Запуск виртуальной машины и подключение к ней
В следующем примере команда запускает VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Подключитесь к виртуальной машине по протоколу [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) или через [удаленный рабочий стол](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Обновление виртуальной машины
Следующая команда образца применяет артефакты к VM:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Список артефактов, доступных в лаборатории

Чтобы перечислить артефакты, доступные в VM в лаборатории, запустите следующие команды.

**Облако Shell - PowerShell**: обратите внимание\`на использование backtick ( ) до $ в $expand (т.е. "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Облако Shell - Bash**: обратите\\внимание на использование черты ( ) характер перед $ в команде. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Образец вывода: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Остановка и удаление виртуальной машины    
Следующий образец команды останавливает VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Удалите виртуальную машину.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия
Смотрите следующее содержание: [Документация Azure CLI для Лабораторий Azure DevTest.](/cli/azure/lab?view=azure-cli-latest) 
