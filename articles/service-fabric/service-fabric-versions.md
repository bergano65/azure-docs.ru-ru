---
title: Поддерживаемые версии кластера в Azure Service Fabric | Документация Майкрософт
description: Дополнительные сведения о версиях кластера в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716087"
---
# <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Убедитесь, что кластер всегда работает поддерживаемая версия Azure Service Fabric. Завершает по меньшей мере 60 дней после мы сообщаем о выпуске новой версии Service Fabric, поддержка предыдущей версии. Вы найдете объявления о новых выпусках [блоге группы Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

См. в следующих документах, Дополнительные сведения о том, как обеспечить управление кластером поддерживаемой версии Service Fabric:

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Обновление до версии Service Fabric под управлением автономного кластера Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Поддерживаемые версии

В следующей таблице перечислены версии Service Fabric и их даты окончания поддержки.

| Среда выполнения Service Fabric в кластере | Можно обновить непосредственно версии кластера |Совместимые версии пакета SDK или NuGet | Дата окончания поддержки |
| --- | --- |--- | --- |
| Все версии кластера до 5.3.121 | 5.1.158.* |Не выше версии 2.3 |20 января 2017 г. |
| 5.3.* | 5.1.158.* |Не выше версии 2.3 |24 февраля 2017 г. |
| 5.4.* | 5.1.158.* |Не выше версии 2.4 |10 мая 2017 г.       |
| 5.5.* | 5.4.164.* |Не выше версии 2.5 |10 августа 2017 г.    |
| 5.6.* | 5.4.164.* |Не выше версии 2.6 |13 октября 2017 г.   |
| 5.7.* | 5.4.164.* |Не выше версии 2.7 |15 декабря 2017 г.  |
| 6.0.* | 5.6.205.* |Не выше версии 2.8 |30 марта 2018 г.     |
| 6.1.* | 5.7.221.* |Не выше версии 3.0 |15 июля 2018 г.      |
| 6.2.* | 6.0.232.* |Не выше версии 3.1 |26 октября 2018 г.   |
| 6.3.* | 6.1.480.* |Не выше версии 3.2. |31 марта 2019 г.  |
| 6.4.* | 6.2.301.* |Версия 3.3 или более ранняя |Текущая версия, поэтому нет даты окончания |

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Ниже перечислены поддерживаемые операционные системы, для поддерживаемых версий Service Fabric.

| Операционная система | Самая ранняя поддерживаемой версии Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Все версии |
| Windows Server 2016 | Все версии |
| Windows Server 1709; | 6,0 |
| Windows Server 1803. | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |

