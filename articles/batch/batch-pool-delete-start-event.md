---
title: Событие начала удаления пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию начала удаления пула пакетной службы.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774545"
---
# <a name="pool-delete-start-event"></a>Событие начала удаления пула

 Это событие создается при запуске операции удаления пула. Так как удаление пула является асинхронным событием, можно ожидать, что после окончания этой операции возникнет событие завершения удаления пула.

 Ниже приведен пример текста для события начала удаления пула.

```
{
    "id": "myPool1"
}
```

|Элемент|Тип|Примечания|
|-------------|----------|-----------|
|идентификатор|String|Идентификатор пула.|

<!-- Update_Description: update metedata properties -->