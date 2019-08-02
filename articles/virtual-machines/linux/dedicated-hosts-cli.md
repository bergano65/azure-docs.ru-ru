---
title: Развертывание выделенных узлов Azure с помощью интерфейса командной строки | Документация Майкрософт
description: Развертывание виртуальных машин на выделенных узлах с помощью Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/29/2019
ms.author: cynthn
ms.openlocfilehash: 7eda675ed7694e1ad7de90f89282bd7a3cc50ea1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700422"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Предварительный просмотр: Развертывание виртуальных машин на выделенных узлах с помощью Azure CLI
 

В этой статье рассказывается, как создать выделенный [узел](dedicated-hosts.md) Azure для размещения виртуальных машин. 

Убедитесь, что установлен Azure CLI версии 2.0.70 или более поздней, и войдите в учетную запись Azure с `az login`помощью. 

> [!IMPORTANT]
> Выделенные узлы Azure в настоящее время находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Известные ограничения предварительной версии**
> - Масштабируемые наборы виртуальных машин в настоящее время не поддерживаются на выделенных узлах.
> - Начальный выпуск предварительной версии поддерживает следующую серию виртуальных машин: DSv3 и ESv3. 
 

## <a name="create-resource-group"></a>Создать группу ресурсов 
Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов с помощью команды AZ Group Create. В следующем примере создается группа ресурсов с именем *мидхресаурцеграуп* в расположении *Восточная часть США* .

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Создание группы узлов 

**Группа узлов** — это ресурс, представляющий коллекцию выделенных узлов. Вы создаете группу узлов в регионе и зону доступности и добавляете к ней узлы. При планировании высокого уровня доступности доступны дополнительные параметры. Для выделенных узлов можно использовать один или оба следующих варианта. 
- Охват нескольких зон доступности. В этом случае требуется группа узлов в каждой из зон, которые вы хотите использовать.
- Охватывать несколько доменов сбоя, сопоставленных с физическими стойками. 
 
В любом случае необходимо указать количество доменов сбоя для группы узлов. Если вы не хотите охватывать домены сбоя в группе, используйте число доменов сбоя, равное 1. 

Также можно выбрать использование зон доступности и доменов сбоя. 

В этом примере мы будем использовать команду [AZ VM Group Create](/cli/azure/vm#az-vm-host-group-create) для создания группы узлов с помощью зон доступности и доменов сбоя. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Другие примеры

Чтобы создать группу узлов в зоне доступности 1 (без доменов сбоя), можно также использовать команду [AZ VM Group Create](/cli/azure/vm#az-vm-host-group-create) .

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
В следующем примере демонстрируется [Создание](/cli/azure/vm#az-vm-host-group-create) группы узлов с использованием только доменов сбоя (для использования в регионах, где зоны доступности не поддерживаются). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Создание узла 

Теперь создадим выделенный узел в группе узлов. Помимо имени узла, необходимо указать номер SKU для узла. Номер SKU узла фиксирует поддерживаемую серию виртуальных машин, а также создание оборудования для выделенного узла.  В рамках предварительной версии будут поддерживаться следующие значения номера SKU узла: DSv3_Type1 и ESv3_Type1.


Дополнительные сведения о номерах SKU узла и ценах см. на странице [цен на выделенный узел Azure](https://aka.ms/ADHPricing).

Чтобы создать узел, выполните команду [AZ VM Host Create](/cli/azure/vm#az-vm-host-create) . Если для группы узлов задано число доменов сбоя, вам будет предложено указать домен сбоя для узла.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Создать виртуальную машину 
Создайте виртуальную машину в выделенном узле с помощью команды [AZ VM Create](/cli/azure/vm#az-vm-create). Если при создании группы узлов была указана зона доступности, то при создании виртуальной машины необходимо будет использовать ту же зону.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> При создании виртуальной машины на узле, на котором недостаточно ресурсов, виртуальная машина будет создана в состоянии сбоя. 


## <a name="check-the-status-of-the-host"></a>Проверка состояния узла

Вы можете проверить состояние работоспособности узла и количество виртуальных машин, которые можно развернуть на узле, с помощью команды [AZ VM узел Get-instance-View](/cli/azure/vm#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Выходные данные будут выглядеть следующим образом.
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Экспорт в качестве шаблона 
Шаблон можно экспортировать, если теперь нужно создать дополнительную среду разработки с теми же параметрами или рабочую среду, которая соответствует ей. Resource Manager использует шаблоны JSON, которые определяют все параметры среды. Можно полностью создавать среды, ссылаясь на этот шаблон JSON. Вы можете создавать шаблоны JSON вручную или экспортировать существующую среду, чтобы создать шаблон JSON. Чтобы экспортировать группу ресурсов, используйте команду [AZ Group Export](/cli/azure/group#az-group-export) .

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

С помощью этой команды в текущем рабочем каталоге создается файл `myDHResourceGroup.json`. При создании среды на основе этого шаблона запрашиваются все имена ресурсов. Эти имена можно указать в файле шаблона, добавив параметр `--include-parameter-default-value` в команду `az group export`. Измените шаблон JSON, чтобы указать имена ресурсов, или создайте файл Parameters. JSON, в котором указываются имена ресурсов.
 
Чтобы создать среду из шаблона, используйте команду [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Очистка 

Вы платите за выделенные узлы, даже если виртуальные машины не развернуты. Следует удалить все узлы, которые сейчас не используются для экономии затрат.  

Узел можно удалить только в том случае, если он не использует больше виртуальных машин. Удалите виртуальные машины с помощью команды [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

После удаления виртуальных машин можно удалить узел с помощью команды [AZ VM узел Delete](/cli/azure/vm#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
После удаления всех узлов вы можете удалить группу узлов с помощью команды [AZ VM Ungroup Delete](/cli/azure/vm#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Вы также можете удалить всю группу ресурсов в одной команде. Это приведет к удалению всех ресурсов, созданных в группе, включая все виртуальные машины, узлы и группы узлов.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения см. в разделе Общие сведения о [выделенных узлах](dedicated-hosts.md) .

- Вы также можете создавать выделенные узлы с помощью [портал Azure](dedicated-hosts-portal.md).

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)приведен пример шаблона, который использует зоны и домены сбоя для максимальной устойчивости в регионе.