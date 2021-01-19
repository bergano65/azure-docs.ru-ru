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
ms.openlocfilehash: 7a6962a0fa1374edb5a9f43641a0adf398708acf
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570720"
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nvidia-tesla-cuda-drivers"></a>Драйверы NVIDIA Tesla (CUDA)

Драйверы NVIDIA Tesla (CUDA) для виртуальных машин NC, NCv2, NCv3, NCasT4_v3, ND и NDv2 (необязательно для серии NV) поддерживаются только в операционных системах, перечисленных в следующей таблице. Ссылки для скачивания драйверов актуальны на момент публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Windows Server можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Windows Server 2016 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.


| OS | Драйвер |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт повторно распространяет установщики драйверов сетки NVIDIA для виртуальных машин серии NV и NVv3, используемых в качестве виртуальных рабочих станций или для виртуальных приложений. Устанавливайте только эти драйверы сетки на виртуальных машинах Azure серии NV только в операционных системах, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure. Вам не нужно настраивать сервер лицензий на программное обеспечение NVIDIA GPU.

Драйверы сетки, распространяемые Azure, не работают на виртуальных машинах серии, отличных от NV, таких как NCv2, NCv3, ND и NDv2. Единственным исключением является NCas_T4_V3 серия виртуальных машин, в которой драйверы сетки будут включать графические функции, аналогичные возможностям серии NV.

NC-Series с графическими процессорами NVIDIA K80 не поддерживают приложения сетки и графики.  

Обратите внимание, что расширение NVIDIA всегда установит последнюю версию драйвера. Мы предоставляем ссылки на предыдущую версию для клиентов, которые имеют зависимость от более старой версии.

Для Windows Server 2019, Windows Server 2016 и Windows 10 (до сборки 2004):
- [Сетка 11,3 (452,77)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Сетка 11,2 (452,57)](https://download.microsoft.com/download/1/b/1/1b15516a-de49-4ba4-8651-6abda4f7fb82/452.57_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

Для Windows Server 2012 R2: 
- [Сетка 11,3 (452,77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (. exe) 
- [Сетка 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe)

Полный список всех ссылок на драйверы предыдущей сетки NVIDIA см. на сайте [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) .
