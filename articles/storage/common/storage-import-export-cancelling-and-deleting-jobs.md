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
ms.openlocfilehash: 0aa1a84da874e0b0f3ed8b865eab0490f1498c1e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451229"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure

 Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено по мере возможности. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание примет состояние `Completed` и будет храниться в течение 90 дней, после чего будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) перед доставкой задания (т. е. пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
