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
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035202"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Настройте локальные параметры телеметрии этого экземпляра sfctl.

sfctl telemetry собирает имя команды без предоставляемых параметров или их значений, версии sfctl, типа операционной системы, версии Python, данных об успешном выполнении или сбое команды, возвращенного сообщения об ошибке.

## <a name="commands"></a>Команды

|Command|Описание|
| --- | --- |
| set-telemetry | Включение и отключение телеметрии. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
Включение и отключение телеметрии.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --off | Отключение телеметрии. |
| --on | Включение телеметрии. Это значение по умолчанию. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
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


## <a name="next-steps"></a>Следующие шаги
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).