---
title: Лазурная служба Ткань CLI- sfctl
description: Узнайте о sfctl, интерфейсе командной строки Azure Service Fabric. Включает список команд и подгрупп.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906221"
---
# <a name="sfctl"></a>sfctl
Команды для управления кластерами и сущностями Service Fabric. Эта версия совместима с сервисным временем выполнения Service Fabric 7.0.

В командах используется шаблон "существительное — глагол". Дополнительные сведения см. в подгруппах.

## <a name="subgroups"></a>Подгруппы
|Подгруппа|Описание|
| --- | --- |
| [приложение](service-fabric-sfctl-application.md) | Создание и удаление приложений и типов приложений, а также управление ими. |
| [chaos](service-fabric-sfctl-chaos.md) | Запуск и остановка службы хаотического тестирования, а также вывод отчета о ее работе. |
| [Кластера](service-fabric-sfctl-cluster.md) | Выбор кластеров Service Fabric, управление ими и их эксплуатация. |
| [Составить](service-fabric-sfctl-compose.md) | Создание, удаление приложений Docker Compose и управление ими. |
| [Контейнер](service-fabric-sfctl-container.md) | Выполнение связанных с контейнером команд в узле кластера. |
| [События](service-fabric-sfctl-events.md) | Извлекать события из хранилища событий (если служба EventStore уже установлена). |
| [is](service-fabric-sfctl-is.md) | Запрос службы инфраструктуры и отправка команд для нее. |
| [mesh](service-fabric-sfctl-mesh.md) | Управление приложениями в службе "Сетка Service Fabric" и их удаление. |
| [Узла](service-fabric-sfctl-node.md) | Управление узлами, которые образуют кластер. |
| [Раздел](service-fabric-sfctl-partition.md) | Запрос секций для любой службы и управление ими. |
| [свойство;](service-fabric-sfctl-property.md) | Хранение и запрос свойств с именами Service Fabric. |
| [Реплики](service-fabric-sfctl-replica.md) | Управление репликами, принадлежащими секциям службы. |
| [Об/мин](service-fabric-sfctl-rpm.md) | Запрос службы диспетчера восстановления и отправка команд для нее. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Управление автономными кластерами Service Fabric. |
| [Службы](service-fabric-sfctl-service.md) | Создание и удаление служб, типов служб и пакетов службы, а также управление ими. |
| [настройки](service-fabric-sfctl-settings.md) | Настройка локальных параметров этого экземпляра sfctl. |
| [хранение](service-fabric-sfctl-store.md) | Выполнение базовых операций на уровне файла с хранилищем образов кластера. |

## <a name="next-steps"></a>Дальнейшие действия
- [Настройка](service-fabric-cli.md) службы Ткань CLI.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).