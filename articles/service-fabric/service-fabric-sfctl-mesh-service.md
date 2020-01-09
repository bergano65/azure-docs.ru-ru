---
title: Azure Service Fabric CLI — служба сетки sfctl
description: Сведения о sfctl, интерфейсе командной строки Azure Service Fabric. Содержит список команд для получения сведений о службе для ресурса приложения.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 1ae04d054a254746d59b85f4fe366cebf19e3faf
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646098"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Получение сведений о службе и списка служб ресурса приложения.

## <a name="commands"></a>Команды

|Get-Help|Description|
| --- | --- |
| list | Составляет список всех ресурсов службы. |
| показать | Предоставляет ресурс службы с заданным именем. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Составляет список всех ресурсов службы.

Возвращает сведения обо всех службах ресурса приложения. Эти сведения содержат описание и другие свойства службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Description|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Предоставляет ресурс службы с заданным именем.

Получает сведения о ресурсе службы с заданным именем. Эти сведения содержат описание и другие свойства службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --name -n                     [обязательный аргумент] | Имя службы. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Description|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).