---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373110"
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

1. Клонировать репозиторий на локальном компьютере. Выберите папку шаблона `/ocean_blue` или `/slate_gray`.
1. Отправьте все файлы в папку шаблона и `/assets` в хранилище BLOB-объектов, как описано в предыдущих разделах.
1. Затем откройте каждый файл `\*.html` в корневом каталоге `/ocean_blue` или `/slate_gray`, замените все экземпляры относительных URL-адресов файлами CSS, изображениями и шрифтами, отправленными на шаге 2. Пример:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Чтобы 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Сохраните файлы `\*.html` и отправьте их в хранилище BLOB-объектов.
1. Теперь измените политику, указав HTML-файл, как упоминалось ранее.
1. Если вы видите отсутствующие шрифты, изображения или CSS, проверьте ссылки в политиках расширений и файлах \*. HTML.
