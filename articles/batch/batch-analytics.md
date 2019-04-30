---
title: Пакетная аналитика Azure | Документы Майкрософт
description: Справочник по пакетной аналитике Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721561"
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