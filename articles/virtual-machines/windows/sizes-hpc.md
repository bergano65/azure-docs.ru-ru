---
title: Размеры виртуальных машин Windows в Azure — HPC
description: Список различных размеров виртуальных машин Windows в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: e96c74eb4b9b96459bb5de7db2faeb47ed99a82e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065455"
---
# <a name="high-performance-compute-vm-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Операционная система** — Windows Server 2016 на всех перечисленных выше виртуальных машинах серии HPC. Windows Server 2012 R2, Windows Server 2012 также поддерживаются на виртуальных машинах, не использующих SR-IOV (таким образом, исключая ХБ и HC).

* **MPI** . размеры виртуальных машин с поддержкой SR-IOV в Azure (ХБ, HC) позволяют использовать почти любую версию MPI с Mellanox офед.
На виртуальных машинах, не использующих SR-IOV, поддерживаемые реализации MPI используют интерфейс Microsoft Network Direct (ND) для обмена данными между экземплярами. Таким образом, поддерживаются только версии Microsoft MPI (MS-MPI) 2012 R2 и более поздних версий и Intel MPI 5. x. Более поздние версии (2017, 2018) библиотеки Intel MPI Runtime могут быть несовместимыми с драйверами Azure RDMA.

* **Расширение виртуальной машины инфинибанддривервиндовс** . на виртуальных машинах с поддержкой RDMA добавьте расширение инфинибанддривервиндовс, чтобы включить InfiniBand. Это расширение виртуальной машины Windows устанавливает драйверы сетевого Direct Windows (на виртуальных машинах без SR-IOV) или драйверы Mellanox ОФЕД (на виртуальных машинах SR-IOV) для подключения RDMA.
В некоторых развертываниях экземпляров A8 и A9 расширение HpcVmDrivers добавляется автоматически. Обратите внимание, что расширение виртуальной машины HpcVmDrivers является устаревшим. Он не будет обновлен. Чтобы добавить в виртуальную машину расширение виртуальной машины, можно использовать командлеты [Azure PowerShell](/powershell/azure/overview). 

  Следующая команда устанавливает последнюю версию расширения Инфинибанддривервиндовс версии 1,0 на существующую виртуальную машину с поддержкой RDMA с именем *myVM* , развернутую в группе ресурсов с именем *MyResourceGroup* в регионе *Западная часть США* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Кроме того, расширения ВМ можно добавить в шаблоны Azure Resource Manager для упрощения развертывания со следующим элементом JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Следующая команда устанавливает последнюю 1,0 версию расширения Инфинибанддривервиндовс на всех виртуальных машинах с поддержкой RDMA в существующем масштабируемом наборе виртуальных машин с именем *myVMSS* , развернутом в группе ресурсов с именем *myResourceGroup*:

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

* **Масштабируемые наборы виртуальных машин** — в масштабируемом наборе виртуальных машин убедитесь, что развертывание ограничивается одной группой размещения. Например, в шаблоне Resource Manager задайте значение `singlePlacementGroup` для свойства `true`. 

* **MPI между виртуальными машинами** . Если между виртуальными машинами требуется подключение MPI, убедитесь, что виртуальные машины находятся в одной группе доступности или на виртуальной машине с одинаковым масштабируемым набором.

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

## <a name="next-steps"></a>Дополнительная информация

- Контрольные списки для использования экземпляров с большим объемом вычислений с пакетом HPC на Windows Server см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Сведения об использовании экземпляров для ресурсоемких вычислений при запуске приложений MPI с использованием пакетной службы Azure см. в статье [Использование задач с несколькими экземплярами для запуска приложений с интерфейсом передачи сообщений в пакетной службе](../../batch/batch-mpi.md).

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
