---
title: Обновление с версии 2. x — Azure Monitor Application Insights Java
description: Обновление с Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355137"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Обновление с Application Insights пакета SDK для Java 2. x

Если вы уже используете Application Insights Java SDK 2. x в своем приложении, нет необходимости удалять его.
Агент Java 3,0 определит его, а затем запишет и сопоставьте любые пользовательские данные телеметрии, отправляемые через пакет SDK для Java 2. x, и подавление автоматической сбора данных, выполненных пакетом SDK для Java 2. x, для предотвращения дублирования данных телеметрии.

Если используется агент Application Insights 2. x, необходимо удалить `-javaagent:` аргумент виртуальной машины Java, указывающий на агент 2. x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>Telemetryinitializer и TelemetryProcessors

Пакет SDK для Java 2. x Telemetryinitializer и TelemetryProcessors не будет выполняться при использовании агента 3,0.
Многие из вариантов использования, которые ранее были необходимы, можно разрешить в 3,0, настроив [пользовательские измерения](./java-standalone-config.md#custom-dimensions) или настроив [обработчики данных телеметрии](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Несколько приложений в одном ВИРТУАЛЬНОЙ машины Java

В настоящее время 3,0 поддерживает только одну [строку подключения и имя роли](./java-standalone-config.md#connection-string-and-role-name) для каждого выполняемого процесса. В частности, у вас не может быть нескольких Tomcat веб-приложений в одном развертывании Tomcat, использующих разные строки подключения или имена ролей.

## <a name="http-request-telemetry-names"></a>Имена телеметрии HTTP-запросов

Имена телеметрии HTTP-запросов в 3,0 были изменены, чтобы обеспечить лучшее агрегированное представление на Application Insights портале U/X.

Однако для некоторых приложений вы по-прежнему предпочитаете агрегированное представление в U/X, которое было предоставлено предыдущими именами телеметрии. в этом случае можно использовать функцию предварительной версии обработчиков данных телеметрии в 3,0, чтобы вернуться к предыдущим именам.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Чтобы добавить в префикс имя телеметрии с помощью метода HTTP ( `GET` , `POST` и т. д.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Задание полного URL-пути для имени телеметрии

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
