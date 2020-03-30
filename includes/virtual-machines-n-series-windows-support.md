---
title: включить файл
description: включить файл
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135052"
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nvidia-tesla-cuda-drivers"></a>Драйверы NVIDIA Tesla (CUDA)

Драйверы NVIDIA Tesla (CUDA) для виртуальных машин серий NC, NCv2, NCv3, ND и NDv2 (необязательно для серии NV) поддерживаются только в операционных системах, перечисленных в следующей таблице. Ссылки для скачивания драйверов актуальны на момент публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Windows Server можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Windows Server 2016 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.


| OS | Драйвер |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (EXE-файл) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (EXE-файл) |

### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт перераспределяет установки драйверов NVIDIA GRID для Виртуальных и NVv3-серий, используемых в качестве виртуальных рабочих станций или для виртуальных приложений. Установите только эти драйверы GRID на VMs-мизанс серии Azure NV, только на операционные системы, перечисленные в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure. Вам не нужно настраивать сервер лицензии на программное обеспечение NVIDIA vGPU.

Обратите внимание, что расширение Nvidia всегда будет устанавливать последний драйвер. Мы предоставляем ссылки на предыдущую версию здесь для клиентов, которые имеют зависимость от старой версии.

Для Windows Server 2019, Windows Server 2016 и Windows 10 (до сборки 1909):
- [ГРИД 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [ГРИД 10.0 (441.66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Для Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 и Windows 7: 
- [ГРИД 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [ГРИД 10.0 (441.66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
