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
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704683"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Размеры виртуальных машин, оптимизированных для высокопроизводительных вычислений

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Поддерживаются только версии Intel MPI 5.x.

> [!NOTE]
> Более поздние версии (2017, 2018) среды выполнения Intel MPI библиотеки могут или не могут быть совместимы с драйверами Azure Linux RDMA.

### <a name="distributions"></a>Дистрибутивы
 
Разверните виртуальные машины для ресурсоемких вычислений из одного из образов с поддержкой подключения RDMA в Azure Marketplace:
  
* **Ubuntu** — Ubuntu Server 16.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** — SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (уровень "Премиум"), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (уровень "Премиум"). Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **На основе centOS HPC** — на основе CentOS HPC 6.5 или более поздней версии (для серии H рекомендуется 7.1 или более поздняя версия). Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.  
 
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM и обновления драйверов могут не сработать в случае обновления ядра.
  > 
 
### <a name="cluster-configuration-options"></a>Параметры конфигурации кластера

Azure предоставляет несколько вариантов для создания кластеров пакета HPC на виртуальных машинах Linux, которые могут взаимодействовать с помощью сети RDMA, включая: 

* **Виртуальные машины**. Разверните виртуальные машины HPC с поддержкой RDMA в одну группу доступности (используя модель развертывания Azure Resource Manager). Если вы используете классическую модель развертывания, разверните виртуальные машины в одну облачную службу. 

* **Масштабируемый набор виртуальных машин**. В масштабируемом наборе виртуальных машин ограничьте развертывание одной группой размещения. Например, в шаблоне Resource Manager задайте значение `true` для свойства `singlePlacementGroup`. 

* **Azure CycleCloud**. Создайте кластер HPC в [Azure CycleCloud](/azure/cyclecloud/), чтобы запустить задания MPI на узлах Linux.

* **Пакетная служба Azure**. Создайте пул [пакетной службы Azure](/azure/batch/) для выполнения рабочих нагрузок MPI на вычислительных узлах Linux. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](../../batch/batch-pool-compute-intensive-sizes.md). Чтобы запустить рабочие нагрузки на основе контейнера в пакетной службе, ознакомьтесь с проектом [Batch Shipyard](https://github.com/Azure/batch-shipyard).

* **Пакет Microsoft HPC** - [Пакет HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) поддерживает несколько дистрибутивов Linux, выполняемых на вычислительных узлах, развернутых в виртуальных машинах Azure с поддержкой RDMA, под управлением головного узла Windows Server. Пример развертывания см. в статье [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

В зависимости от выбора средства управления кластером для запуска заданий MPI может потребоваться дополнительная настройка системы. Например, в кластере виртуальных машин может потребоваться установить отношения доверия между узлами кластера, создав ключи SSH или установив доверие SSH без использования пароля.

### <a name="network-topology-considerations"></a>Аспекты топологии сети
* На виртуальных машинах Linux с поддержкой RDMA интерфейс Eth1 зарезервирован для сетевого трафика RDMA. Не изменяйте параметры Eth1 и любые данные в файле конфигурации при обращении к этой сети. Eth0 зарезервирован для обычного сетевого трафика Azure.

* Сеть RDMA в Azure резервирует адресное пространство 172.16.0.0/16. 




## <a name="other-sizes"></a>Остальные размеры
- [Универсальные](sizes-general.md)
- [Оптимизированные для вычислений](sizes-compute.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.




