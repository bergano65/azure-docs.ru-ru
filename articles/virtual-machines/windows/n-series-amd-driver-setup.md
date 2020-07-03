---
title: Установка драйвера GPU AMD для Azure серии N для Windows
description: Как настроить драйверы AMD GPU для виртуальных машин серии N под управлением Windows Server или Windows в Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198000"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Установка драйверов GPU AMD на виртуальных машинах серии N под управлением Windows

Чтобы воспользоваться возможностями GPU новых виртуальных машин Azure NVv4 Series под Windows, необходимо установить драйверы AMD GPU. [Расширение драйвера GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) устанавливает драйверы AMD GPU на виртуальной машине серии NVv4. Для установки расширения и управления им можно использовать портал Azure или такие инструменты, как Azure PowerShell и шаблоны Azure Resource Manager. Поддерживаемые операционные системы и шаги по развертыванию см. в [документации по расширению драйвера GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) .

Если вы решили установить драйверы AMD GPU вручную, в этой статье содержатся Поддерживаемые операционные системы, драйверы, а также действия по установке и проверке.

На виртуальных машинах NVv4 поддерживаются только драйверы GPU, опубликованные корпорацией Майкрософт. НЕ устанавливайте драйверы GPU из других источников.

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

| Операционная система | Драйвер |
| -------- |------------- |
| Windows 10 ЕВД — сборка 1903 <br/><br/>Windows 10 — сборка 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Установка драйвера

1. Подключитесь по удаленный рабочий стол к каждой виртуальной машине серии NVv4.

2. Если вы являетесь клиентом NVv4 Preview, остановите виртуальную машину и дождитесь ее перехода в состояние "остановлено (освобождено)".

3. Запустите виртуальную машину и скачайте последнюю версию [служебной программы для очистки AMD](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Удалите существующий драйвер, выполнив "amdcleanuputility-x64. exe". НЕ используйте существующую служебную программу очистки, которая была установлена с предыдущим драйвером.  

4. Скачайте и установите последнюю версию драйвера.

5. Перезагрузите виртуальную машину.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная конфигурация карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/device-manager.png)

С помощью Dxdiag можно проверить свойства графического процессора, включая видео RAM. В следующем примере показан раздел 1/2 карты Radeon порывом MI25 на виртуальной машине Azure NVv4.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Если вы используете Windows 10 Build 1903 или более поздней версии, служба dxdiag не будет отображать сведения на вкладке "дисплей". Используйте параметр "сохранить все сведения" в нижней части, и в выходном файле отобразятся сведения, связанные с AMD MI25 GPU.

![Свойства драйвера GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


