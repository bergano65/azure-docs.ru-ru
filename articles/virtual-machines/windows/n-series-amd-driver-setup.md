---
title: Установка драйвера GPU AMD для Azure серии N для Windows
description: Как настроить драйверы AMD GPU для виртуальных машин серии N под управлением Windows Server или Windows в Azure
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
ms.openlocfilehash: 6f0aa8a08b8a502edbd15d3ede157b78a13b8588
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479362"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Установка драйверов GPU AMD на виртуальных машинах серии N под управлением Windows

Чтобы воспользоваться возможностями GPU новых виртуальных машин Azure NVv4 Series под Windows, необходимо установить драйверы AMD GPU. Расширение драйвера AMD будет доступно в ближайшие недели. Эта статья содержит поддерживаемые операционные системы, драйверы, а также действия по установке и проверке вручную.

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

| ОС | Драйвер |
| -------- |------------- |
| Windows 10 ЕВД — сборка 1903 <br/><br/>Windows 10 — сборка 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Установка драйвера

1. Подключитесь по удаленный рабочий стол к каждой виртуальной машине серии NVv4.

1. Скачайте, извлеките и установите поддерживаемый драйвер для своей операционной системы Windows.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
Свойства драйвера GPU ![](./media/n-series-amd-driver-setup/device-manager.png)

С помощью Dxdiag можно проверить свойства графического процессора, включая видео RAM. В следующем примере показана 1-8 Секция карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
Свойства драйвера GPU ![](./media/n-series-amd-driver-setup/dxdiag.png)
