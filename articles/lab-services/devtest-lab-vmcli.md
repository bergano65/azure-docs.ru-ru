---
title: Создание виртуальных машин и управление ими в DevTest Labs с помощью Azure CLI | Документация Майкрософт
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795942"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Создание виртуальных машин и управление ими с DevTest Labs с использованием Azure CLI
Это краткое руководство поможет вам создание, запуск, подключение, обновление и очистка на машине разработки в лаборатории. 

Перед началом работы

* Если лаборатория еще не создана, ознакомьтесь с инструкциями в [этой статье](devtest-lab-create-lab.md).

* [Установка Azure CLI](/cli/azure/install-azure-cli). Перед началом выполните команду az login, чтобы создать подключение к Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Создание и проверка виртуальной машины 
Перед выполнением команд связанных DevTest Labs, устанавливается соответствующий контекст Azure с помощью `az account set` команды:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Команда для создания виртуальной машины: `az lab vm create`. Группу ресурсов для лаборатории, имя лабораторной и имя виртуальной машины — обязательные. Остальные аргументы зависят от типа виртуальной машины.

Следующая команда создает образ на базе Windows из рынке Azure. Имя образа совпадает, который будет отображаться при создании виртуальной машины с помощью портала Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Следующая команда создает виртуальную машину на основе пользовательского образа в лаборатории:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**Тип изображения** аргумент было изменено с **коллекции** для **пользовательских**. Имя изображения соответствует, отображаемые при создании виртуальной машины на портале Azure.

Следующая команда создает виртуальную Машину из образа marketplace с помощью ssh проверка подлинности:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Можно также создать виртуальные машины, на основе формул, задав **тип изображения** параметр **формула**. Если вам необходимо выбрать конкретной виртуальной сети для виртуальной машины, используйте **имя виртуальной сети** и **подсети** параметров. Дополнительные сведения см. в разделе [создать az lab vm](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Убедитесь, что виртуальная машина доступна.
Используйте `az lab vm show` команду, чтобы убедиться, что виртуальная машина доступна, перед запуском и подключиться к нему. 

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
Например, следующая команда запускает виртуальную Машину:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Подключение к виртуальной Машине: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) или [удаленного рабочего стола](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Обновление виртуальной машины
В следующем примере команды применяет артефакты к виртуальной Машине:

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

Перечислите артефакты, доступные в лаборатории.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Остановка и удаление виртуальной машины    
В следующем примере команды останавливает виртуальную Машину.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Удалите виртуальную машину.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия
См. в следующих материалах: [Документация по Azure CLI для Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 