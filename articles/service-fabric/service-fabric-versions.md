---
title: Описание версии кластера поддержки Azure Service Fabric | Документация Майкрософт
description: Поддерживаемые версии кластера Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: twhitney
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: TylerMSFT
ms.openlocfilehash: 154869c3e6bcd44a71480a3cf7363537dddcebf9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727687"
---
# <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/),

Сведения о том, как обеспечить управление кластером поддерживаемой версией Service Fabric, приведены в следующих документах.

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade your standalone Service Fabric cluster on Windows Server](service-fabric-cluster-upgrade-windows-server.md) (Обновление изолированного кластера Service Fabric на платформе Windows Server)

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
| 6.3.* | 6.1.480.* |Не выше версии 3.2. |28 февраля 2019 г.  |
| 6.4.* | 6.2.301.* |Версия 3.3 или более ранняя |Текущая версия, дата окончания поддержки не определена. |