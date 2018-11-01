---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226684"
---
В следующей таблице перечислены возможные комбинации отправок и записей универсальных (унив.) и специализированных (спец.) образов ОС Windows. Сочетания, которые будут работать без проблем, обозначены меткой "Да", а комбинации, которые будут выдавать ошибки, — меткой "Нет". После таблицы приведены причины возникновения ошибок и способы их устранения.

| ОС | Отправка спец. | Отправка унив. | Запись спец. | Запись унив. |
| --- | --- | --- | --- | --- |
| Windows унив. |Нет<sup>1</sup> |Да |Нет<sup>3</sup> |Да |
| Windows спец. |Да |Нет<sup>2</sup> |Да |Нет<sup>4</sup> |

