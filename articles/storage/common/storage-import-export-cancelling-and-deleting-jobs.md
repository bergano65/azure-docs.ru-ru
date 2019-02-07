---
title: Отмена и удаление задания импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как отменять и удалять задания службы импорта и экспорта Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700134"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure

 Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию [Update Job Properties](/rest/api/storageimportexport/jobs) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено по мере возможности. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание примет состояние `Completed` и будет храниться в течение 90 дней, после чего будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs) перед доставкой задания (т. е. пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
