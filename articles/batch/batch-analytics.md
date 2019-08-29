---
title: Пакетная аналитика Azure | Документы Майкрософт
description: Справочник по пакетной аналитике Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 03cb6231a42e27c474e20f4c6ae91095156bb766
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095520"
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