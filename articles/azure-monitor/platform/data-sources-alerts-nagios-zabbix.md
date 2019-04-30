---
title: Сбор оповещений Nagios и Zabbix в Azure Monitor | Документация Майкрософт
description: Nagios и Zabbix — средства мониторинга с открытым исходным кодом. Оповещения от этих инструментов мониторинга можно собирать в Azure Monitor для анализа вместе с оповещениями из других источников.  В этой статье описано, как настроить агент Log Analytics для Linux для сбора оповещений из этих систем.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759985"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Сбор оповещений Nagios и Zabbix с помощью агента Log Analytics для Linux в службу Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) и [Zabbix](https://www.zabbix.com/) — средства мониторинга с открытым исходным кодом. Оповещения этих инструментов мониторинга можно собирать в Azure Monitor для анализа вместе с данными журнала из других источников.  В этой статье описано, как настроить агент Log Analytics для Linux для сбора оповещений из этих систем.


> [!NOTE]
> [Созданные Azure Monitor оповещения](alerts-overview.md) хранятся отдельно от данных журнала и недоступны запросам по журналу.

 
## <a name="prerequisites"></a>Технические условия
Агент Log Analytics для Linux поддерживает сбор оповещений из Nagios до версии 4.2.x и Zabbix до версии 2.x.

## <a name="configure-alert-collection"></a>Настройка сбора оповещений

### <a name="configuring-nagios-alert-collection"></a>Настройка сбора оповещений Nagios
Для сбора оповещений выполните следующие действия на сервере Nagios.

1. Предоставьте пользователю **omsagent** разрешение на чтение для файла журнала Nagios `/var/log/nagios/nagios.log`. Если файл nagios.log принадлежит группе `nagios`, можно добавить пользователя **omsagent** в группу **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Измените файл конфигурации на `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Для этого введите следующие записи в незакомментированном виде:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Перезапустите управляющую программу omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Настройка сбора оповещений Zabbix
Для сбора оповещений сервера Zabbix необходимо указать имя пользователя и пароль в формате *открытого текста*.  Хотя это и не оптимально, рекомендуется создать пользователя Zabbix с разрешениями только для чтения для перехвата соответствующих предупреждений.

Для сбора оповещений выполните следующие действия на сервере Nagios.

1. Измените файл конфигурации на `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Убедитесь, что в файле есть следующие параметры и что они не закомментированы.  Измените имя пользователя и пароль на соответствующие значения для вашей среды Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Перезапустите управляющую программу omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Записи оповещений
Записи оповещений из Nagios и Zabbix можно получить с помощью [запросов по журналам](../log-query/log-query-overview.md) в Azure Monitor.

### <a name="nagios-alert-records"></a>Записи оповещений Nagios

Для записей оповещений Nagios параметр **Type** (тип) имеет значение **Alert** (оповещение), а параметр **SourceSystem** (источник) — значение **Nagios**.  У них есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| `Type` |*Предупреждение* |
| `SourceSystem` |*Nagios* |
| `AlertName` |Имя оповещения. |
| `AlertDescription` | Описание оповещения. |
| `AlertState` | Состояние службы или узла.<br><br>OК<br>ПРЕДУПРЕЖДЕНИЕ<br>РАБОТАЕТ<br>СБОЙ |
| `HostName` | Имя узла, который создал оповещение. |
| `PriorityNumber` | Приоритет оповещения. |
| `StateType` | Тип состояния оповещения.<br><br>SOFT — проблема, которая не проверялась повторно.<br>HARD — проблема, которая проверялась повторно заданное число раз.  |
| `TimeGenerated` |Дата и время создания оповещения. |


### <a name="zabbix-alert-records"></a>Записи оповещений Zabbix
Для записей оповещений Nagios параметр **Type** (тип) имеет значение **Alert** (оповещение), а параметр **SourceSystem** (источник) — значение **Zabbix**.  У них есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| `Type` |*Предупреждение* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | Имя оповещения. |
| `AlertPriority` | Серьезность оповещения.<br><br>не определен<br>информационный.<br>предупреждение<br>average<br>высокий<br>очень высокий  |
| `AlertState` | Состояние оповещения.<br><br>0 — актуальное состояние.<br>1 — состояние неизвестно.  |
| `AlertTypeNumber` | Указывает, может ли оповещение создавать несколько событий, связанных с проблемой.<br><br>0 — актуальное состояние.<br>1 — состояние неизвестно.    |
| `Comments` | Дополнительные комментарии для оповещения. |
| `HostName` | Имя узла, который создал оповещение. |
| `PriorityNumber` | Значение, указывающее уровень серьезности оповещения.<br><br>0 — не определен<br>1 — информация<br>2 — предупреждение<br>3 — средний<br>4 — высокий<br>5 — очень высокий |
| `TimeGenerated` |Дата и время создания оповещения. |
| `TimeLastModified` |Дата и время последнего изменения состояния оповещения. |


## <a name="next-steps"></a>Дальнейшие действия
* Подробнее об [оповещениях](alerts-overview.md) в Azure Monitor.
* Узнайте больше о [запросах журнала](../log-query/log-query-overview.md), которые можно применять для анализа данных, собираемых из источников данных и решений. 
