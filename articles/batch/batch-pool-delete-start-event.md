---
title: Событие начала удаления пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию начала удаления пула пакетной службы.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
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
|id|String|Идентификатор пула.|