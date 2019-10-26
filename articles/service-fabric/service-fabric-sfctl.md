---
title: 'Интерфейс командной строки Azure Service Fabric : sfctl | Документация Майкрософт'
description: Описание команд sfctl интерфейса командной строки Azure Service Fabric.
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
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900885"
---
# <a name="sfctl"></a>sfctl
Команды для управления кластерами и сущностями Service Fabric. Эта версия совместима со средой выполнения Service Fabric 6,5.

В командах используется шаблон "существительное — глагол". Дополнительные сведения см. в подгруппах.

## <a name="subgroups"></a>Подгруппы
|Подгруппа|Описание|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Создание и удаление приложений и типов приложений, а также управление ими. |
| [chaos](service-fabric-sfctl-chaos.md) | Запуск и остановка службы хаотического тестирования, а также вывод отчета о ее работе. |
| [cluster](service-fabric-sfctl-cluster.md) | Выбор кластеров Service Fabric, управление ими и их эксплуатация. |
| [compose](service-fabric-sfctl-compose.md) | Создание, удаление приложений Docker Compose и управление ими. |
| [container](service-fabric-sfctl-container.md) | Выполнение связанных с контейнером команд в узле кластера. |
| [events](service-fabric-sfctl-events.md) | Получение событий из хранилища событий (если служба Евентсторе уже установлена). |
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

## <a name="next-steps"></a>Дальнейшие действия
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).