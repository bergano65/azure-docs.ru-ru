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
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 4e2413a152048e08d6218783891c20b66ac2e618
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034991"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Получение и удаление ресурсов тома.

## <a name="commands"></a>Команды

|Command|Описание|
| --- | --- |
| delete | Удаляет ресурс тома. |
| list | Выводит список всех ресурсов тома. |
| show | Предоставляет ресурс тома с заданным именем. |

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
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
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
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
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
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Следующие шаги
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).