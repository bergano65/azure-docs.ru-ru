---
title: Описание версии кластера поддержки Azure Service Fabric | Документация Майкрософт
description: Поддерживаемые версии кластера Azure Service Fabric
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
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681613"
---
# <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/),

Сведения о том, как обеспечить управление кластером поддерживаемой версией Service Fabric, приведены в следующих документах.

- [Обновление до версии Service Fabric в кластере Azure](service-fabric-cluster-upgrade.md)
- [Обновление до версии Service Fabric в изолированном кластере windows server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Поддерживаемые версии

Ниже перечислены поддерживаемые версии Service Fabric и их даты окончания поддержки.

| **Среда выполнения Service Fabric в кластере** | **Обновление непосредственно версии кластера** |**Совместимые версии пакета SDK и NuGet** | **Дата окончания поддержки** |
| --- | --- |--- | --- |
| Все версии кластера до 5.3.121 | 5.1.158* |Не выше версии 2.3 |20 января 2017 г. |
| 5.3.* | 5.1.158.* |Не выше версии 2.3 |24 февраля 2017 г. |
| 5.4.* | 5.1.158.* |Не выше версии 2.4 |10 мая 2017 г.       |
| 5.5.* | 5.4.164.* |Не выше версии 2.5 |10 августа 2017 г.    |
| 5.6.* | 5.4.164.* |Не выше версии 2.6 |13 октября 2017 г.   |
| 5.7.* | 5.4.164.* |Не выше версии 2.7 |15 декабря 2017 г.  |
| 6.0.* | 5.6.205.* |Не выше версии 2.8 |30 марта 2018 г.     |
| 6.1.* | 5.7.221.* |Не выше версии 3.0 |15 июля 2018 г.      |
| 6.2.* | 6.0.232.* |Не выше версии 3.1 |26 октября 2018 г   |
| 6.3.* | 6.1.480.* |Не выше версии 3.2. |Март 31,2019  |
| 6.4.* | 6.2.301.* |Версия 3.3 или более ранняя |Текущая версия, дата окончания поддержки не определена. |

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

 Ниже перечислены поддерживаемые операционные системы, для поддерживаемых версий Service Fabric.

| **Операционная система** | **Самой ранней поддерживаемой версии Service Fabric** |
| --- | --- |
| Windows Server 2012 R2 | Все версии |
| Windows Server 2016 | Все версии |
| Windows Server 1709; | 6,0 |
| Windows Server 1803. | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6,0 |

