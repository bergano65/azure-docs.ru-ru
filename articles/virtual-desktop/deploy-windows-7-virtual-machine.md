---
title: Развертывание Windows 7 виртуальная машина Windows Виртуальный рабочий стол - Azure
description: Как настроить и развернуть виртуальную машину Windows 7 на Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366689"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Развертывание виртуальной машины Windows 7 в службе "Виртуальный рабочий стол Windows"

Процесс развертывания виртуальной машины Windows 7 (VM) на Windows Virtual Desktop немного отличается от процесса развертывания виртуальных компьютеров, работающих в более поздних версиях Windows. В этом руководстве будет усваивательная система Windows 7.

## <a name="prerequisites"></a>Предварительные требования

Перед тем, как начать, следуйте инструкциям в [создании пула хоста с PowerShell](create-host-pools-powershell.md) для создания пула хоста. После этого следуйте инструкциям в [создании пулов хоста в Azure Marketplace,](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) чтобы назначить одного или нескольких пользователей группе настольных приложений.

## <a name="configure-a-windows-7-virtual-machine"></a>Настройка виртуальной машины Windows 7

После того как вы сделали предпосылки, вы готовы настроить Windows 7 VM для развертывания на Windows Virtual Desktop.

Настройка Windows 7 VM на виртуальном рабочем столе Windows:

1. Вопийте на порталaz и либо ищите изображение windows 7 Enterprise, либо загрузите собственное индивидуальное изображение Windows 7 Enterprise (x64).  
2. Развертывание одного или нескольких виртуальных машин с Windows 7 Enterprise в качестве принимающей операционной системы. Убедитесь, что виртуальные машины позволяют протокол удаленного рабочего стола (RDP) (порт TCP/3389).
3. Подключитесь к хостам Windows 7 Enterprise с помощью RDP и проверьте подлинность с определенными учетными данными при настройке развертывания. 
4. Добавьте учетную запись, используемую при подключении к хостам с RDP, в группу "Удаленный пользователь рабочего стола". Если вы этого не сделаете, вы не сможете подключиться к VM после присоединения к вашему домену Active Directory.
5. Перейдите к обновлению Windows на вашем VM.
6. Установите все обновления Windows в важной категории.
7. Установите все обновления Windows в категории «Опционально» (за исключением пакетов языков). Это устанавливает удаленного рабочего стола Протокол 8.0 обновление ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)), что вам нужно для завершения этих инструкций.
8. Откройте редактор политики локальной группы и перейдите к **компьютерной конфигурации** > **административных шаблонов** > Windows**Компоненты** > **Удаленного настольного обслуживания** > **удаленного рабочего стола Сессия Хост** > **удаленной сессии среды.**
9. Включить политику удаленного рабочего стола 8.0.
10. Присоединяйтесь к этому VM к вашему домену Active Directory.
11. Перезапустите виртуальную машину, запустив следующую команду:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Следуйте инструкциям [здесь,](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) чтобы получить регистрационный жетон.
13. [Скачать Windows Виртуальный настольный агент для Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Скачать Windows Виртуальный настольный агент менеджер для Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Откройте установщик виртуального настольного агента Windows и следуйте инструкциям. По запросу, дайте ключ регистрации, который вы создали в шаге 12.
16. Откройте установщик виртуального рабочего стола Windows и следуйте инструкциям.
17. Дополнительно заблокируйте порт TCP/3389, чтобы удалить прямой доступ к протоколу удаленного рабочего стола к VM.

## <a name="next-steps"></a>Дальнейшие действия

Развертывание виртуального рабочего стола Windows готово к использованию. [Загрузите последнюю версию windows Virtual Desktop клиента,](https://aka.ms/wvd/clients/windows) чтобы начать работу.

Для списка известных проблем и инструкций по устранению неполадок для Windows 7 на Windows Virtual Desktop, смотрите нашу статью устранения неполадок в [Troubleshoot Windows 7 виртуальных машин в Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
