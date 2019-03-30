---
title: 'Интерфейс командной строки Azure Service Fabric: - sfctl mesh volume | Документация Майкрософт'
description: Описание команд sfctl mesh volume интерфейса командной строки Service Fabric.
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
ms.openlocfilehash: 740aec6ccb9b20cbcb8f55b2518c2b2539ef82ac
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668744"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Получение и удаление ресурсов тома.

## <a name="commands"></a>Команды

|Команда|ОПИСАНИЕ|
| --- | --- |
| удалить | Удаляет ресурс тома. |
| list | Выводит список всех ресурсов тома. |
| show | Предоставляет ресурс тома с заданным именем. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Удаляет ресурс тома.

Удаляет ресурс тома, идентифицированного по имени.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --name -n [обязательный параметр] | Имя тома. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Выводит список всех ресурсов тома.

Возвращает сведения обо всех ресурсах тома в указанной группе ресурсов. Эти сведения содержат описание и другие свойства тома.

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Предоставляет ресурс тома с заданным именем.

Получает сведения о ресурсе тома с заданным именем. Эти сведения содержат описание и другие свойства тома.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --name -n [обязательный параметр] | Имя тома. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).