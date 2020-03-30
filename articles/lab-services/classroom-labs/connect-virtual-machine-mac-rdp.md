---
title: Как подключиться к VM-сервису Azure Lab от Mac Документы Майкрософт
description: Эта статья
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503092"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Подключение к виртуальной машине с помощью RDP на компьютере Mac
В этом разделе показано, как студент может подключиться к классной лаборатории VM с Mac с помощью RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Установка удаленного рабочего стола (Майкрософт) на компьютере Mac
1. Откройте App Store на компьютере Mac и выполните поиск по запросу **удаленный рабочий стол (Майкрософт)**.

    ![Удаленный рабочий стол (Майкрософт)](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Установите последнюю версию удаленного рабочего стола (Майкрософт). 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Доступ к виртуальной машине с компьютера Mac с помощью RDP
1. Откройте файл **RDP**, скачанный на компьютере с установленным **удаленным рабочим столом (Майкрософт)**. При этом должно начаться подключение к виртуальной машине. 

    ![Подключение к виртуальной машине](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Выберите **Continue** (Продолжить), если появится следующее предупреждение. 

    ![Предупреждение о проблеме с сертификатом](../media/how-to-use-classroom-lab/certificate-error.png)
1. Должна отобразиться виртуальная машина. 

    > [!NOTE]
    > Ниже приведен пример для виртуальной машины Linux CentOS. 

    ![ВМ](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать, как подключиться к Linux VMs с помощью RDP, [см.](how-to-use-remote-desktop-linux-student.md)


