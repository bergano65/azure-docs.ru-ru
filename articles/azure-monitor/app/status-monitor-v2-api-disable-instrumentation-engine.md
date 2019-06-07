---
title: 'Монитор состояния v2 Справочник по API Azure: Отключить ядра инструментирования | Документация Майкрософт'
description: Справочник по API v2 монитор состояния. Disable-InstrumentationEngine. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b5ff218ce7bc7593d38f496b22d2a03402a00cdc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514426"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>Состояние монитора v2 API: Disable-InstrumentationEngine (v0.2.1-альфа-версия)

В этой статье описывается командлет, который является членом [модуль Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Описание
Отключает ядро инструментирования, удаляя некоторые разделы реестра.
Перезапустите службы IIS, чтобы изменения вступили в силу.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Параметры 

### <a name="-verbose"></a>-Verbose
**Общий параметр.** Этот параметр можно используйте для вывода подробных журналов.

## <a name="output"></a>Выход


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Пример выходных данных успешно отключать подсистему инструментирования

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Дальнейшие действия

 Новые возможности в версии 2 монитор состояния:
 - Используйте наше руководство по [Устранение](status-monitor-v2-troubleshoot.md) v2 монитор состояния.
