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
ms.openlocfilehash: 00661043d1ec9769adbf4119a2c9c1925dcd29fa
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186298"
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
- [Сетка 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Сетка 10,1 (442,06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (. exe) 

Для Windows Server 2012 R2: 
- [Сетка 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)
- [Сетка 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
