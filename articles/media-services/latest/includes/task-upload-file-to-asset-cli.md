---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88657142"
---
<!-- ### Upload files with the CLI -->

Следующая команда отправляет один файл.  

Измените следующие значения:

1. Измените `/path/to/file` на локальный путь к файлу.  
1. Измените `MyContainer` на ресурс, созданный ранее с помощью идентификатора актива (не имени).
1. Измените `MyBlob` на имя, которое вы хотите использовать для отправленного файла.
1. Измените `MyStorageAccountName` на имя используемой учетной записи хранения.
1. Измените `MyStorageAccountKey` на ключ доступа для вашей учетной записи хранения.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
