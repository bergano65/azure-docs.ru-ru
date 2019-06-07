---
title: Размеры виртуальных машин Linux в Azure, оптимизированных для HPC | Документация Майкрософт
description: Список различных размеров виртуальных машин Linux в Azure, оптимизированных для высокопроизводительных вычислений. Сведения о количестве виртуальных ЦП, дисков данных и сетевых адаптеров, а также о пропускной способности хранилища и сети для размеров виртуальных машин этой серии.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 32b0f467f11cf8cb0a04657006cb5a86b11e27e9
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755160"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Параметры SR-IOV включена размеры виртуальных Машин в Azure позволяют почти любой разновидности MPI для использования.
На виртуальные машины не SR-IOV поддерживаются только версии Intel MPI 5.x. Более поздние версии (2017, 2018) среды выполнения Intel MPI библиотеки могут или не могут быть совместимы с драйверами Azure Linux RDMA.


### <a name="supported-os-images"></a>Поддерживаемые образы ОС
 
Azure Marketplace состоит из многих дистрибутивов Linux, поддерживающих подключение RDMA.
  
* **На базе centOS HPC** — не SR-IOV включен, виртуальные машины, на базе CentOS версии 6.5 HPC или более поздней версии, до 7.5 подходят. Для виртуальных машин серии H рекомендуется использовать версии 7.1 для 7.5. Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.
  Для виртуальных машин, SR-IOV CentOS HPC 7.6 поставляется оптимизированных и предварительно загружаются драйверы RDMA и различные пакеты MPI.
  Для других образов виртуальных Машин RHEL и CentOS добавьте расширение InfiniBandLinux для включения InfiniBand. Это расширение виртуальной Машины Linux устанавливает драйверы Mellanox OFED (на виртуальных машинах SR-IOV) для подключения RDMA. Следующий командлет PowerShell устанавливает последнюю версию (версия 1.0) InfiniBandDriverLinux расширение на существующей виртуальной Машины с поддержкой RDMA. Именем виртуальной Машины с поддержкой RDMA *myVM* и развертывается в группе ресурсов с именем *myResourceGroup* в *Западная часть США* регион следующим образом:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Кроме того расширения виртуальных Машин могут быть включены в шаблоны Azure Resource Manager для более удобного развертывания с помощью следующего элемента JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
 
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это потому, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не работать, если обновления ядра.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (Premium), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Premium), SLES 12 SP4 и SLES 15. Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16.04 LTS, 18.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Дополнительные сведения о включении InfiniBand, Настройка MPI, см. в разделе [включить InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband-with-sriov).


### <a name="cluster-configuration-options"></a>Параметры конфигурации кластера

Azure предоставляет несколько вариантов для создания кластеров пакета HPC на виртуальных машинах Linux, которые могут взаимодействовать с помощью сети RDMA, включая: 

* **Виртуальные машины**. Разверните виртуальные машины HPC с поддержкой RDMA в одну группу доступности (используя модель развертывания Azure Resource Manager). Если вы используете классическую модель развертывания, разверните виртуальные машины в одну облачную службу. 

* **Масштабируемые наборы виртуальных машин** — в масштабируемый набор виртуальных машин задано, убедитесь, что вы ограниченного развертывания одной группы размещения. Например, в шаблоне Resource Manager задайте значение `true` для свойства `singlePlacementGroup`. 

* **Azure CycleCloud**. Создайте кластер HPC в [Azure CycleCloud](/azure/cyclecloud/), чтобы запустить задания MPI на узлах Linux.

* **Пакетная служба Azure**. Создайте пул [пакетной службы Azure](/azure/batch/) для выполнения рабочих нагрузок MPI на вычислительных узлах Linux. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](../../batch/batch-pool-compute-intensive-sizes.md). Чтобы запустить рабочие нагрузки на основе контейнера в пакетной службе, ознакомьтесь с проектом [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Пакет Microsoft HPC** - [Пакет HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) поддерживает несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure с поддержкой RDMA, под управлением головного узла Windows Server. Пример развертывания см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Рекомендации относительно сети
* На не SR-IOV с поддержкой виртуальных машин Linux в Azure, eth1 зарезервировано RDMA сетевого трафика. Не изменяйте параметры eth1 и любые данные в файле конфигурации при обращении к этой сети.
* На виртуальные машины, (HB и серии HC) с поддержкой SR-IOV, ib0 зарезервировано RDMA сетевого трафика.
* Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. Чтобы выполнять приложения MPI в экземплярах, развернутых в виртуальной сети Azure, убедитесь, что адресное пространство виртуальной сети не пересекается с сетью RDMA.
* В зависимости от выбора средства управления кластером для запуска заданий MPI может потребоваться дополнительная настройка системы. Например в кластере виртуальных машин, может потребоваться установить доверительные отношения между узлами кластера, путем создания ключей SSH или установив входа по протоколу SSH без пароля.


## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о том, как настроить, оптимизировать и масштабировать [рабочих нагрузок HPC](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc) в Azure.
- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
