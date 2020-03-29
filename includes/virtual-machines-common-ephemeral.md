---
title: включить файл
description: включить файл
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155485"
---
Эфемерные диски ОС создаются на локальном хранилище виртуальной машины (VM) и не сохраняются в удаленном хранилище Azure. Эфемерные оС диски хорошо работают для апаций рабочих нагрузок, где приложения терпимы к отдельным сбоям VM, но в большей степени зависят от времени развертывания VM или повторного изображения отдельных экземпляров VM. С эфемерным диском ОС, вы получаете более низкую задержку чтения/записи на дискЕ ОС и более быстрый VM reimage. 
 
Ключевыми особенностями эфемерных дисков являются: 
- Идеально подходит для приложений без гражданства.
- Они могут быть использованы как с Marketplace и пользовательских изображений.
- Возможность быстрого сбросить или переизобразить визг и масштабировать набор экземпляров в исходное состояние загрузки.  
- Более низкая задержка, похожая на временный диск. 
- Эфемерные диски ОС являются бесплатными, вы не несете никаких расходов на хранение диска ОС.
- Они доступны во всех регионах Azure. 
- Эфемерный OS Disk поддерживается [общей галереей изображений](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Основные различия между стойкими и эфемерными дисками ОС:

|                             | Стойкий диск ОС                          | Временный диск ОС                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Предельный размер диска ОС      | 2 ТиБ                                                                                        | Размер кэша для размера VM или 2TiB, в зависимости от того, что меньше. Для **размера кэша в GiB**, см. [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), и [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Поддерживаемые размеры VM          | All                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Поддержка типа диска           | Управляемый и неуправляемый диск ОС                                                                | Управляемый только диск ОС                                                               |
| Поддержка регионов              | все регионы.                                                                                  | все регионы.                              |
| Сохраняемость данных            | Данные диска ОС, записанные на диск ОС, хранятся в Хранилище Azure                                  | Данные, записанные на диск ОС, хранятся в локальном хранилище VM и не сохраняются в Хранилище Azure. |
| Стоп-сделка состояние      | ВМ и набор масштабов могут быть стоп-сделки и перезапущены из стоп-сделки состояние | ВМ и набор масштабов не могут быть стоп-сделки                                  |
| Специализированная поддержка диска ОС | Да                                                                                          | нет                                                                                 |
| Перегабарит диска ОС              | Поддерживается во время создания VM и после того, как VM является стоп-сделки                                | Поддерживается только при создании VM                                                  |
| Переизливка размера до нового размера VM   | Данные диска ОС сохраняются                                                                    | Данные на диске ОС удаляются, ОС переprovisioned                                      |

## <a name="size-requirements"></a>Требования к размеру

Вы можете развернуть изображения VM и экземпляров до размера кэша VM. Например, стандартные изображения Windows Server с рынка составляют около 127 GiB, что означает, что вам нужен размер VM, который имеет кэш больше 127 GiB. В этом случае [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) имеет размер кэша 86 GiB, который не является достаточно большим. Размер Standard_DS3_v2 имеет размер кэша 172 GiB, который достаточно велик. В этом случае Standard_DS3_v2 является наименьшим размером в серии DSv2, который можно использовать с помощью этого изображения. Основные изображения Linux в Marketplace и Windows `[smallsize]` Server изображения, которые обозначаются, как правило, около 30 GiB и может использовать большинство доступных размеров VM.

Эфемерные диски также требуют, чтобы размер VM поддерживает премиум-хранилище. Размеры обычно (но не всегда) имеют `s` в названии, как DSv2 и EsV3. Для получения дополнительной [Azure VM sizes](../articles/virtual-machines/linux/sizes.md) информации см.

## <a name="powershell"></a>PowerShell

Чтобы использовать эфемерный диск для развертывания PowerShell VM, используйте [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) в вашей конфигурации VM. Установите `-DiffDiskSetting` `Local` к `-Caching` `ReadOnly`и к .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Для развертывания наборов масштабов используйте cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) в конфигурации. Установите `-DiffDiskSetting` `Local` к `-Caching` `ReadOnly`и к .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Чтобы использовать эфемерный диск для развертывания CLI `--ephemeral-os-disk` VM, установите `--os-disk-caching` параметр в `ReadOnly` [az vm создать](/cli/azure/vm#az-vm-create) `true` и параметр для .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Для наборов масштабов `--ephemeral-os-disk true` используется один и тот же параметр `--os-disk-caching` для `ReadOnly` [создания az-vmss](/cli/azure/vmss#az-vmss-create) и устанавливается параметр.

## <a name="portal"></a>Портал   

На портале Azure можно использовать эфемерные диски при развертывании VM, открыв **расширенный** раздел **вкладки Диски.** Для **использования эфемерного диска ОС** выберите **Да**.

![Скриншот, показывающий кнопку радио для выбора использования эфемерного диска ОС](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Если опция использования эфемерного диска седой, возможно, вы выбрали размер VM, который не имеет размера кэша больше, чем изображение ОС или который не поддерживает премиум-хранилище. Вернитесь на страницу **Основы** и попробуйте выбрать другой размер VM.

Вы также можете создавать масштабные наборы с эфемерными дисками ОС с помощью портала. Просто убедитесь, что вы выбираете размер VM с достаточно большим размером кэша, а затем в **использовании эфемерного диска ОС** выберите **Да.**

![Скриншот, показывающий кнопку радио для выбора эфемерного диска ОС для набора масштаба](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Развертывание шаблона набора масштабирования  
Процесс создания набора масштабов, используюго эфемерный диск `diffDiskSettings` ОС, `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` заключается в добавлении свойства к типу ресурса в шаблоне. Кроме того, политика кэширования `ReadOnly` должна быть установлена для эфемерного диска ОС. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Развертывание шаблона VM 
Вы можете развернуть VM с эфемерным диском ОС с помощью шаблона. Процесс создания VM, используюго эфемерные диски ОС, заключается в добавлении `diffDiskSettings` свойства в тип ресурсов Microsoft.Compute/virtualMachines в шаблоне. Кроме того, политика кэширования `ReadOnly` должна быть установлена для эфемерного диска ОС. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Reimage VM с помощью REST
Вы можете переизобразить экземпляр виртуальной машины с эфемерным диском ОС с помощью REST API, как описано ниже, и через Azure Portal, перейдя к обзору панели VM. Для наборов масштабов повторное изображение уже доступно через Powershell, CLI и портал.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**В: Каков размер локальных дисков ОС?**

О: Мы поддерживаем платформу и пользовательские изображения, вплоть до размера кэша VM, где все прочитанные/записи на диск ОС будут локальными на том же узде, что и Виртуальная машина. 

**В: Можно ли изменять эфемерный диск ОС?**

О: Нет, после того, как будет подготовлен эфемерный диск ОС, диск ОС не может быть уменьшен. 

**В: Могу ли я прикрепить управляемые диски к эфемерному VM?**

О: Да, вы можете прикрепить управляемый диск данных к VM, который использует эфемерный диск ОС. 

**В: Будут ли все размеры VM поддерживаться для эфемерных дисков ОС?**

О: Нет, все размеры Premium Storage VM поддерживаются (DS, ES, FS, GS и M), за исключением B-серий, N-серий и размеров H-серий.  
 
**Вопрос: Можно ли применять эфемерный диск ОС к существующим вм-и маштабным наборам?**

О: Нет, эфемерный диск ОС может быть использован только во время создания VM и масштабирования. 

**Вопрос: Можете ли вы смешивать эфемерные и нормальные диски ОС в наборе масштаба?**

О: Нет, вы не можете иметь сочетание эфемерных и стойких экземпляров диска ОС в одном и том же наборе масштаба. 

**В: Можно ли создать эфемерный диск ОС с помощью Powershell или CLI?**

О: Да, вы можете создавать VMs с эфемерной ОС Диск с помощью REST, шаблоны, PowerShell и CLI.

**В: Какие функции не поддерживаются эфемерным диском ОС?**

О: Эфемерные диски не поддерживают:
- Захват изображений VM
- моментальными снимками дисков. 
- Шифрование дисков Azure 
- Azure Backup
- Azure Site Recovery  
- ОС диск своп 
