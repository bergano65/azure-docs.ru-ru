---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl mesh deployment | Документация Майкрософт'
description: Описание команд sfctl mesh deployment интерфейса командной строки Azure Service Fabric.
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
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035190"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Создание ресурсов службы "Сетка Service Fabric".

## <a name="commands"></a>Команды

|Command|Описание|
| --- | --- |
| create | Создает развертывание ресурсов Сетки Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Создает развертывание ресурсов Сетки Service Fabric.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --input-yaml-files [обязательный] | Разделенные запятыми относительные/абсолютные пути ко всем YAML-файлам или относительный/абсолютный путь к каталогу (рекурсивный), содержащему YAML-файлы. |
| --parameters | Относительный или абсолютный путь к YAML-файлу или объекту JSON, содержащему параметры, которые необходимо переопределить. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

### <a name="examples"></a>Примеры

Объединение и развертывание всех ресурсов в кластер путем переопределения параметров, указанных в YAML-файле

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Объединение и развертывание всех ресурсов каталога в кластер путем переопределения параметров, указанных в YAML-файле

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Объединение и развертывание всех ресурсов каталога в кластер путем переопределения параметров, которые непосредственно передаются как JSON-объект

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Следующие шаги
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).