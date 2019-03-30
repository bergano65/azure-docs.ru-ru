---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl mesh code-package-log | Документация Майкрософт'
description: Описание команд sfctl mesh code-package-log интерфейса командной строки Service Fabric.
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661077"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Сведения о команде получения журналов для контейнера указанного пакета кода для данной реплики службы.

## <a name="commands"></a>Команды

|Команда|ОПИСАНИЕ|
| --- | --- |
| get | Получает журналы из контейнера. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Получает журналы из контейнера.

Получает журналы контейнера указанного пакета кода для реплики службы.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --app-name --application-name [обязательный аргумент] | Имя приложения. |
| --code-package-name           [обязательный аргумент] | Имя пакета кода службы. |
| --replica-name                [обязательный аргумент] | Имя реплики Service Fabric. |
| --service-name                [обязательный аргумент] | Имя службы. |
| --tail | Число отображаемых строк из конца указанных журналов. Количество по умолчанию — 100. Значение all отображает полные журналы. |

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