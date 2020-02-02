---
title: Пакетная аналитика Azure
description: Статьи по пакетной аналитике содержат справочные сведения о событиях и оповещениях, доступных для ресурсов пакетной службы.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 5f5063ff2b285a0613d8c7bf367007997afcb778
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935261"
---
# <a name="batch-analytics"></a>Пакетная аналитика
Статьи по пакетной аналитике содержат справочные сведения о событиях и оповещениях, доступных для ресурсов пакетной службы.

Статья [Ведение журналов диагностики пакетной службы Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) содержит дополнительные сведения о включении и использовании журналов диагностики пакетной службы.

## <a name="diagnostic-logs"></a>Журналы диагностики

За время существования некоторых пакетных ресурсов пакетная служба Azure выдает приведенные ниже события журнала диагностики.

**События журнала службы**
* [Создание пула](batch-pool-create-event.md)
* [Начало удаления пула](batch-pool-delete-start-event.md)
* [Завершение удаления пула](batch-pool-delete-complete-event.md)
* [Начало изменения размера пула](batch-pool-resize-start-event.md)
* [Завершение изменения размера пула](batch-pool-resize-complete-event.md)
* [Начало выполнения задачи](batch-task-start-event.md)
* [Завершение выполнения задачи](batch-task-complete-event.md)
* [Сбой выполнения задачи](batch-task-fail-event.md)