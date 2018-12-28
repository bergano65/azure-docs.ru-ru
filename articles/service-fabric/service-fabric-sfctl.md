---
title: 'Интерфейс командной строки Azure Service Fabric : sfctl | Документация Майкрософт'
description: Описание команд sfctl интерфейса командной строки Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cb48cff488c4f0bcd3c4a742f5d9922ec59a36b6
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269861"
---
# <a name="sfctl"></a>sfctl
Команды для управления кластерами и сущностями Service Fabric. Эта версия совместима со средой выполнения Service Fabric 6.4.

В командах используется шаблон "существительное — глагол". Дополнительные сведения см. в подгруппах.

## <a name="subgroups"></a>Подгруппы
|Подгруппа|ОПИСАНИЕ|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Создание и удаление приложений и типов приложений, а также управление ими. |
| [chaos](service-fabric-sfctl-chaos.md) | Запуск и остановка службы хаотического тестирования, а также вывод отчета о ее работе. |
| [cluster](service-fabric-sfctl-cluster.md) | Выбор кластеров Service Fabric, управление ими и их эксплуатация. |
| [compose](service-fabric-sfctl-compose.md) | Создание, удаление приложений Docker Compose и управление ими. |
| [container](service-fabric-sfctl-container.md) | Выполнение связанных с контейнером команд в узле кластера. |
| [is](service-fabric-sfctl-is.md) | Запрос службы инфраструктуры и отправка команд для нее. |
| [mesh](service-fabric-sfctl-mesh.md) | Управление приложениями в службе "Сетка Service Fabric" и их удаление. |
| [node](service-fabric-sfctl-node.md) | Управление узлами, которые образуют кластер. |
| [partition](service-fabric-sfctl-partition.md) | Запрос секций для любой службы и управление ими. |
| [property](service-fabric-sfctl-property.md) | Хранение и запрос свойств с именами Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Управление репликами, принадлежащими секциям службы. |
| [rpm](service-fabric-sfctl-rpm.md) | Запрос службы диспетчера восстановления и отправка команд для нее. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Управление автономными кластерами Service Fabric. |
| [service](service-fabric-sfctl-service.md) | Создание и удаление служб, типов служб и пакетов службы, а также управление ими. |
| [параметры](service-fabric-sfctl-settings.md) | Настройка локальных параметров этого экземпляра sfctl. |
| [store](service-fabric-sfctl-store.md) | Выполнение базовых операций на уровне файла с хранилищем образов кластера. |

## <a name="next-steps"></a>Дополнительная информация
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).