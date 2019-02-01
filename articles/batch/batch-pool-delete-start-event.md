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
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474298"
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
|id|Строка|Идентификатор пула.|