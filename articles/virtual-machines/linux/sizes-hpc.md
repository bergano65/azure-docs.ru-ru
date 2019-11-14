---
title: Размеры виртуальных машин Linux в Azure — HPC
description: Список различных размеров виртуальных машин Linux в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034897"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Размеры виртуальных машин с поддержкой SR-IOV в Azure позволяют использовать почти любую разновидность MPI.
На виртуальных машинах, не использующих SR-IOV, поддерживаются только версии Intel MPI 5. x. Более поздние версии (2017, 2018) библиотеки Intel MPI Runtime могут быть несовместимыми с драйверами RDMA для Linux Azure.


### <a name="supported-os-images"></a>Поддерживаемые образы ОС
 
В Azure Marketplace имеется множество дистрибутивов Linux, поддерживающих подключение RDMA.
  
* **HPC на основе CentOS** . для виртуальных машин, не использующих SR-IOV, CentOS версии 6,5 HPC или более поздней версии, подходят до 7,5. Для виртуальных машин серии H рекомендуется использовать версии 7,1 и 7,5. Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.
  Для виртуальных машин SR-IOV CentOS-HPC 7,6 оптимизирован и предварительно загружен с драйверами RDMA и различными установленными пакетами MPI.
  Для других образов виртуальных машин RHEL/CentOS добавьте расширение Инфинибандлинукс, чтобы включить InfiniBand. Это расширение виртуальной машины Linux устанавливает драйверы Mellanox ОФЕД (на виртуальных машинах SR-IOV) для подключения RDMA. Следующий командлет PowerShell устанавливает последнюю версию (версии 1,0) расширения Инфинибанддриверлинукс на существующую виртуальную машину с поддержкой RDMA. Виртуальная машина с поддержкой RDMA называется *myVM* и развертывается в группе ресурсов с именем *MyResourceGroup* в регионе *Западная часть США* следующим образом:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Кроме того, расширения ВМ можно добавить в шаблоны Azure Resource Manager для простоты развертывания с помощью следующего элемента JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Следующая команда устанавливает последнюю 1,0 версию расширения Инфинибанддриверлинукс на всех виртуальных машинах с поддержкой RDMA в существующем масштабируемом наборе виртуальных машин с именем *myVMSS* , развернутом в группе ресурсов с именем *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM, а обновления драйверов могут не работать при обновлении ядра.
  >
  

* **SUSE Linux Enterprise Server** SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (Premium), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Premium), SLES 12 SP4 и SLES 15. Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu Server 16,04 LTS, 18,04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Дополнительные сведения о включении InfiniBand с настройкой MPI см. в разделе [Enable InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Параметры конфигурации кластера

Azure предоставляет несколько вариантов для создания кластеров пакета HPC на виртуальных машинах Linux, которые могут взаимодействовать с помощью сети RDMA, включая: 

* **Виртуальные машины**. Разверните виртуальные машины HPC с поддержкой RDMA в одну группу доступности (используя модель развертывания Azure Resource Manager). Если вы используете классическую модель развертывания, разверните виртуальные машины в одну облачную службу. 

* **Масштабируемые наборы виртуальных машин** — в масштабируемом наборе виртуальных машин убедитесь, что развертывание ограничивается одной группой размещения. Например, в шаблоне Resource Manager задайте значение `singlePlacementGroup` для свойства `true`. 

* **MPI между виртуальными машинами** . Если между виртуальными машинами требуется подключение MPI, убедитесь, что виртуальные машины находятся в одной группе доступности или на виртуальной машине с одинаковым масштабируемым набором.

* **Azure CycleCloud**. Создайте кластер HPC в [Azure CycleCloud](/azure/cyclecloud/), чтобы запустить задания MPI на узлах Linux.

* **Пакетная служба Azure**. Создайте пул [пакетной службы Azure](/azure/batch/) для выполнения рабочих нагрузок MPI на вычислительных узлах Linux. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](../../batch/batch-pool-compute-intensive-sizes.md). Чтобы запустить рабочие нагрузки на основе контейнера в пакетной службе, ознакомьтесь с проектом [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Пакет Microsoft HPC** - [Пакет HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) поддерживает несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure с поддержкой RDMA, под управлением головного узла Windows Server. Пример развертывания см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Рекомендации относительно сети
* На виртуальных машинах Linux с поддержкой RDMA в Azure без SR-IOV eth1 зарезервировано для сетевого трафика RDMA. Не изменяйте параметры eth1 и любые сведения в файле конфигурации, ссылающиеся на эту сеть.
* На виртуальных машинах с поддержкой SR-IOV (ХБ и HC Series) IB0 зарезервирован для сетевого трафика RDMA.
* Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. Чтобы выполнять приложения MPI в экземплярах, развернутых в виртуальной сети Azure, убедитесь, что адресное пространство виртуальной сети не пересекается с сетью RDMA.
* В зависимости от выбора средства управления кластером для запуска заданий MPI может потребоваться дополнительная настройка системы. Например, в кластере виртуальных машин может потребоваться установить отношение доверия между узлами кластера путем создания ключей SSH или без пароля для входа SSH.


## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о настройке, оптимизации и масштабировании [рабочих нагрузок HPC](../workloads/hpc/configure.md) в Azure.
- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
