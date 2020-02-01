---
title: Azure Service Fabric CLI — код сетки sfctl — пакет — журнал
description: Сведения о sfctl, интерфейсе командной строки Azure Service Fabric. Содержит список команд для получения журналов для указанного пакета кода.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906045"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Сведения о команде получения журналов для контейнера указанного пакета кода для данной реплики службы.

## <a name="commands"></a>Команды

|Get-Help|Description|
| --- | --- |
| get | Получает журналы из контейнера. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Получает журналы из контейнера.

Получает журналы контейнера указанного пакета кода для реплики службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --code-package-name           [обязательный аргумент] | Имя пакета кода службы. |
| --replica-name                [обязательный аргумент] | Имя реплики Service Fabric. |
| --service-name                [обязательный аргумент] | Имя службы. |
| --tail | Число отображаемых строк из конца указанных журналов. Количество по умолчанию — 100. Значение all отображает полные журналы. |

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