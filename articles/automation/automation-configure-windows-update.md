---
title: Настройка настроек обновления Windows для работы с управлением обновлениями Azure
description: В этой статье описаны настройки обновления Windows, которые вы настраиваете для работы с управлением обновлениями Azure.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279030"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Настройка настроек обновления Windows для управления обновлением

Управление обновлениями Azure полагается на [клиент обновления Windows](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) для загрузки и установки обновлений Windows. При подключении к службам обновления Windows (WSUS) или Windows Update существуют определенные параметры, которые используются клиентом windows Update. Многие из этих параметров можно управлять с помощью:

- Редактор локальных групповых политик
- Групповая политика
- PowerShell
- Непосредственное редактирование реестра

Управление обновлениями уважает многие параметры, указанные для управления клиентом обновления Windows. Если вы используете настройки для включения обновлений, не связанных с Windows, управление обновлениями также будет управлять этими обновлениями. Если требуется включить загрузку обновлений до развертывания обновления, развертывание обновлений может быть более быстрым, более эффективным и менее вероятным, чтобы превысить окно обслуживания.

## <a name="pre-download-updates"></a>Обновления предварительной загрузки

Для настройки автоматической загрузки обновлений, но не устанавливайте их автоматически, можно использовать групповую политику для настройки [настройки автоматических обновлений](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) до **3.** Эта настройка позволяет загружать необходимые обновления в фоновом режиме и уведомляет вас о том, что обновления готовы к установке. Таким образом, управление обновлениями по-прежнему контролирует графики, но обновления можно загрузить за окном обслуживания управления обновлением. Такое поведение предотвращает **превышение окна обслуживания** ошибками в управлении обновлением.

Эту настройку можно включить с помощью PowerShell, запустив следующую команду:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Настройка настроек перезагрузки

Ключи реестра, перечисленные в [настройке автоматических обновлений путем редактирования](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) ключей реестра и [реестра, используемых для управления перезагрузкой,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) могут привести к перезагрузке машин, даже если вы укажете, что в настройках **развертывания обновления** не **указаны.** Назначьте эти ключи реестра, чтобы наилучшим образом соответствовать вашей среде.

## <a name="enable-updates-for-other-microsoft-products"></a>Включение обновлений для других продуктов Майкрософт

По умолчанию клиент Windows Update настроен на обновление только для Windows. Если вы включите **обновления для других продуктов Майкрософт при обновлении** настройки Windows, вы также получите обновления для других продуктов, в том числе патчей безопасности для Microsoft S'L Server и другого программного обеспечения Майкрософт. Эта опция может быть настроена, если вы скачали и скопировали последние [файлы административного шаблона,](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) доступные для Windows 2016 и выше.

Если вы работаете под управлением Windows Server 2012 R2, этот параметр не может быть настроен по групповой политике. Выполнить следующую команду PowerShell на этих машинах. Управление обновлением соответствует этой настройке.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Настройки конфигурации WSUS

Управление обновлением поддерживает настройки WSUS. Приведенные ниже настройки WSUS, которые можно настроить для работы с управлением обновлениями.

### <a name="intranet-microsoft-update-service-location"></a>Место службы обновления Интрасети Майкрософт

Вы можете указать источники для сканирования и загрузки обновлений в соответствии с [информацией о местонахождении службы обновления Microsoft.](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location) По умолчанию клиент Windows Update настроен на загрузку обновлений из Windows Update. Когда вы указываете сервер WSUS в качестве источника для ваших машин, если обновления не утверждены в WSUS, развертывание обновления завершается неудачей. 

Чтобы ограничить машины только этой внутренней службой обновления, настройте [не подключайтесь к каким-либо местоположениям Windows Update в Интернете.](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations) 

## <a name="next-steps"></a>Дальнейшие действия

После настройки настроек обновления Windows можно запланировать развертывание обновлений, следуя инструкциям в [обновлении и патчах управления для вашегои M-сообщений Azure.](automation-tutorial-update-management.md)
