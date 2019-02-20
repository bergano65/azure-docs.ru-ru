---
title: Сбор пользовательских данных JSON в Azure Monitor | Документация Майкрософт
description: С помощью агента Log Analytics для Linux можно собирать данные из пользовательских источников данных JSON в службу Azure Monitor.  В качестве такого пользовательского источника данных может использоваться простой сценарий, возвращающий результат в формате JSON, например curl, или один из более чем 300 подключаемых модулей FluentD. В этой статье описано, как настроить такой сбор данных.
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
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 8b03d6838e9d942da766e0c7aa4c2c2e161a6b14
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990127"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Сбор данных из пользовательских источников данных JSON с помощью агента Log Analytics для Linux в службу Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

С помощью агента Log Analytics для Linux можно собирать данные из пользовательских источников данных JSON в службу [Azure Monitor](data-collection.md).  В качестве такого пользовательского источника данных может использоваться простой сценарий, возвращающий результат в формате JSON, например [curl](https://curl.haxx.se/), или один из более чем [300 подключаемых модулей FluentD](http://www.fluentd.org/plugins/all). В этой статье описано, как настроить такой сбор данных.


> [!NOTE]
> Для работы с пользовательскими источниками данных требуется агент Log Analytics для Linux версии 1.1.0-217 или более поздней.

## <a name="configuration"></a>Параметр Configuration

### <a name="configure-input-plugin"></a>Настройка входного подключаемого модуля

Для сбора данных JSON в Azure Monitor добавьте `oms.api.` в начало тега FluentD во входном подключаемом модуле.

Например, ниже приведен отдельный файл конфигурации `exec-json.conf` в `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  В нем используется подключаемый модуль FluentD `exec` для запуска команды curl каждые 30 секунд.  Выходные данные этой команды собираются с помощью выходного подключаемого модуля JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Для файла конфигурации, добавленного в разделе `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`, необходимо изменить владельца, выполнив следующую команду.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Настройка выходного подключаемого модуля 
Добавьте следующую конфигурацию выходного подключаемого модуля в раздел `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` основной конфигурации или в отдельный файл конфигурации в каталоге `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Перезапуск агента Log Analytics для Linux
Перезапустите службу агента Log Analytics для Linux, выполнив следующую команду.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Выходные данные
Данные, собираемые в Azure Monitor, будут иметь тип записи `<FLUENTD_TAG>_CL`.

Например, пользовательский тег `tag oms.api.tomcat` в Azure Monitor будет иметь тип записи `tomcat_CL`.  Для получения всех записей этого типа выполните следующий запрос журнала.

    Type=tomcat_CL

Поддерживаются вложенные источники данных JSON, но они индексируются не на основе родительского поля. Например, в результате выполнения запроса журнала `tag_s : "[{ "a":"1", "b":"2" }]` будут возвращены следующие данные JSON.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о [запросах журнала](../log-query/log-query-overview.md), которые можно применять для анализа данных, собираемых из источников данных и решений. 