---
title: Устранение неполадок подключения к службе виртуальный рабочий стол Windows — Azure
description: Устранение проблем при настройке клиентских подключений в среде клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: 81ba2b3b5834e06922f694d6eb5338465bfb0ab7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975468"
---
# <a name="windows-virtual-desktop-service-connections"></a>Подключения к службе виртуальных рабочих столов Windows

Используйте эту статью для устранения проблем с подключением клиентов к виртуальным рабочим столам Windows.

## <a name="provide-feedback"></a>Отправить отзыв

Вы можете придать нам отзыв и обсудить службу виртуальных рабочих столов Windows с группой разработчиков и другими активными членами сообщества, входящими в состав [сообщества виртуальных рабочих столов Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но ничего не отображается (канал отсутствует)

Пользователь может запускать удаленный рабочий стол клиентов и выполнять проверку подлинности, однако пользователь не увидит значки в веб-канале обнаружения.

Убедитесь, что пользователь, сообщающий о проблемах, назначен группам приложений с помощью следующей командной строки:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Убедитесь, что пользователь входит в систему с использованием правильных учетных данных.

Если используется веб-клиент, убедитесь в отсутствии проблем с кэшированными учетными данными.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Виртуальные машины Windows 10 Корпоративная с несколькими сеансами не отвечают

Если виртуальная машина не отвечает и вы не можете получить к ней доступ через RDP, вам потребуется устранить неполадки с помощью функции диагностики, проверив состояние узла.

Чтобы проверить состояние узла, выполните следующий командлет:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Если состояние узла — `NoHeartBeat`, это означает, что виртуальная машина не отвечает и агент не может взаимодействовать со службой виртуальных рабочих столов Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

Существует несколько вещей, которые можно выполнить для исправления состояния неподтверждения.

### <a name="update-fslogix"></a>Обновление Фслогикс

Если ваш Фслогикс не является актуальным, особенно если он имеет версию 2.9.7205.27375 файла фрксдрввт. sys, это может привести к взаимоблокировке. Не забудьте [Обновить фслогикс до последней версии](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Отключить Бгтаскрегистратионмаинтенанцетаск

Если обновление Фслогикс не работает, это может быть вызвано тем, что компонент Бисрв исчерпал системные ресурсы в ходе еженедельного обслуживания. Временно отключите задачу обслуживания, отключив Бгтаскрегистратионмаинтенанцетаск с помощью одного из следующих двух методов:

- Перейдите в меню "Пуск" и выполните поиск по **планировщик задач**. Перейдите к **библиотеке планировщик задач** > **Microsoft** > **Windows** > **брокеринфраструктуре**. Найдите задачу с именем **бгтаскрегистратионмаинтенанцетаск**. Если он найден, щелкните его правой кнопкой мыши и выберите в раскрывающемся меню пункт **Отключить** .
- Откройте меню командной строки от имени администратора и выполните следующую команду:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с виртуальным рабочим столом Windows и сведениями о эскалации см. в разделе [Обзор устранения неполадок, обратная связь и поддержка](troubleshoot-set-up-overview.md).
- Сведения об устранении неполадок при создании клиента и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок при использовании PowerShell с виртуальным рабочим столом Windows см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Руководство по устранению неполадок см. в разделе [учебник. Устранение неполадок диспетчер ресурсов развертываний шаблонов](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
