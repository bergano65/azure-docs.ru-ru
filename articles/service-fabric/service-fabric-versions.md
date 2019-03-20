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
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191653"
---
# <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/),

Сведения о том, как обеспечить управление кластером поддерживаемой версией Service Fabric, приведены в следующих документах.

- [Обновление до версии Service Fabric в кластере Azure](service-fabric-cluster-upgrade.md)
- [Обновление до версии Service Fabric в изолированном кластере windows server](service-fabric-cluster-upgrade-windows-server.md)

Ниже приведен список поддерживаемых версий Service Fabric и их даты окончания поддержки.

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