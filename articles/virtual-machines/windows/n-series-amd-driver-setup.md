---
title: Установка драйвера GPU AMD для виртуальных машин Azure серии N под управлением Windows
description: Сведения о том, как установить драйверы GPU AMD для виртуальных машин серии N под управлением Windows Server или Windows в Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 745ec7ebf792fe1165022516be4c83fb9e864cc9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799882"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Установка драйверов GPU AMD на виртуальные машины серии N под управлением Windows

Чтобы воспользоваться преимуществами GPU на новых виртуальных машинах Azure серии NVv4 под управлением Windows, необходимо установить драйверы GPU AMD. [Расширение драйвера GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) устанавливает драйверы GPU AMD на виртуальную машину серии NVv4. Для установки расширения и управления им можно использовать портал Azure или такие инструменты, как Azure PowerShell и шаблоны Azure Resource Manager. Сведения о поддерживаемых операционных системах и этапах развертывания см. в [документации по расширению драйвера GPU AMD](../extensions/hpccompute-amd-gpu-windows.md).

Если вы решили установить драйверы GPU AMD вручную, то в этой статье вы найдете сведения о поддерживаемых операционных системах, ссылки на драйверы и инструкции по установке и проверке.

На виртуальных машинах NVv4 поддерживаются только драйверы GPU, опубликованные корпорацией Майкрософт. НЕ УСТАНАВЛИВАЙТЕ драйверы GPU из других источников.

Основные характеристики, сведения о дисках и объеме памяти см. в статье [Графический процессор](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

| OS | Драйвер |
| -------- |------------- |
| Windows 10 EVD — сборка 1903 <br/><br/>Windows 10 — сборка 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Установка драйвера

1. Подключитесь к каждой виртуальной машине серии NVv4 по протоколу удаленного рабочего стола.

2. Скачайте и установите последний выпуск драйвера.

3. Перезагрузите виртуальную машину.

## <a name="verify-driver-installation"></a>Проверка установки драйверов

Установку драйвера можно проверить в диспетчере устройств. В следующем примере показана успешная настройка карты Radeon Instinct MI25 на виртуальной машине Azure серии NVv4.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/device-manager.png)

С помощью средства dxdiag можно проверить свойства GPU, в том числе объем видеопамяти. В следующем примере показаны сведения об одной из двух секций карты Radeon Instinct MI25 на виртуальной машине Azure серии NVv4.
<br />
![Свойства драйвера GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Если вы используете Windows 10 сборки 1903 или более позднюю версию, средство dxdiag не будет отображать сведения на вкладке "Монитор". Используйте действие "Сохранить все сведения" в нижней части окна, чтобы получить сведения о GPU AMD MI25 в выходном файле.

![Свойства драйвера GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


