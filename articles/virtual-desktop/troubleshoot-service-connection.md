---
title: Устранение неполадок подключения к службе виртуальный рабочий стол Windows — Azure
description: Устранение проблем при настройке клиентских подключений в среде клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612645"
---
# <a name="windows-virtual-desktop-service-connections"></a>Подключения к службе виртуальных рабочих столов Windows

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Используйте эту статью для устранения проблем с подключением клиентов к виртуальным рабочим столам Windows.

## <a name="provide-feedback"></a>Предоставление отзыва

Вы можете придать нам отзыв и обсудить службу виртуальных рабочих столов Windows с группой разработчиков и другими активными членами сообщества, входящими в состав [сообщества виртуальных рабочих столов Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но ничего не отображается (канал отсутствует)

Пользователь может запускать удаленный рабочий стол клиентов и выполнять проверку подлинности, однако пользователь не увидит значки в веб-канале обнаружения.

Убедитесь, что пользователь, сообщающий о проблемах, назначен группам приложений с помощью следующей командной строки:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Убедитесь, что пользователь входит в систему с использованием правильных учетных данных.

Если используется веб-клиент, убедитесь в отсутствии проблем с кэшированными учетными данными.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Виртуальные машины Windows 10 Корпоративная с несколькими сеансами не отвечают

Если виртуальная машина не отвечает и вы не можете получить к ней доступ через RDP, вам потребуется устранить неполадки с помощью функции диагностики, проверив состояние узла.

Чтобы проверить состояние узла, выполните следующий командлет:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Если узел имеет `NoHeartBeat`состояние, то это означает, что виртуальная машина не отвечает и агент не может взаимодействовать со службой виртуальных рабочих столов Windows.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Существует несколько вещей, которые можно выполнить для исправления состояния неподтверждения.

### <a name="update-fslogix"></a>Обновление Фслогикс

Если ваш Фслогикс не является актуальным, особенно если он имеет версию 2.9.7205.27375 файла фрксдрввт. sys, это может привести к взаимоблокировке. Не забудьте [Обновить фслогикс до последней версии](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с виртуальным рабочим столом Windows и сведениями о эскалации см. в разделе [Обзор устранения неполадок, обратная связь и поддержка](troubleshoot-set-up-overview.md).
- Сведения об устранении неполадок при создании среды виртуальных рабочих столов Windows и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула среды и узла](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок при использовании PowerShell с виртуальным рабочим столом Windows см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Руководство по устранению неполадок см. в разделе [учебник. Устранение неполадок диспетчер ресурсов развертываний шаблонов](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
