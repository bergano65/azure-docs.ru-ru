---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185287"
---
В следующей таблице перечислены возможные комбинации отправок и записей универсальных (унив.) и специализированных (спец.) образов ОС Windows. Сочетания, которые будут работать без проблем, обозначены меткой "Да", а комбинации, которые будут выдавать ошибки, — меткой "Нет". После таблицы приведены причины возникновения ошибок и способы их устранения.

| ОС | Отправка спец. | Отправка унив. | Запись спец. | Запись унив. |
| --- | --- | --- | --- | --- |
| Windows унив. |Нет<sup>1</sup> |Да |Нет<sup>3</sup> |Да |
| Windows спец. |Да |Нет<sup>2</sup> |Да |Нет<sup>4</sup> |

