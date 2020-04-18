---
title: Мониторинг Java-приложений в любом месте - Azure Monitor Application Insight
description: Мониторинг производительности приложений без кода для Java-приложений, работающих в любой среде без инструментирования приложения. Найдите первопричину проблем d с помощью распределенной карты трассировки и приложения.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641892"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Параметры конфигурации - автономный агент Java для Azure Monitor Application Insight Insight



## <a name="connection-string-and-role-name"></a>Строка соединения и имя роли

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Требуется строка подключения, и имя роли важно в любое время, когда вы отправляете данные из разных приложений на один и тот же ресурс Application Insights.

Вы найдете более подробную информацию и дополнительные параметры конфигурации ниже для получения более подробной информации.

## <a name="configuration-file-path"></a>Путь файла конфигурации

По умолчанию Application Insights Java 3.0 Preview `ApplicationInsights.json`предполагает, что файл конфигурации будет `applicationinsights-agent-3.0.0-PREVIEW.jar`назван и будет находиться в том же каталоге, что и .

Вы можете указать свой собственный путь файла конфигурации, используя либо

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`переменной окружающей среды, или
* `applicationinsights.configurationFile`Свойство Java-системы

Если вы укажете относительный путь, он `applicationinsights-agent-3.0.0-PREVIEW.jar` будет решен относительно каталога, где находится.

## <a name="connection-string"></a>Строка подключения

Это обязательный шаг. Строку подключения можно найти в ресурсе Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Строка подключения к приложениям Insights":::

Вы также можете установить строку `APPLICATIONINSIGHTS_CONNECTION_STRING`соединения с помощью переменной среды.

## <a name="cloud-role-name"></a>Имя роли облака

Имя роли облака используется для обозначения компонента на карте приложения.

Если вы хотите установить имя роли облака:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Если имя роли облака не установлено, имя ресурса Application Insights будет использоваться для обозначения компонента на карте приложения.

Вы также можете установить имя роли `APPLICATIONINSIGHTS_ROLE_NAME`облака с помощью переменной среды.

## <a name="cloud-role-instance"></a>Экземпляр облачной роли

Экземпляр роли облака по умолчанию влияет на имя машины.

Если вы хотите настроить экземпляр роли облака на что-то другое, а не на имя машины:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Можно также установить экземпляр роли облака `APPLICATIONINSIGHTS_ROLE_INSTANCE`с помощью переменной среды.

## <a name="application-log-capture"></a>Захват журнала приложения

Приложение Insights Java 3.0 Preview автоматически фиксирует журнал приложений через Log4j, Logback и java.util.logging.

По умолчанию он будет `WARN` захватывать все журналы, выполняемые на уровне или выше.

Если вы хотите изменить этот порог:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Это допустимые `threshold` значения, которые можно `ApplicationInsights.json` указать в файле, и то, как они соответствуют уровням регистрации в различных средах регистрации:

| `threshold`  | Log4j  | Logback | ИЮЛ     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| Смертельным        | Смертельным  | ошибка   | SEVERE  |
| ОШИБКА/ТЯЖЕЛАЯ | ошибка  | ошибка   | SEVERE  |
| ПРЕДУПРЕЖДЕНИЕ/ПРЕДУПРЕЖДЕНИЕ | Предупредить   | Предупредить    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| DEBUG/FINE   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| ТРАССА/ФОМЕЙН | трассировка  | трассировка   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Метрики JMX

Если у вас есть некоторые метрики JMX, которые вы заинтересованы в захвате:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Micrometer

По умолчанию, если приложение использует [Micrometer,](https://micrometer.io)Application Insights 3.0 (начиная с Preview.2) теперь добавляет себя в глобальный реестр Micrometer и фиксирует метрики Micrometer.

Если вы хотите отключить эту функцию:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Пульс

По умолчанию Application Insights Java 3.0 Preview отправляет метрику сердцебиения один раз в 15 минут. Если вы используете метрику сердцебиения для запуска оповещений, вы можете увеличить частоту этого сердцебиения:

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> Вы не можете уменьшить частоту этого сердцебиения, так как данные сердцебиения также используются для отслеживания использования Application Insights.

## <a name="sampling"></a>Выборка

Выборка полезна, если вам нужно снизить затраты.
Выборка выполняется в качестве функции на идентификаторе операции (также известном как идентификатор трассировки), так что один и тот же идентификатор операции всегда будет приводить к тому же решению выборки. Это гарантирует, что вы не получите части распределенной транзакции, отобранные в то время как другие ее части будут отобраны.

Например, если вы установите выборку до 10%, вы увидите только 10% транзакций, но каждый из этих 10% будет иметь полную сквозную информацию о транзакциях.

Вот пример, как установить выборку до **10% от всех транзакций** - пожалуйста, убедитесь, что вы установите скорость выборки, которая является правильной для вашего случая использования:

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP Прокси

Если ваше приложение находится за брандмауэром и не может подключаться непосредственно к Application Insights (см. [IP-адреса, используемые Application Insights),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)вы можете настроить Application Insights Java 3.0 Предварительный просмотр для использования прокси HTTP:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Самодиагностика

"Самодиагностика" относится к внутренней регистрации из Application Insights Java 3.0 Предварительный просмотр.

Это может быть полезно для выявления и диагностики проблем с самим applications.

По умолчанию он регистрируется `warn`для консоли с уровнем, соответствующим этой конфигурации:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

Действительные `OFF` `ERROR`уровни, `INFO` `DEBUG`, `WARN` `TRACE`, , и .

Если вы хотите войти в файл вместо входа в консоль:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

При использовании журнала файлов, `maxSizeMB`как только файл хитов, он будет опрокидывания, сохраняя только совсем недавно завершенный файл журнала в дополнение к текущему файлу журнала.
