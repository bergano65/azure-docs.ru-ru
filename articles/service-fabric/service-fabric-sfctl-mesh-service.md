---
title: Интерфейс командной строки Azure Service Fabric: - sfctl mesh service | Документация Майкрософт
description: Описание команд sfctl mesh service интерфейса командной строки Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282920"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Получение сведений о службе и списка служб ресурса приложения.

## <a name="commands"></a>Команды

|Get-Help|ОПИСАНИЕ|
| --- | --- |
| list | Составляет список всех ресурсов службы. |
| show | Предоставляет ресурс службы с заданным именем. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Составляет список всех ресурсов службы.

Возвращает сведения обо всех службах ресурса приложения. Эти сведения содержат описание и другие свойства службы.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Предоставляет ресурс службы с заданным именем.

Получает сведения о ресурсе службы с заданным именем. Эти сведения содержат описание и другие свойства службы.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --name -n                     [обязательный аргумент] | Имя службы. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Дополнительная информация
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).