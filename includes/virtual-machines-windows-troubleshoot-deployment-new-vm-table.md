---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185287"
---
В следующей таблице перечислены возможные комбинации загрузки и захвата обобщенных (gen.) и специализированных (spec.) изображений ОС. Сочетания, которые будут работать без проблем, обозначены меткой "Да", а комбинации, которые будут выдавать ошибки, — меткой "Нет". После таблицы приведены причины возникновения ошибок и способы их устранения.

| OS | Отправка спец. | Отправка унив. | Запись спец. | Запись унив. |
| --- | --- | --- | --- | --- |
| Windows унив. |N<sup>1</sup> |Да |N<sup>3</sup> |Да |
| Windows спец. |Да |N<sup>2</sup> |Да |N<sup>4</sup> |

