---
title: Событие начала удаления пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию начала удаления пула пакетной службы.
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
ms.openlocfilehash: 6f8b1bdd680d43cb14707338d2e37e41114126b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094747"
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
|id|Строковое|Идентификатор пула.|