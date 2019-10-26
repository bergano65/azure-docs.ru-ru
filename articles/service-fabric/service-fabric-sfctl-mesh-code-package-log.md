---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl mesh code-package-log | Документация Майкрософт'
description: Описание команд sfctl mesh code-package-log интерфейса командной строки Service Fabric.
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901264"
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
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |


## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).