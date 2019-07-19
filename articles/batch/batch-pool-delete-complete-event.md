---
title: Событие завершения удаления пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию завершения удаления пула пакетной службы.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: fd32554866d1e2130fd0833adc1b286fb6bc07a5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323227"
---
# <a name="pool-delete-complete-event"></a>Событие завершения удаления пула

 Это событие создается при завершении операции удаления пула.

 Ниже приведен пример текста для события завершения удаления пула.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Элемент|Тип|Примечания|
|-------------|----------|-----------|
|id|Строка,|Идентификатор пула.|
|startTime|DateTime|Время, когда было начато удаление пула.|
|endTime|DateTime|Время, когда удаление пула было завершено.|

## <a name="remarks"></a>Примечания
Дополнительные сведения о состояниях и кодах ошибок для операции изменения размера пула см. в статье [Удаление пула из учетной записи](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).