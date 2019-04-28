---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201704"
---
### <a name="install-via-composer"></a>Установка через компоновщик
1. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.
3. Откройте командную строку и выполните следующую команду в корневом каталоге проекта.
   
    ```
    php composer.phar install
    ```

Также можно перейти в [клиентскую библиотеку службы хранилища Azure для PHP][php-sdk-github] на GitHub, чтобы клонировать исходный код.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
