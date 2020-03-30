---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189037"
---
## <a name="sample-templates"></a>Примеры шаблонов
Здесь можно найти примеры шаблонов для настройки пользовательского интерфейса:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Этот проект содержит следующие шаблоны:
- [Океан Блю](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Шифер Серый](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Для использования образца:

1. Клонре репо на локальной машине. Выберите папку `/ocean_blue` `/slate_gray`шаблона или .
1. Загрузите все файлы в `/assets` папку шаблона и папку, чтобы хранить Blob, как описано в предыдущих разделах.
1. Затем откройте `\*.html` каждый файл `/ocean_blue` в `/slate_gray`корне либо или, замените все экземпляры относительных URL-адресов URL-адресами файлов css, изображений и шрифтов, загруженных в шаге 2. Пример:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Чтобы
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Сохраните `\*.html` файлы и загрузите их в хранилище Blob.
1. Теперь измените политику, указав на ваш HTML-файл, как упоминалось ранее.
1. Если вы видите недостающие шрифты, изображения или CSS, проверьте ссылки в политике расширений и файлах \*.html.
