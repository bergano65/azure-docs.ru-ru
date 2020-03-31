---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185290"
---
В таблице ниже перечислены возможные сочетания отправки и записи универсальных и специализированных образов ОС Linux. Сочетания, которые будут работать без проблем, обозначены меткой "Да", а комбинации, которые будут выдавать ошибки, — меткой "Нет". После таблицы приведены причины возникновения ошибок и способы их устранения.

| OS | Отправка спец. | Отправка унив. | Запись спец. | Запись унив. |
| --- | --- | --- | --- | --- |
| Унив. Linux  |N<sup>1</sup> |Да |N<sup>3</sup> |Да |
| Спец. Linux |Да |N<sup>2</sup> |Да |N<sup>4</sup> |

