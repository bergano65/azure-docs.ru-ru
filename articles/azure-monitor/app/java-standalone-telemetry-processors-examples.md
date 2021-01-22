---
title: Примеры обработчиков данных телеметрии. Azure Monitor Application Insights для Java
description: Примеры, иллюстрирующие обработчики данных телеметрии в Azure Monitor Application Insights для Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696318"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Примеры обработчиков данных телеметрии. Azure Monitor Application Insights для Java

## <a name="includeexclude-samples"></a>Примеры включения и исключения

### <a name="1-include-spans"></a>1. Включение диапазонов

Ниже показано включение диапазонов для этого обработчика атрибутов. Все остальные диапазоны, которые не соответствуют свойствам, не обрабатываются этим процессором.

Ниже приведены условия, которые должны быть выполнены для соответствия.
* Имя диапазона должно быть равно "span a" или "Спанб" 

Ниже приведены диапазоны, которые соответствуют свойствам include и применяются действия процессора.
* Имя Span1: ' span ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Имя Span2: атрибуты "Спанб": {env: dev, test_request: false}
* Имя Span3: ' span ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Следующий диапазон не соответствует свойствам include, а действия процессора не применяются.
* Имя Span4: атрибуты "Спанк": {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. исключение диапазонов

Ниже показано исключение диапазонов для этого обработчика атрибутов. Все диапазоны, соответствующие свойствам, не обрабатываются этим процессором.

Ниже приведены условия, которые должны быть выполнены для соответствия.
* Имя диапазона должно быть равно "span a" или "Спанб" 

Ниже приведены диапазоны, которые соответствуют свойствам исключения, а действия процессора не применяются.
* Имя Span1: ' span ' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Имя Span2: атрибуты "Спанб": {env: dev, test_request: false}
* Имя Span3: ' span ' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Следующий диапазон не соответствует свойствам исключения, и применяются действия процессора.
* Имя Span4: атрибуты "Спанк": {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. Ексклудемулти диапазоны

Ниже показано исключение диапазонов для этого обработчика атрибутов. Все диапазоны, соответствующие свойствам, не обрабатываются этим процессором.

Ниже приведены условия, которые должны быть выполнены для соответствия.
* Для соответствия в диапазоне должен существовать атрибут ("env", "Dev").
* При условии, что в диапазоне есть атрибут с ключом "test_request", существует совпадение.

Ниже приведены диапазоны, которые соответствуют свойствам исключения, а действия процессора не применяются.
* Имя Span1: атрибуты "Спанб": {env: dev, test_request: 123, credit_card: 1234}
* Имя Span2: ' span ' Attributes: {env: dev, test_request: false}

Следующий диапазон не соответствует свойствам исключения, и применяются действия процессора.
* Имя Span3: атрибуты "Спанб": {env: 1, test_request: dev, credit_card: 1234}
* Имя Span4: атрибуты "Спанк": {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. Выборочная обработка

Ниже показано, как указать набор свойств span, чтобы указать, к каким диапазонам должен применяться этот процессор. `include`Свойства, которые указывают, какие из них должны быть добавлены, а также `exclude` свойства, позволяющие отфильтровать диапазоны, которые не должны обрабатываться.

В следующей конфигурации следующие диапазоны соответствуют свойствам и применяются действия процессора:

* Имя Span1: атрибуты "Спанб": {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Имя Span2: ' span ' Attributes: {env: промежуточное хранение, test_request: false, redact_trace: true}

Следующие диапазоны не соответствуют свойствам включения, и действия процессора не применяются:
* Имя Span3: атрибуты "Спанб": {env: Production, test_request: true, credit_card: 1234, redact_trace: false}
* Имя Span4: атрибуты "Спанк": {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Образцы обработчика атрибутов

### <a name="insert"></a>Вставить

Следующий код вставляет новый атрибут {"атрибут1": "attributeValue1"} в диапазон, где не существует ключ "атрибут1".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Вставить из другого ключа

В следующем примере значение атрибута "аносеркэй" используется для вставки нового атрибута {"Невкэй": "Value из атрибута" аносеркэй "} в диапазон, где не существует ключа" Невкэй ". Если атрибут "аносеркэй" не существует, новый атрибут не вставляется в диапазон.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Update

Следующий код обновляет атрибут до {"DB. Secret": "показана исправленная версия"} и обновляет атрибут "BOO", используя значение атрибута "foo". Диапазоны без атрибута "BOO" не изменятся.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Удалить

Ниже демонстрируется удаление атрибута с ключом "credit_card".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Хэш

Ниже показаны хэш-значения существующих значений атрибутов.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

В следующем примере показано использование Regex для создания новых атрибутов на основе значения другого атрибута.
Например, при указании HTTP. URL = " http://example.com/path?queryParam1=value1 , queryParam2 = value2" будут вставлены следующие атрибуты:
* Хттппротокол: http
* Хттпдомаин: example.com
* httpPath: путь
* Хттпкуерипарамс: queryParam1 = Значение1, queryParam2 = значение2
* значение HTTP. URL не изменяется.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

В следующем примере показано, как обрабатывать диапазоны с именем диапазона, которое соответствует шаблонам RegExp.
Этот процессор удалит атрибут "token" и запишет атрибут "Password" в диапазонах, где имя диапазона совпадает с "auth \* ". и где имя Span не соответствует имени \* входа.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Примеры процессоров span

### <a name="name-a-span"></a>Назовите диапазон

В следующем примере задаются значения атрибута "DB. svc", "Operation" и "ID", которые формируют новое имя диапазона в этом порядке, разделенное значением "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Извлечь атрибуты из имени диапазона

Предположим, что имя диапазона ввода —/API/V1/Document/12345678/Update. Применение следующих результатов в области вывода имя/api/v1/document/{documentId}/update добавит новый атрибут "documentId" = "12345678" к диапазону.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Извлечение атрибутов из имени диапазона с помощью include и Exclude

Ниже показано, как переименовать область имен в "{operation_website}" и добавить атрибут {key: operation_website, значение: Олдспаннаме}, если диапазон имеет следующие свойства:
- Имя диапазона содержит строку "/" в любом месте строки.
- Имя диапазона не является "не разграничения/Change".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```