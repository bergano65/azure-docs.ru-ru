---
title: Настройка параметров Центр обновления Windows для работы с Azure Управление обновлениями
description: В этой статье описываются параметры Центр обновления Windows, настроенные для работы с Управление обновлениями Azure.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373250"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Настройка параметров Центр обновления Windows для Управление обновлениями

Управление обновлениями Azure использует [клиент центр обновления Windows](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) для загрузки и установки обновлений Windows. Существуют определенные параметры, используемые клиентом Центр обновления Windows при подключении к Windows Server Update Services (WSUS) или Центр обновления Windows. Многие из этих параметров можно управлять с помощью:

- редактор локальных групповых политик
- Групповая политика
- PowerShell
- Непосредственное редактирование реестра

Управление обновлениями учитывает многие параметры, заданные для управления клиентом Центр обновления Windows. Если вы используете параметры для включения обновлений, отличных от Windows, Управление обновлениями также будет управлять этими обновлениями. Если вы хотите включить загрузку обновлений перед развертыванием обновления, развертывание обновлений может быть быстрее, более эффективным и менее вероятно, чем окно обслуживания.

## <a name="pre-download-updates"></a>Предварительная загрузка обновлений

Чтобы настроить автоматическую загрузку обновлений, но не устанавливать их автоматически, можно использовать групповая политика, чтобы задать для [параметра настройки автоматическое обновление](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) значение **3**. Этот параметр включает загрузку необходимых обновлений в фоновом режиме и уведомляет о том, что обновления готовы к установке. Таким образом Управление обновлениями остается в управлении расписаниями, но обновления можно загрузить за пределы периода Управление обновлениями обслуживания. Такое поведение предотвращает **превышение количества ошибок периода обслуживания** в Управление обновлениями.

Этот параметр можно включить с помощью PowerShell, выполнив следующую команду:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Настройка параметров перезагрузки

Разделы реестра, перечисленные в разделе [настройка Автоматическое обновление путем изменения реестра](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) и [разделов реестра, используемых для управления перезапуском](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , могут привести к перезагрузке компьютеров, даже если в параметрах **развертывания обновления** не указан параметр **никогда перезагружаться** . Настройте эти разделы реестра, чтобы они лучше соответствовали вашей среде.

## <a name="enable-updates-for-other-microsoft-products"></a>Включение обновлений для других продуктов Майкрософт

По умолчанию Центр обновления Windows клиент настроен для предоставления обновлений только для Windows. Если включить **обновления для других продуктов Майкрософт при обновлении параметра Windows** , вы также получаете обновления для других продуктов, включая исправления системы безопасности для Microsoft SQL Server и другого программного обеспечения Майкрософт. Этот параметр можно настроить, если вы скачали и скопировали последние [административные файлы шаблонов](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) , доступные для Windows 2016 и более поздних версий.

Если вы используете Windows Server 2012 R2, этот параметр нельзя настроить с помощью групповая политика. Выполните следующую команду PowerShell на этих компьютерах. Управление обновлениями соответствует этому параметру.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Параметры конфигурации WSUS

Управление обновлениями поддерживает параметры WSUS. Параметры WSUS, которые можно настроить для работы с Управление обновлениями, перечислены ниже.

### <a name="intranet-microsoft-update-service-location"></a>Расположение службы обновлений Майкрософт в интрасети

Вы можете указать источники для сканирования и загрузки обновлений в разделе [укажите расположение службы центр обновления Майкрософт интрасети](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). По умолчанию Центр обновления Windows клиент настроен для загрузки обновлений из Центр обновления Windows. При указании сервера WSUS в качестве источника для компьютеров, если обновления не утверждены в WSUS, происходит сбой развертывания обновления. 

Чтобы ограничить виртуальные машины только внутренней службой обновления, настройте не [подключаться к центр обновления Windows расположениям в Интернете](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Следующие шаги

После настройки параметров Центр обновления Windows можно запланировать развертывание обновлений, следуя инструкциям в статье [Управление обновлениями и исправлениями для виртуальных машин Azure](automation-tutorial-update-management.md).
