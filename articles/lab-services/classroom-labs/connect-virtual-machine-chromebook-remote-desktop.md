---
title: Подключение к виртуальной машине Служб лаборатории Azure с Chromebook | Документация Майкрософт
description: Узнайте, как подключиться с компьютера Chromebook к виртуальной машине в Службах лаборатории Azure.
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
ms.date: 05/11/2020
ms.author: nicolela
ms.openlocfilehash: 581ba32f536728fec88ddf3120637f9347e2e925
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704379"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Подключение к виртуальной машине с помощью протокола удаленного рабочего стола на Chromebook
В этом разделе показано, как учащийся может подключиться к виртуальной машине задания с компьютера Chromebook с помощью RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Установка удаленного рабочего стола (Майкрософт) на компьютере Chromebook
1. Откройте App Store на компьютере Chromebook и выполните поиск по запросу **удаленный рабочий стол (Майкрософт)** .

    ![Удаленный рабочий стол (Майкрософт)](../media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Установите последнюю версию удаленного рабочего стола (Майкрософт). 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Доступ к виртуальной машине с компьютера Chromebook с помощью RDP
1. Откройте файл **RDP**, скачанный на компьютере с установленным **удаленным рабочим столом (Майкрософт)** . При этом должно начаться подключение к виртуальной машине. 

    ![Подключение к виртуальной машине](../media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. При появлении запроса введите свой пароль.
    ![Подключение к виртуальной машине](../media/how-to-use-classroom-lab/password-chromebook.png)


1. Выберите **Continue** (Продолжить), если появится следующее предупреждение. 

    ![Предупреждение о проблеме с сертификатом](../media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Вы должны увидеть рабочий стол виртуальной машины, к которой вы подключаетесь.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о [подключении к виртуальным машинам Linux](how-to-use-remote-desktop-linux-student.md).


