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
ms.openlocfilehash: 6dc8c54b9d138ab62e086cca59cd5b4801fa6130
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228340"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Установка драйверов GPU AMD на виртуальных машинах серии N под управлением Windows

Чтобы воспользоваться возможностями GPU новых виртуальных машин Azure NVv4 Series под Windows, необходимо установить драйверы AMD GPU. Расширение драйвера AMD будет доступно в ближайшие недели. Эта статья содержит поддерживаемые операционные системы, драйверы, а также действия по установке и проверке вручную.

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

| OS | Драйвер |
| -------- |------------- |
| Windows 10 ЕВД — сборка 1903 <br/><br/>Windows 10 — сборка 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Установка драйвера

1. Подключитесь по удаленный рабочий стол к каждой виртуальной машине серии NVv4.

2. Если вы являетесь клиентом NVv4 Preview, остановите виртуальную машину и дождитесь ее перехода в состояние "остановлено (освобождено)".

3. Запустите виртуальную машину, а затем удалите предварительную версию драйвера, выполнив команду "amdcleanuputility-x64. exe", расположенную в папке ". ..\Амдклеанунинсталлутилити". Точный путь будет зависеть от того, где находятся файлы установки предыдущего драйвера.  

4. Скачайте и установите последнюю версию драйвера.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
Свойства драйвера GPU ![](./media/n-series-amd-driver-setup/device-manager.png)

С помощью Dxdiag можно проверить свойства графического процессора, включая видео RAM. В следующем примере показана 1-8 Секция карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
Свойства драйвера GPU ![](./media/n-series-amd-driver-setup/dxdiag.png)
