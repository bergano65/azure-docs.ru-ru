---
title: включение файла
description: включение файла
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b8aba410cd447f14fcce89fee93c5f6a253a34ce
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102404"
---
## <a name="supported-distributions-and-drivers"></a>Поддерживаемые дистрибутивы и драйверы

### <a name="nvidia-cuda-drivers"></a>Драйверы NVIDIA CUDA

Драйверы NVIDIA CUDA для виртуальных машин серий NC, NCv2, NCv3, ND и NDv2 (необязательно для серии NV) поддерживаются только в дистрибутивах Linux, перечисленных в следующей таблице. Приведенные сведения о драйверах CUDA актуальны на время публикации. Последние версии драйверов CUDA можно получить на веб-сайте [NVIDIA](https://developer.nvidia.com/cuda-zone). Убедитесь, что вы установили последнюю версию драйверов CUDA для своего дистрибутива или выполнили обновление до этой версии. 

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Linux можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Ubuntu 16.04 LTS или CentOS 7.4 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.

| Дистрибутив | Драйвер |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> Версия 7.3, 7.4, 7.5, 7.6 на платформе CentOS, HPC на платформе CentOS 7.4 | NVIDIA CUDA 10.0, драйвер серии R410 |

### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт распространяет установщики драйверов NVIDIA GRID для виртуальных машин серии NV и NVv2, используемых в качестве виртуальных рабочих станций или виртуальных приложений. Эти драйверы GRID следует устанавливать только на виртуальные машины Azure серии NV под управлением операционных систем, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure. Вам не нужно настраивать сервер лицензий программного обеспечения vGPU NVIDIA.

| Дистрибутив | Драйвер |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>Версия 7.3, 7.4, 7.5, 7.6 на основе CentOS | NVIDIA GRID 7.1, ветвь драйверов R410|

> [!WARNING] 
> Установка стороннего программного обеспечения на продуктах Red Hat может нарушать условия технической поддержки Red Hat. Ознакомьтесь со [статьей из базы знаний Red Hat](https://access.redhat.com/articles/1067).
>
