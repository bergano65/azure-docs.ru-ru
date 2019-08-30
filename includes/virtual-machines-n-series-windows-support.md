---
title: включение файла
description: включение файла
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bab282fded7e4b30d2eca6ed51ceaecf22206869
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166774"
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

Корпорация Майкрософт повторно распространяет установщики драйверов сетки NVIDIA для виртуальных машин серии NV и NVv3, используемых в качестве виртуальных рабочих станций или для виртуальных приложений. Устанавливайте только эти драйверы сетки на виртуальных машинах Azure серии NV только в операционных системах, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure. Вам не нужно настраивать сервер лицензий на программное обеспечение NVIDIA GPU.

Обратите внимание, что расширение NVIDIA всегда установит драйвер латст. Мы предоставляем ссылки на предыдущую версию для клиентов, которые имеют зависимость от более старой версии.

| OS | Драйвер |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Сетка 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [Сетка 8,0 (425,31)](https://download.microsoft.com/download/4/8/C/48C2D46E-EB64-460E-A8D9-0F55737D0D68/425.31_grid_win10_server2016_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Сетка 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [Сетка 8,0 (425,31)](https://download.microsoft.com/download/6/D/7/6D73C628-B5FB-4243-9520-DAEF363223CB/425.31_grid_win8_win7_server2012R2_server2008R2_64bit_international.exe) (. exe)  |
