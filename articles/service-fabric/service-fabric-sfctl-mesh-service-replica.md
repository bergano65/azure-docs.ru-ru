---
title: 'Интерфейс командной строки Azure Service Fabric : sfctl mesh service-replica | Документация Майкрософт'
description: Описание команд sfctl mesh service-replica интерфейса командной строки Service Fabric.
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038454"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Получите сведения о репликах и выведите список реплик данной службы в ресурсе приложения.

## <a name="commands"></a>Команды

|Команда|Описание|
| --- | --- |
| список | Выводит список всех реплик службы. |
| показать | Получает заданную реплику службы приложения. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Выводит список всех реплик службы.

Возвращает сведения о репликах службы. Сведения содержат описание и другие свойства реплики службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --service-name                [обязательный аргумент] | Имя службы. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Получает заданную реплику службы приложения.

Получает сведения о реплике службы с заданным именем. Сведения содержат описание и другие свойства реплики службы.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --name -n                     [обязательный аргумент] | Имя реплики службы. |
| --service-name                [обязательный аргумент] | Имя службы. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).