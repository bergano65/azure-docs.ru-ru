---
title: Мониторинг приложений Java в любом месте Azure Monitor Application Insights
description: Наблюдение за производительностью приложений Java, работающих в любой среде, без инструментирования приложения. Найдите основную причину проблем d с помощью распределенной трассировки и схемы приложения.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537663"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Параметры конфигурации — автономный агент Java для Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Строка подключения и имя роли

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

Строка подключения является обязательной, и имя роли важно каждый раз при отправке данных из разных приложений в один и тот же ресурс Application Insights.

Дополнительные сведения и дополнительные параметры конфигурации можно найти ниже.

## <a name="configuration-file-path"></a>Путь к файлу конфигурации

По умолчанию Application Insights Java 3,0 Preview ждет, что файл конфигурации имеет имя `ApplicationInsights.json` и находится в том же каталоге, что и `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Можно указать собственный путь к файлу конфигурации, используя либо

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` переменная среды или
* `applicationinsights.configurationFile` Системное свойство Java

Если указан относительный путь, он будет разрешаться относительно каталога, в котором находится `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

## <a name="connection-string"></a>Строка подключения

Это обязательный шаг. Строку подключения можно найти в ресурсе Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Строка подключения Application Insights":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Можно также задать строку подключения с помощью переменной среды `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Если не задать строку подключения, агент Java будет отключен.

## <a name="cloud-role-name"></a>Имя облачной роли

Имя облачной роли используется для обозначения компонента на схеме приложения.

Если вы хотите задать имя роли облака:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Если имя роли облака не задано, имя ресурса Application Insights будет использоваться для обозначения компонента на схеме приложения.

Вы также можете задать имя роли облака с помощью переменной среды `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Экземпляр облачной роли

По умолчанию экземпляр облачной роли имеет имя компьютера.

Если вы хотите задать для экземпляра роли облака нечто другое, а не имя компьютера:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Вы также можете задать экземпляр роли облака с помощью переменной среды `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Запись журнала приложений

Application Insights Предварительная версия Java 3,0 автоматически захватывает ведение журнала приложений с помощью log4j, Logback и Java. util. Logging.

По умолчанию он собирает все записи журнала, выполненные на `INFO` уровне или выше.

Если вы хотите изменить это пороговое значение:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Можно также задать пороговое значение ведения журнала с помощью переменной среды `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Это допустимые `threshold` значения, которые можно указать в `ApplicationInsights.json` файле и их соответствие уровням ведения журнала в разных платформах ведения журналов.

| пороговое значение   | Log4j  | Logback | ИЮЛ     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| АВАРИЙ             | АВАРИЙ  | ошибка   | SEVERE  |
| Ошибка (или СЕРЬЕЗная) | ошибка  | ошибка   | SEVERE  |
| ПРЕДУПРЕЖДАть (или ПРЕДУПРЕЖДАть) | ДАТЬ   | ДАТЬ    | ПРЕДУПРЕЖДЕНИЕ |
| ИНФОРМАЦИЯ              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| ОТЛАДКа (или Точная)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (или FINEST) | трассировка  | трассировка   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Метрики JMX

Если у вас есть некоторые JMX метрики, которые вы заинтересованы в захвате:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Метрики JMX также можно задать с помощью переменной среды `APPLICATIONINSIGHTS_JMX_METRICS` .

Это содержимое переменной среды должно быть данными JSON, соответствующим приведенной выше структуре, например `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Микрометер (включая метрики с пружинного загрузочного выключателя)

Если приложение использует [микрометер](https://micrometer.io), Application Insights 3,0 (начиная с предварительной версии. 2) теперь собирает метрики, отправленные в глобальный реестр микрометер.

Если приложение использует [пружинный выключатель загрузки](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), то Application Insights 3,0 (начиная с предварительной версии. 4) теперь захватывает метрики, настроенные с помощью программы-загрузчика пружины (которая использует микрометер, но не использует глобальный реестр микрометер).

Если вы хотите отключить эти функции, выполните следующие действия.

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

По умолчанию Application Insights Предварительная версия Java 3,0 отправляет метрику пульса каждые 15 минут. Если вы используете метрику пульса для активации оповещений, можно увеличить частоту этого пульса:

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
> Вы не можете уменьшить частоту этого пульса, так как данные пульса также используются для мониторинга Application Insights использования.

## <a name="sampling"></a>Выборка

Выборка полезна, если необходимо снизить затраты.
Выборка выполняется в качестве функции для идентификатора операции (также известного как идентификатор трассировки), поэтому один и тот же идентификатор операции всегда будет принимать одно и то же решение выборки. Это гарантирует, что вы не будете получать части распределенной транзакции, выборке в, в то время как другие ее части.

Например, если выбрать для выборки значение 10%, будут отображены только 10% транзакций, но каждый из этих 10% будет иметь полные сведения о транзакциях.

Ниже приведен пример того, как задать для выборки **10% всех транзакций** . Убедитесь, что для варианта использования задана правильная частота выборки:

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

Можно также задать процент выборки с помощью переменной среды `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>Прокси-сервер HTTP

Если приложение находится за брандмауэром и не может напрямую подключаться к Application Insights (см. [IP-адреса, используемые Application Insights](./ip-addresses.md)), можно настроить Application Insights Java 3,0 Preview для использования прокси-сервера http:

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

## <a name="self-diagnostics"></a>Самостоятельная диагностика

"Самодиагностика" относится к внутреннему журналу Application Insights предварительной версии Java 3,0.

Это может быть полезно для обнаружения и диагностики проблем с Application Insights.

По умолчанию он записывается в консоль с уровнем `warn` , соответствующим этой конфигурации:

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

Допустимые уровни: `OFF` , `ERROR` ,,, `WARN` `INFO` `DEBUG` и `TRACE` .

Если вы хотите записать в файл вместо ведения журнала в консоли, выполните следующие действия.

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

При использовании ведения журнала файлов после того, как файл будет найден, `maxSizeMB` он переключается, оставляя только последний завершенный файл журнала в дополнение к текущему файлу журнала.
