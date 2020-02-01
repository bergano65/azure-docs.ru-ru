---
title: Azure Service Fabric CLI — развертывание сетки sfctl
description: Сведения о sfctl, интерфейсе командной строки Azure Service Fabric. Содержит список команд для создания Service Fabricных ресурсов сетки.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906026"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Создание ресурсов службы "Сетка Service Fabric".

## <a name="commands"></a>Команды

|Get-Help|Description|
| --- | --- |
| create | Создает развертывание ресурсов Сетки Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Создает развертывание ресурсов Сетки Service Fabric.

### <a name="arguments"></a>Аргументы

|Аргумент|Description|
| --- | --- |
| --input-yaml-files [обязательный] | Относительные или абсолютные пути к файлам всех файлов YAML или относительный или абсолютный путь к каталогу (с рекурсией), который содержит файлы YAML. |
| --parameters | Относительный или абсолютный путь к YAML-файлу или объекту JSON, который содержит параметры, которые необходимо переопределить. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|Description|
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

Объединяет и развертывает все ресурсы в каталоге в кластере путем переопределения параметров, передаваемых непосредственно в виде объекта JSON.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).
