---
title: Размеры виртуальных машин Windows в Azure, оптимизированных для рабочих нагрузок HPC | Документация Майкрософт
description: Список различных размеров виртуальных машин Windows в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: 62c6bb906d9c9935be2da148f24d5285cbf0ed67
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710340"
---
# <a name="high-performance-compute-vm-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Операционная система** -Windows Server 2016 все выше HPC ряда виртуальных машин. Windows Server 2012 R2, Windows Server 2012 также поддерживаются на виртуальных машинах включена не SR-IOV (следовательно, исключая HB и гибридного подключения).

* **MPI** -SR-IOV включена размеры виртуальных Машин в Azure (HB, HC) позволяют почти любой разновидности MPI для использования с Mellanox OFED.
На виртуальные машины не SR-IOV поддерживаемые реализации MPI используют интерфейс Microsoft сети прямой (ND) для взаимодействия между экземплярами. Таким образом только Microsoft MPI (MS-MPI) 2012 R2 или более поздней версии и поддерживаются версии Intel MPI 5.x. Более поздние версии (2017, 2018) среды выполнения Intel MPI библиотеки могут или не могут быть совместимы с драйверами Azure RDMA.

* **Расширение виртуальной Машины InfiniBandDriverWindows** — на виртуальных машинах с поддержкой RDMA добавить расширение InfiniBandDriverWindows, обеспечивающее InfiniBand. Это расширение виртуальной Машины Windows устанавливает драйверы Windows Network Direct (на виртуальных машинах не SR-IOV) или драйверы Mellanox OFED (на виртуальных машинах SR-IOV) для подключения RDMA.
В некоторых развертываниях экземпляров A8 и A9 расширение HpcVmDrivers добавляется автоматически. Обратите внимание на то, что расширение виртуальных Машин HpcVmDrivers рекомендуется; он не обновляется. Чтобы добавить в виртуальную машину расширение виртуальной машины, можно использовать командлеты [Azure PowerShell](/powershell/azure/overview). 

  Следующая команда устанавливает модуль InfiniBandDriverWindows последней версии 1.0 на существующей виртуальной Машины с поддержкой RDMA с именем *myVM* развернута в группе ресурсов с именем *myResourceGroup* в  *Западная часть США* региона:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Кроме того расширения виртуальных Машин могут быть включены в шаблоны Azure Resource Manager для более удобного развертывания, с помощью следующего элемента JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Следующая команда устанавливает модуль InfiniBandDriverWindows последней версии 1.0 на всех виртуальных машинах с поддержкой RDMA, в существующий масштабируемый набор с именем *myVMSS* развернута в группе ресурсов с именем *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Дополнительные сведения см. в статье [Обзор расширений и компонентов виртуальной машины под управлением Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Вы также можете работать с расширениями для виртуальных машин, развернутых в рамках [классической модели развертывания](classic/manage-extensions.md).

* **Адресное пространство сети RDMA.** Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. Чтобы выполнять приложения MPI в экземплярах, развернутых в виртуальной сети Azure, убедитесь, что адресное пространство виртуальной сети не пересекается с сетью RDMA.


### <a name="cluster-configuration-options"></a>Параметры конфигурации кластера

Azure предоставляет несколько вариантов для создания кластеров виртуальных машин для Windows HPC, которые могут взаимодействовать с помощью сети RDMA. 

* **Виртуальные машины**. Разверните виртуальные машины HPC с поддержкой RDMA в одну группу доступности (используя модель развертывания Azure Resource Manager). Если вы используете классическую модель развертывания, разверните виртуальные машины в одну облачную службу. 

* **Масштабируемые наборы виртуальных машин** — в масштабируемый набор виртуальных машин задано, убедитесь, что вы ограниченного развертывания одной группы размещения. Например, в шаблоне Resource Manager задайте значение `true` для свойства `singlePlacementGroup`. 

* **MPI между виртуальными машинами** — Если связи MPI при необходимости между виртуальными машинами (ВМ), убедитесь, что виртуальные машины находятся в одной группе доступности или виртуальной машины, так же масштабируемого набора.

* **Azure CycleCloud**. Создайте кластер HPC в [Azure CycleCloud](/azure/cyclecloud/), чтобы запустить задания MPI на узлах Windows.

* **Пакетная служба Azure**. Создайте пул [пакетной службы Azure](/azure/batch/) для выполнения рабочих нагрузок MPI на вычислительных узлах Windows Server. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](../../batch/batch-pool-compute-intensive-sizes.md). Чтобы запустить рабочие нагрузки на основе контейнера в пакетной службе, ознакомьтесь с проектом [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Пакет Microsoft HPC** - [Пакет HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) включает среду выполнения для MS-MPI, которая использует сеть Azure RDMA при развертывании на виртуальных машинах Windows с поддержкой RDMA. Примеры развертываний см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](../virtual-machines-windows-sizes-memory.md)
- [Оптимизированные для хранилища](../virtual-machines-windows-sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Следующие шаги

- Контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Сведения об использовании экземпляров для ресурсоемких вычислений при запуске приложений MPI с использованием пакетной службы Azure см. в статье [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе](../../batch/batch-mpi.md).

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
