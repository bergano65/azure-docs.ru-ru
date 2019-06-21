---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185096"
---
### <a name="cacheskuname"></a>Параметр cacheSKUName

Ценовая категория нового кэша Azure для Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Шаблон определяет значения, разрешенные для этого параметра (Basic, Standard или Premium) и присваивает значение по умолчанию (Basic), если значение не указано. Уровень Basic предоставляет один узел с различными размерами (до 53 ГБ). Уровень Standard предоставляет два узла (основной и реплика) с различными размерами (до 53 ГБ) и соглашением об уровне обслуживания 99,9 %.

### <a name="cacheskufamily"></a>Параметр cacheSKUFamily

Семейство для SKU.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>Параметр cacheSKUCapacity

Создает экземпляр кэша Azure для Redis.

Для семейства "Basic" и "стандартный":

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Емкость кэша уровня "премиум" значение определяется запуска одинаково, за исключением допустимые значения от 1 до 5 вместо от 0 до 6.

Шаблон определяет целые значения, разрешенные для этого параметра (0 – 6 для семейств Basic и Standard; 1 – 5 для семейства "премиум"). Если значение не указано, шаблон назначает значение по умолчанию 0 Basic и Standard, 1 для уровня "премиум".

Значения соответствуют следующим размерам кэша:

| Значение | Basic и Standard<br>Размер кэша | "Премиум"<br>Размер кэша |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 МБ (по умолчанию)                 | Недоступно                   |
| 1     | 1 GB                             | 6 ГБ (по умолчанию)        |
| 2     | 2,5 ГБ                           | 13 ГБ                 |
| 3     | 6 ГБ                             | 26 ГБ                 |
| 4\.     | 13 ГБ                            | 53 ГБ                 |
| 5     | 26 ГБ                            | 120 ГБ                |
| 6     | 53 ГБ                            | Недоступно                   |
