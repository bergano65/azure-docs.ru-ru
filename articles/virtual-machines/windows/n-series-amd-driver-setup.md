---
title: Настройка драйвера gPU серии Azure N для Windows
description: Как настроить драйверы amD GPU для N-серии VMs под управлением Windows Server или Windows в Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 883dbc95ee77d03aee4c3231c6ab8c03f9f7f6e4
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387841"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Установка драйверов AMD GPU на N-серии VMs под управлением Windows

Чтобы воспользоваться возможностями графического процессора новой серии Azure NVv4 под управлением Windows, должны быть установлены драйверы ГРАФИЧЕСКОго процессора AMD. Расширение драйвера AMD будет доступно в ближайшие недели. В этой статье предусмотрены поддерживаемые операционные системы, драйверы, а также шаги ручной установки и проверки.

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

| OS | Драйвер |
| -------- |------------- |
| Windows 10 EVD - Сборка 1903 <br/><br/>Windows 10 - Сборка 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.No1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Установка драйвера

1. Подключайтесь с помощью удаленного рабочего стола к каждому VM серии NVv4.

2. Если вы являетесь клиентом предварительного просмотра NVv4, пожалуйста, остановите VM и подождите, пока он перейдет в состояние остановленного (Deallocated).

3. Пожалуйста, запустите VM и скачать последнюю [AMD очистки утилиты](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Удалите существующий драйвер, запустив "amdcleanuputility-x64.exe". Пожалуйста, НЕ используйте любые exisitng очистки утилита, которая была установлена с предыдущим драйвером.  

4. Скачать и установить последний драйвер.

5. Перезагрузите виртуальную машину.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. Ниже приводится успешная конфигурация карты Radeon Instinct MI25 на Azure NVv4 VM.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/device-manager.png)

Вы можете использовать dxdiag для проверки свойств отображения GPU, включая оперативную память видео. В следующем примере показана 1/8-я часть карты Radeon Instinct MI25 на Azure NVv4 VM.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/dxdiag.png)
