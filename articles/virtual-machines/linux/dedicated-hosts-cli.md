---
title: Развертывание Linux VMs для выделенных хостов с помощью CLI
description: Развертывание VMs для выделенных узлов с помощью Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127693"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Развертывание VMs для выделенных хостов с помощью Azure CLI
 

Эта статья поможет вам создать [специальный хост](dedicated-hosts.md) Azure для размещения виртуальных машин (VM). 

Убедитесь, что вы установили версию Azure CLI 2.0.70 или позже и восподписаны на учетную запись Azure с помощью. `az login` 


## <a name="limitations"></a>Ограничения

- Виртуальные наборы масштабов машин в настоящее время не поддерживаются на выделенных хостах.
- Размеры и типы оборудования, доступные для выделенных узлов, различаются в зависимости от региона. Чтобы узнать больше, обратитесь к [странице ценообразования](https://aka.ms/ADHPricing) хоста.
 

## <a name="create-resource-group"></a>Создать группу ресурсов 
Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Создайте группу ресурсов с помощью команды az group create. Следующий пример создает группу ресурсов под названием *myDHResourceGroup* в *восточной части США.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Создание группы узлов 

**Группа узлов** — это ресурс, представляющий коллекцию выделенных узлов. Вы создаете группу узлов в регионе и зоне доступности и добавляете к ней хосты. При планировании высокой доступности, Есть дополнительные варианты. Вы можете использовать один или оба из следующих вариантов с выделенными хостами: 
- Пролет через несколько зон доступности. В этом случае необходимо иметь группу хоста в каждой из зон, которые вы хотите использовать.
- Пролетчерез несколько доменов неисправностей, которые отображаются на физических стойках. 
 
В любом случае необходимо учесть количество доменов неисправностей для принимающей группы. Если вы не хотите охватывать домены неисправностей в группе, используйте количество доменов неисправностей 1. 

Вы также можете решить использовать как зоны доступности, так и домены неисправностей. 

В этом примере мы будем использовать [создание группы хоста az vm](/cli/azure/vm/host/group#az-vm-host-group-create) для создания группы узла, используя как зоны доступности, так и домены неисправностей. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Другие примеры

Вы также можете использовать [группу хоста az vm для](/cli/azure/vm/host/group#az-vm-host-group-create) создания группы узлов в зоне доступности 1 (и без доменов неисправностей).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Следующие виды использования [группы узлов az vm создаются](/cli/azure/vm/host/group#az-vm-host-group-create) для создания группы узлов, используя только домены неисправностей (для использования в регионах, где зоны доступности не поддерживаются). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Создание узла 

Теперь создадим специальный хост в группе-хоста. В дополнение к имени хоста, вы должны предоставить SKU для хозяина. Host SKU фиксирует поддерживаемую серию VM, а также аппаратное поколение для выделенного хоста.  

Для получения дополнительной информации о хост-ску и ценах [см.](https://aka.ms/ADHPricing)

Используйте [создание хоста az vm](/cli/azure/vm/host#az-vm-host-create) для создания хоста. Если вы установите счет домена неисправностей для вашей хост-группы, вам будет предложено указать домен неисправности для вашего хоста.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Создание виртуальной машины 
Создайте виртуальную машину в специальном хосте, используя [az vm create.](/cli/azure/vm#az-vm-create) Если при создании группы хоста указана зона доступности, необходимо использовать ту же зону при создании виртуальной машины.

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
> Если вы создаете виртуальную машину на хосте, которая не имеет достаточно ресурсов, виртуальная машина будет создана в состоянии FAILED. 


## <a name="check-the-status-of-the-host"></a>Проверьте состояние хоста

Вы можете проверить состояние работоспособности хоста и сколько виртуальных машин вы все еще можете развернуть в узел с помощью [az vm хост получить-экземпляр-вид.](/cli/azure/vm/host#az-vm-host-get-instance-view)

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
Вы можете экспортировать шаблон, если теперь требуется создать дополнительную среду разработки с теми же параметрами или среду производства, которая его соответствует. Resource Manager использует шаблоны JSON, которые определяют все параметры среды. Можно полностью создавать среды, ссылаясь на этот шаблон JSON. Шаблоны JSON можно создавать вручную или экспортировать существующую среду для создания шаблона JSON для вас. Используйте [экспорт группы az](/cli/azure/group#az-group-export) для экспорта вашей группы ресурсов.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

С помощью этой команды в текущем рабочем каталоге создается файл `myDHResourceGroup.json`. При создании среды на основе этого шаблона запрашиваются все имена ресурсов. Эти имена можно указать в файле шаблона, добавив параметр `--include-parameter-default-value` в команду `az group export`. Измените шаблон JSON, чтобы указать имена ресурсов, или создайте файл parameters.json , задающий их.
 
Чтобы создать среду из шаблона, используйте [создание группы az.](/cli/azure/group/deployment#az-group-deployment-create)

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Очистка 

С вас взимается плата за выделенные хосты, даже если виртуальные машины не развернуты. Вы должны удалить все узлы, которые вы в настоящее время не используете, чтобы сэкономить расходы.  

Удалить хост можно только в том случае, если с его помощью больше нет виртуальных машин. Удалите VMs с помощью [az vm удалить](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

После удаления VMs, вы можете удалить хост с помощью [az vm хост удалить](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
После удаления всех узлов можно удалить группу узлов с помощью [az vm хост-группы.](/cli/azure/vm/host/group#az-vm-host-group-delete)  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Можно также удалить всю группу ресурсов в одной команде. Это позволит удалить все ресурсы, созданные в группе, включая все vMs, хосты и группы узлов.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Дальнейшие действия

- Для получения дополнительной информации смотрите обзор [выделенных хостов.](dedicated-hosts.md)

- Вы также можете создавать выделенные хосты с помощью [портала Azure.](dedicated-hosts-portal.md)

- Существует образец шаблона, найденный [здесь,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)который использует как зоны, так и домены неисправностей для максимальной устойчивости в регионе.