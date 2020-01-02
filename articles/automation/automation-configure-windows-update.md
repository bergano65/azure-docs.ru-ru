---
title: Настройка параметров Центр обновления Windows для работы с Azure Управление обновлениями
description: В этой статье описываются параметры Центр обновления Windows, настроенные для работы с Управление обновлениями Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850420"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Настройка параметров Центр обновления Windows для Управление обновлениями

Управление обновлениями Azure использует Центр обновления Windows для загрузки и установки обновлений Windows. В результате Управление обновлениями учитывает многие параметры, используемые Центр обновления Windows. Если вы используете параметры для включения обновлений, отличных от Windows, Управление обновлениями также будет управлять этими обновлениями. Если вы хотите включить загрузку обновлений перед развертыванием обновления, развертывание обновлений может быть быстрее, более эффективным и менее вероятно, чем окно обслуживания.

## <a name="pre-download-updates"></a>Предварительная загрузка обновлений

Чтобы настроить автоматическую загрузку обновлений в групповая политика, задайте для [параметра настройка Автоматическое обновление](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) значение **3**. Этот параметр включает загрузку необходимых обновлений в фоновом режиме, но не устанавливает их. Таким образом Управление обновлениями остается в управлении расписаниями, но обновления можно загрузить за пределы периода Управление обновлениями обслуживания. Это поведение предотвращает ошибки "период обслуживания" в Управление обновлениями.

Этот параметр можно также включить, выполнив следующую команду PowerShell в системе, которую требуется настроить для автоматической загрузки обновлений:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Отключить автоматическую установку

По умолчанию на виртуальных машинах Azure включена автоматическая установка обновлений. Это может привести к установке обновлений перед тем, как запланировать их для установки с Управление обновлениями. Это поведение можно отключить, задав для раздела реестра `NoAutoUpdate` значение `1`. В следующем фрагменте кода PowerShell показано, как это сделать:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Настройка параметров перезагрузки

Разделы реестра, перечисленные в разделе [настройка Автоматическое обновление путем изменения реестра](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) и [разделов реестра, используемых для управления перезапуском](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , могут привести к перезагрузке компьютеров, даже если в параметрах **развертывания обновления** не указан параметр **никогда перезагружаться** . Эти разделы реестра должны быть настроены для лучшего соответствия среде.

## <a name="enable-updates-for-other-microsoft-products"></a>Включение обновлений для других продуктов Майкрософт

По умолчанию Центр обновления Windows предоставляет обновления только для ОС Windows. Если включить **обновления для других продуктов Майкрософт при обновлении параметра Windows** , вы также получаете обновления для других продуктов, включая исправления системы безопасности для Microsoft SQL Server и другого программного обеспечения Майкрософт. Этот параметр нельзя настроить с помощью групповой политики. Выполните следующую команду PowerShell для систем, на которых требуется включить другие обновления Майкрософт. Управление обновлениями будет соответствовать этому параметру.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Параметры конфигурации WSUS

Управление обновлениями соответствует параметрам Windows Server Update Services (WSUS). Параметры WSUS, которые можно настроить для работы с Управление обновлениями, перечислены ниже.

### <a name="intranet-microsoft-update-service-location"></a>Расположение службы обновлений Майкрософт в интрасети

Вы можете указать источники для сканирования и загрузки обновлений в разделе [укажите расположение службы центр обновления Майкрософт интрасети](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Дальнейшие действия

После настройки параметров Центр обновления Windows можно запланировать развертывание обновлений, следуя инструкциям в статье [Управление обновлениями и исправлениями для виртуальных машин Azure](automation-tutorial-update-management.md).
