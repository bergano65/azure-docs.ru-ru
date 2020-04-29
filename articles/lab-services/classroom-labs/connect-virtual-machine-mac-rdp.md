---
title: Подключение к виртуальной машине служб лаборатории Azure из Mac | Документация Майкрософт
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503092"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Подключение к виртуальной машине с помощью RDP на компьютере Mac
В этом разделе показано, как учащийся может подключиться к виртуальной машине лаборатории из компьютера Mac с помощью RDP.

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

    ![Виртуальная машина](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Дальнейшие шаги
Сведения о подключении к виртуальным машинам Linux с помощью RDP см. в статье [Использование удаленного рабочего стола для виртуальных машин Linux](how-to-use-remote-desktop-linux-student.md) .


