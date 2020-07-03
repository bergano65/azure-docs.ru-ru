---
title: Azure Service Fabric CLI — код сетки sfctl — пакет — журнал
description: Сведения о sfctl, интерфейсе командной строки Azure Service Fabric. Содержит список команд для получения журналов для указанного пакета кода.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906045"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Сведения о команде получения журналов для контейнера указанного пакета кода для данной реплики службы.

## <a name="commands"></a>Команды

|Команда|Описание|
| --- | --- |
| get | Получает журналы из контейнера. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Получает журналы из контейнера.

Получает журналы контейнера указанного пакета кода для реплики службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --code-package-name           [обязательный аргумент] | Имя пакета кода службы. |
| --replica-name                [обязательный аргумент] | Имя реплики Service Fabric. |
| --service-name                [обязательный аргумент] | Имя службы. |
| --tail | Число отображаемых строк из конца указанных журналов. Количество по умолчанию — 100. Значение all отображает полные журналы. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |


## <a name="next-steps"></a>Дальнейшие шаги
- [Настройте](service-fabric-cli.md) Service Fabric CLI.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).