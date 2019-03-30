---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl settings telemetry | Документация Майкрософт'
description: Описание команд sfctl settings telemetry интерфейса командной строки Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669271"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Настройте локальные параметры телеметрии этого экземпляра sfctl.

sfctl telemetry собирает имя команды без предоставляемых параметров или их значений, версии sfctl, типа операционной системы, версии Python, данных об успешном выполнении или сбое команды, возвращенного сообщения об ошибке.

## <a name="commands"></a>Команды

|Команда|ОПИСАНИЕ|
| --- | --- |
| set-telemetry | Включение и отключение телеметрии. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
Включение и отключение телеметрии.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --off | Отключение телеметрии. |
| --on | Включение телеметрии. Это значение по умолчанию. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

### <a name="examples"></a>Примеры

Отключение телеметрии.

```
sfctl settings telemetry set_telemetry --off
```

Включение телеметрии.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).