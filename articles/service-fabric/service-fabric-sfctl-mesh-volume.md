---
title: 'Интерфейс командной строки Azure Service Fabric: - sfctl mesh volume | Документация Майкрософт'
description: Описание команд sfctl mesh volume интерфейса командной строки Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: cc26a0a3170f6cd75f5a1cbb3981044d28d9aba3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901132"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Получение и удаление ресурсов тома.

## <a name="commands"></a>Команды

|Команда|Описание|
| --- | --- |
| удалить | Удаляет ресурс тома. |
| list | Выводит список всех ресурсов тома. |
| показать | Предоставляет ресурс тома с заданным именем. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Удаляет ресурс тома.

Удаляет ресурс тома, идентифицированного по имени.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --name -n [обязательный параметр] | Имя тома. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Выводит список всех ресурсов тома.

Возвращает сведения обо всех ресурсах тома в указанной группе ресурсов. Эти сведения содержат описание и другие свойства тома.

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Предоставляет ресурс тома с заданным именем.

Получает сведения о ресурсе тома с заданным именем. Эти сведения содержат описание и другие свойства тома.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --name -n [обязательный параметр] | Имя тома. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).