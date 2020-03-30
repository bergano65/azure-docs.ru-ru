---
title: H-серия - Виртуальные машины Azure
description: Спецификации для V-серии VMs.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470509"
---
# <a name="h-series"></a>Серия H

VMs-серии оптимизированы для приложений, управляемых высокими частотами процессора или большой памятью на основные требования. VMs-серии оснащены 8 или 16 процессорами Intel Xeon E5 2667 v3, до 14 ГБ оперативной памяти на ядро процессора и отсутствием гиперпоточности. H-серия имеет 56 Gb/sec Mellanox FDR InfiniBand в неблокирующей конфигурации жирного дерева для последовательной производительности RDMA. VMs-серии поддерживают Intel MPI 5.x и MS-MPI.

ACU: 290–300

Хранилище класса Premium: не поддерживается

Кэширование в хранилище класса Premium: не поддерживается

Миграция в реальном времени: не поддерживается

Сохранение памяти Обновления: Не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти GB/s | Базовая частота процессора (ГГц) | Частота всех ядер (ГГц, пик) | Одноядерная частота (ГГц, пик) | Производительность RDMA (Gb/s) | Поддержка MPI | Темп хранения (GB) | Максимальное число дисков данных | МАКС Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Для MPI-приложений специальная сеть бэкэнда RDMA включена сетью FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Поддерживаемые изображения ОС (Linux)
 
На рынке Azure есть много дистрибутивов Linux, которые поддерживают подключение RDMA:
  
* **CentOS основе HPC** - Для не-SR-IOV включен VMs, CentOS на основе версии 6.5 HPC или более поздней версии, до 7,5 подходят. Для VMs-серии рекомендуется версии от 7.1 до 7.5. Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.
  Для SR-IOV VMs CentOS-HPC 7.6 оптимизирован и предварительно загружен с установленными драйверами RDMA и различными пакетами MPI.
  Для других изображений RHEL/CentOS VM добавьте расширение InfiniBandLinux, чтобы включить InfiniBand. Это расширение Linux VM устанавливает Драйверы Mellanox OFED (на SR-IOV VMs) для подключения RDMA. Следующие PowerShell cmdlet устанавливает последнюю версию (версия 1.0) расширения InfiniBandDriverLinux на существующий RDMA-способный VM. RDMA-способный VM называется *myVM* и развернут в ресурсной группе под названием *myResourceGroup* в *регионе Западной США* следующим образом:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Кроме того, расширения VM могут быть включены в шаблоны Azure Resource Manager для легкого развертывания со следующим элементом JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Следующая команда устанавливает последнюю версию 1.0 InfiniBandDriverLinux расширение на всех RDMA-способных VMs в существующих виртуальных машин масштаба набор под названием *myVMSS* развернуты в группе ресурсов под названием *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > В образах HPC на основе CentOS обновления ядра отключены в файле конфигурации **yum** . Это связано с тем, что драйверы Linux RDMA распространяются в виде пакета RPM, и обновления драйверов могут не работать при обновлении ядра.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 для HPC, SLES 12 SP3 для HPC (Премиум), SLES 12 SP1 для HPC, SLES 12 SP1 для HPC (Премиум), SLES 12 SP4 и SLES 15. Драйверы RDMA и пакеты Intel MPI будут установлены на виртуальной машине. Установите MPI, выполнив следующую команду:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Для получения более подробной информации о включении InfiniBand, настройка MPI, [см.](./workloads/hpc/enable-infiniband.md)

## <a name="other-sizes"></a>Остальные размеры

- [Общее назначение](sizes-general.md)
- [Оптимизирована память](sizes-memory.md)
- [Оптимизированные для операций в хранилище](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
