---
title: Аналитика в пакетной службе Azure
description: Статьи по пакетной аналитике содержат справочные сведения о событиях и оповещениях, доступных для ресурсов пакетной службы.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849517"
---
# <a name="batch-analytics"></a>Пакетная аналитика

В подразделах этого раздела содержатся справочные сведения о событиях и оповещениях, доступных для ресурсов пакетной службы.

Статья [Ведение журналов диагностики пакетной службы Azure](./batch-diagnostics.md) содержит дополнительные сведения о включении и использовании журналов диагностики пакетной службы.

## <a name="diagnostic-logs"></a>Журналы диагностики

За время существования некоторых пакетных ресурсов пакетная служба Azure выдает приведенные ниже события журнала диагностики.

### <a name="service-log-events"></a>События журнала службы

- [Создание пула](batch-pool-create-event.md)
- [Начало удаления пула](batch-pool-delete-start-event.md)
- [Завершение удаления пула](batch-pool-delete-complete-event.md)
- [Начало изменения размера пула](batch-pool-resize-start-event.md)
- [Завершение изменения размера пула](batch-pool-resize-complete-event.md)
- [Автомасштабирование пула](batch-pool-autoscale-event.md)
- [Начало выполнения задачи](batch-task-start-event.md)
- [Завершение выполнения задачи](batch-task-complete-event.md)
- [Сбой выполнения задачи](batch-task-fail-event.md)
- [Сбой расписания задачи](batch-task-schedule-fail-event.md)
