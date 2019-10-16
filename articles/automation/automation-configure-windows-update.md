---
title: Настройка параметров Центр обновления Windows для работы с Azure Управление обновлениями
description: В этой статье описываются параметры Центр обновления Windows, настроенные для работы с Управление обновлениями
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377568"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Настройка параметров Центр обновления Windows для Управление обновлениями

Решение "Управление обновлениями" основано на загрузке и установке обновлений Windows через Центр обновления Windows. Это означает, что мы учитываем многие параметры, настраиваемые для Центра обновления Windows. Если вы настроите параметры для установки обновлений, отличных от Windows, решение "Управление обновлениями" также будет обрабатывать такие обновления. Если вы настроите скачивание обновлений перед каждым развертыванием обновлений, развертывание обновлений будет проходить быстрее и с меньшей вероятностью выйдет за рамки периода обслуживания.

## <a name="pre-download-updates"></a>Предварительное скачивание обновлений

Чтобы настроить автоматическое скачивание обновлений в групповой политике, задайте для параметра [Настройка автоматического обновления](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) значение **3**. В этом режиме обновления скачиваются в фоновом режиме, но не устанавливаются. Это позволяет решению "Управление обновлениями" контролировать расписание установки, но скачивать данные вне периода обслуживания, настроенного для решения "Управление обновлениями". Это снизит вероятность ошибок **превышения длительности периода обслуживания** в решении "Управление обновлениями".

Также этот параметр можно задать с помощью PowerShell, выполнив следующие команды в системе, для которой нужно настроить автоматическое скачивание обновлений.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Отключить автоматическую установку

По умолчанию на виртуальных машинах Azure автоматическая установка обновлений включена. Это может привести к установке обновлений перед тем, как запланировать их установку с помощью Управление обновлениями. Это поведение можно отключить, задав для раздела реестра `NoAutoUpdate` значение `1`. В следующем фрагменте кода PowerShell показан один из способов.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Настройка параметров перезагрузки

Разделы реестра, перечисленные в разделе [настройка Автоматическое обновление путем изменения реестра](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) и [разделов реестра, используемых для управления перезапуском](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , могут привести к перезагрузке компьютеров, даже если в параметрах развертывания обновления не указан параметр **никогда перезагружаться** . . Необходимо настроить эти разделы реестра для конкретной среды.

## <a name="enable-updates-for-other-microsoft-products"></a>Включение обновлений для других продуктов Майкрософт

По умолчанию Центр обновления Windows предоставляет обновления только для ОС Windows. Если включить **обновления для других продуктов Майкрософт при обновлении Windows**, вы получите обновления для других продуктов, включая исправления системы безопасности для SQL Server или другого программного обеспечения, предназначенного для первого производителя. Этот параметр нельзя настроить с помощью групповой политики. Выполните следующие команды PowerShell в системах, для которых вы хотите включить установку наших собственных исправлений, и решение "Управление обновлениями" будет учитывать это параметр.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Параметры конфигурации WSUS

**Управление обновлениями** учитывает параметры конфигурации WSUS. Список параметров WSUS, которые можно настроить для работы с Управление обновлениями, приведен ниже.

### <a name="intranet-microsoft-update-service-location"></a>Расположение службы обновлений Майкрософт в интрасети

Вы можете указать источники для сканирования и загрузки обновлений в [расположении службы центр обновления Майкрософт интрасети](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Следующие шаги

После настройки параметров Центр обновления Windows можно запланировать развертывание обновлений, следуя инструкциям в разделе [Управление обновлениями и исправлениями для виртуальных машин Azure](automation-tutorial-update-management.md) .