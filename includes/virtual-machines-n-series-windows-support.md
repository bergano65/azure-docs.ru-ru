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
ms.openlocfilehash: bfb7d1d52549d7fda9547b65a259fe2ce73f8839
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997905"
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nvidia-tesla-cuda-drivers"></a>Драйверы NVIDIA Tesla (CUDA)

Драйверы NVIDIA Tesla (CUDA) для виртуальных машин NC, NCv2, NCv3, NCasT4_v3, ND и NDv2 (необязательно для серии NV) поддерживаются только в операционных системах, перечисленных в следующей таблице. Ссылки для скачивания драйверов актуальны на момент публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Windows Server можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Windows Server 2016 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.


| OS | Драйвер |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт повторно распространяет установщики драйверов сетки NVIDIA для виртуальных машин серии NV и NVv3, используемых в качестве виртуальных рабочих станций или для виртуальных приложений. Устанавливайте только эти драйверы сетки на виртуальных машинах Azure серии NV только в операционных системах, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure. Вам не нужно настраивать сервер лицензий на программное обеспечение NVIDIA GPU.

Драйверы сетки, распространяемые Azure, не работают на виртуальных машинах серии, отличных от NV, таких как NC, NCv2, NCv3, ND и NDv2.

Обратите внимание, что расширение NVIDIA всегда установит последнюю версию драйвера. Мы предоставляем ссылки на предыдущую версию для клиентов, которые имеют зависимость от более старой версии.

Для Windows Server 2019, Windows Server 2016 и Windows 10 (до сборки 2004):
- [Сетка 11 (452,39)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Сетка 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

Для Windows Server 2012 R2: 
- [Сетка 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 
- [Сетка 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
