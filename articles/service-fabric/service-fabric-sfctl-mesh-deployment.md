---
title: Развертывание сетки Службы обслуживания Azure Fabric CLI-
description: Узнайте о интерфейсе командной строки Sfctl, интерфейсе командной строки Azure Service Fabric. Включает список команд для создания ресурсов Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906026"
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
| --input-yaml-files [обязательный] | Запятые-отделенные относительные или абсолютные пути файла всех файлов yaml или относительного или абсолютного пути каталога (рекурсивного), которые содержат файлы yaml. |
| --parameters | Относительный или абсолютный путь к файлу yaml или объекту json, который содержит параметры, которые необходимо переопределить. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Описание|
| --- | --- |
| --debug | Повышение уровня детализации журнала для включения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат вывода.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. на веб-сайте http\://jmespath.org/. |
| --verbose | Повышение уровня детализации журнала. Чтобы включить полные журналы отладки, используйте параметр --debug. |

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

Консолидация и развертывание всех ресурсов в каталоге для кластера путем переопределения параметров, которые передаются непосредственно как объект json
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Дальнейшие действия
- [Настройка](service-fabric-cli.md) службы Ткань CLI.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).
