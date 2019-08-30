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
ms.openlocfilehash: 11ac4e10cbd116ed204a8a11274408f5a5a9b4d9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183134"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Создание виртуальных машин и управление ими с DevTest Labs с использованием Azure CLI
Это краткое руководство поможет вам создать, запустить, подключить, обновить и очистить компьютер разработки в лаборатории. 

Перед началом работы

* Если лаборатория еще не создана, ознакомьтесь с инструкциями в [этой статье](devtest-lab-create-lab.md).

* [Установка Azure CLI](/cli/azure/install-azure-cli). Перед началом выполните команду az login, чтобы создать подключение к Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Создание и проверка виртуальной машины 
Перед выполнением команд, связанных с DevTest Labs, настройте соответствующий контекст Azure с помощью `az account set` команды:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Команда для создания виртуальной машины: `az lab vm create`. Группа ресурсов для лаборатории, имени лаборатории и имени виртуальной машины является обязательной. Остальные аргументы изменяются в зависимости от типа виртуальной машины.

Следующая команда создает образ на основе Windows из рынка Azure. Имя образа совпадает с именем, которое можно увидеть при создании виртуальной машины с помощью портал Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Следующая команда создает виртуальную машину на основе пользовательского образа, доступного в лаборатории.

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Аргумент **типа Image** изменился с **коллекции** на **Пользовательский**. Имя образа соответствует тому, что вы видите при создании виртуальной машины в портал Azure.

Следующая команда создает виртуальную машину из образа Marketplace с аутентификацией SSH.

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Вы также можете создавать виртуальные машины на основе формул, присвоив параметру **Image-Type** значение **Формула**. Если необходимо выбрать определенную виртуальную сеть для виртуальной машины, используйте параметры **vnet-Name** и **Subnet** . Дополнительные сведения см. в статье [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Убедитесь, что виртуальная машина доступна.
`az lab vm show` Используйте команду, чтобы убедиться, что виртуальная машина доступна перед запуском и подключением к ней. 

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
В следующем примере команда запускает виртуальную машину:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Подключение к виртуальной машине: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) или [Удаленный рабочий стол](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Обновление виртуальной машины
Следующий пример команды применяет артефакты к виртуальной машине:

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
Следующий пример команды останавливает виртуальную машину.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Удалите виртуальную машину.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Следующие шаги
См. следующее содержимое: [Azure CLI документация для Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
