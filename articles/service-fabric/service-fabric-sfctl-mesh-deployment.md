---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl mesh deployment | Документация Майкрософт'
description: Описание команд sfctl mesh deployment интерфейса командной строки Azure Service Fabric.
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
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901250"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Создание ресурсов службы "Сетка Service Fabric".

## <a name="commands"></a>Команды

|Команда|Описание|
| --- | --- |
| create | Создает развертывание ресурсов Сетки Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Создает развертывание ресурсов Сетки Service Fabric.

### <a name="arguments"></a>Аргументы

|Аргумент|Описание|
| --- | --- |
| --input-yaml-files [обязательный] | Относительные и абсолютные пути к файлам всех файлов YAML или относительный или абсолютный путь к каталогу (с рекурсией), который содержит файлы YAML. |
| --parameters | Относительный или абсолютный путь к YAML-файлу или объекту JSON, который содержит параметры, которые необходимо переопределить. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения см. на сайте http\://jmespath.org. |
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

Объединяет и развертывает все ресурсы в каталоге в кластере путем переопределения параметров, передаваемых непосредственно в виде объекта JSON.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).