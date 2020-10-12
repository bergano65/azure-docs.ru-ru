---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78189037"
---
## <a name="sample-templates"></a>Примеры шаблонов
Здесь можно найти примеры шаблонов для настройки пользовательского интерфейса:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Этот проект содержит следующие шаблоны:
- [Синий морской](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Серый планшет](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Чтобы использовать пример, выполните следующие действия.

1. Клонировать репозиторий на локальном компьютере. Выберите папку шаблона `/ocean_blue` или `/slate_gray` .
1. Отправьте все файлы в папке шаблона и `/assets` папке в хранилище BLOB-объектов, как описано в предыдущих разделах.
1. Затем откройте каждый `\*.html` файл в корневой папке `/ocean_blue` или `/slate_gray` , замените все экземпляры относительных URL-адресов файлами CSS, изображениями и шрифтами, отправленными на шаге 2. Пример:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Кому
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Сохраните `\*.html` файлы и отправьте их в хранилище BLOB-объектов.
1. Теперь измените политику, указав HTML-файл, как упоминалось ранее.
1. Если вы видите отсутствующие шрифты, изображения или CSS, проверьте ссылки в политике расширения и \* HTML-файлах.
