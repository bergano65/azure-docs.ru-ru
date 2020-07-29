---
title: Виртуальные машины Azure серии H
description: Спецификации для виртуальных машин серии H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: 34f89656fc56b27edafeff0e60874a00139266dd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268957"
---
# <a name="h-series"></a>Серия H

Виртуальные машины серии H оптимизированы для приложений, которые управляются высокой частотой ЦП или большими объемами памяти на базовые требования. Виртуальные машины серии H, компоненты 8 или 16 процессоров Intel Xeon 3 2667 v3, до 14 ГБ ОЗУ на ядро ЦП и без технологии Hyper-Threading. Функции серии H 56 ГБ/с Mellanox FDR InfiniBand в неблокирующей конфигурации дерева FAT для обеспечения постоянной производительности RDMA. Виртуальные машины серии H поддерживают Intel MPI 5. x и MS-MPI.

ACU: 290–300

Хранилище класса "Премиум":  Не поддерживается

Кэширование в хранилище класса Premium: не поддерживается

Динамическая миграция: Не поддерживается

Обновления с сохранением памяти: Не поддерживается

| Размер | vCPU | Процессор | Память (ГБ) | Пропускная способность памяти ГБ/с | Базовая частота ЦП (ГГц) | Частота ядер (ГГц, пик) | Частота с одним ядром (ГГц, пик) | Производительность RDMA (ГБ/с) | Поддержка MPI | Хранилище Temp (ГБ) | Максимальное число дисков данных | Максимальная пропускная способность дисков, операций ввода-вывода в секунду | Максимальное число сетевых адаптеров Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon, версия 3, 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon, версия 3, 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H8m  | 8  | Intel Xeon, версия 3, 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon, версия 3, 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon, версия 3, 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon, версия 3, 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |

<sup>1</sup> для приложений MPI сеть с выделенной внутренней сетью RDMA включена в сети FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Поддерживаемые образы ОС (Linux)
 
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
  
  Следующая команда устанавливает последнюю версию расширения Инфинибанддриверлинукс версии 1,0 на всех виртуальных машинах с поддержкой RDMA в существующем масштабируемом наборе виртуальных машин с именем *myVMSS* , развернутом в группе ресурсов с именем *myResourceGroup*:
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Дополнительные сведения о включении InfiniBand с настройкой MPI см. в разделе [Enable InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Остальные размеры

- [Универсальные](sizes-general.md)
- [Оптимизированные для памяти](sizes-memory.md)
- [Оптимизированные для хранилища](sizes-storage.md)
- [Оптимизированные для GPU](sizes-gpu.md)
- [Для высокопроизводительных вычислений](sizes-hpc.md)
- [Предыдущие поколения](sizes-previous-gen.md)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](acu.md) сравнить производительность вычислений для различных номеров SKU Azure.
