---
title: Событие завершения удаления пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию завершения удаления пула пакетной службы.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775769"
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
|идентификатор|String|Идентификатор пула.|
|startTime|DateTime|Время, когда было начато удаление пула.|
|endTime|DateTime|Время, когда удаление пула было завершено.|

## <a name="remarks"></a>Примечания
Дополнительные сведения о состояниях и кодах ошибок для операции изменения размера пула см. в статье [Удаление пула из учетной записи](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).

<!-- Update_Description: update metedata properties -->