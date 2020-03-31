---
title: Аналитика журналов диагностики виртуального рабочего стола Windows - Azure
description: Как использовать аналитику журналов с функцией диагностики виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127967"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Используйте аналитику журнала для функции диагностики

Windows Virtual Desktop предлагает функцию диагностики, которая позволяет администратору выявлять проблемы с помощью одного интерфейса. Эта функция регистрирует информацию о диагностике всякий раз, когда кто-то присваивает роль Windows Virtual Desktop, использует службу. Каждый журнал содержит информацию о том, какая роль Windows Virtual Desktop была вовлечена в действие, любые сообщения об ошибках, которые появляются во время сеанса, информацию о клиентах и информацию о пользователе. Функция диагностики создает журналы активности как для пользователей, так и для административных действий. Каждый журнал действий подпадает под три основные категории: 

- Действия подписки на канал: когда пользователь пытается подключиться к своей ленте через приложения Microsoft Remote Desktop.
- Действия подключения: когда пользователь пытается подключиться к рабочему столу или удаленному приложению через приложения Microsoft Remote Desktop.
- Действия по управлению: когда администратор выполняет операции управления в системе, такие как создание пулов хоста, назначение пользователей группам приложений и создание ролевых заданий.

Соединения, которые не достигают Windows Virtual Desktop, не отображаются в результатах диагностики, поскольку служба ролевых ролей диагностики сама по себе является частью Windows Virtual Desktop. Проблемы с подключением Windows Virtual Desktop могут возникать, когда пользователь испытывает проблемы с подключением к сети.

## <a name="why-you-should-use-log-analytics"></a>Почему следует использовать аналитику журналов

Мы рекомендуем использовать Log Analytics для анализа диагностических данных в клиенте Azure, который выходит за рамки устранения неполадок для одного пользователя. Как вы можете тянуть в VM счетчики производительности в журнал Analytics у вас есть один инструмент для сбора информации для развертывания.

## <a name="before-you-get-started"></a>Необходимые условия

Прежде чем использовать Log Analytics с функцией диагностики, необходимо [создать рабочее пространство.](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)

После создания рабочего пространства следуйте инструкциям [в подключении компьютеров Windows к Azure Monitor,](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) чтобы получить следующую информацию: 

- Идентификатор рабочей области
- Основной ключ рабочей области

Эта информация понадобится позже в процессе настройки.

## <a name="push-diagnostics-data-to-your-workspace"></a>Нажмите на диагностические данные в рабочее пространство 

Данные диагностики от арендатора Windows Virtual Desktop можно перебросить в журнал Analytics для рабочего пространства. Эту функцию можно настроить сразу же при первом создании арендатора, связав рабочее пространство с арендатором, или позже настроить его с существующим арендатором.

Чтобы связать арендатора с рабочим пространством Log Analytics во время настройки нового арендатора, запустите следующий cmdlet, чтобы войти в Windows Virtual Desktop с учетной записью пользователя TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Если вы собираетесь связать существующего арендатора вместо нового арендатора, запустите этот cmdlet вместо этого: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Вам нужно будет запустить эти cmdlets для каждого арендатора вы хотите, чтобы ссылка на журнал Analytics. 

>[!NOTE]
>Если вы не хотите связывать рабочее пространство Log Analytics при `New-RdsTenant` создании арендатора, запустите cmdlet вместо этого. 

## <a name="cadence-for-sending-diagnostic-events"></a>Каденция для отправки диагностических событий

Диагностические события отправляются в журнал Analytics по завершении.  

## <a name="example-queries"></a>Примеры запросов

Следующие примеры запросов показывают, как функция диагностики генерирует отчет о наиболее частых действиях в вашей системе:

В этом первом примере показаны действия соединения, инициированные пользователями с поддерживаемыми удаленными клиентами рабочего стола:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

В следующем запросе отображается действия управления от админантов на клиентах:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Прекратить отправку данных в журнал Analytics 

Чтобы остановить отправку данных от существующего арендатора в Log Analytics, запустите следующие строки cmdlet и установите пустые строки:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Вам нужно будет запустить этот cmdlet для каждого арендатора вы хотите, чтобы остановить отправку данных от. 

## <a name="next-steps"></a>Дальнейшие действия 

Для анализа распространенных сценариев ошибок, которые функция диагностики может определить для вас, [см.](diagnostics-role-service.md#common-error-scenarios)
