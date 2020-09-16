---
author: baanders
description: Файл включения для Azure Digital Twins — настройка последней версии расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606906"
---
Сначала выполните эту команду, чтобы просмотреть список всех установленных расширений.

```azurecli
az extension list
```

Данные выводятся в виде массива уже имеющихся у вас расширений. Имена расширений можно увидеть в поле `"name"` для каждой записи списка.

С помощью выходных данных определите, какую из следующих команд нужно выполнить для настройки расширения (команд может быть несколько).
* Если список содержит `azure-iot`, это расширение уже установлено. Выполните эту команду, чтобы убедиться, что у вас установлены самые последние обновления:

   ```azurecli
   az extension update --name azure-iot
   ```

* Если список **не** содержит `azure-iot`, расширение необходимо установить. Используйте эту команду:

    ```azurecli
    az extension add --name azure-iot
    ```

* Если список содержит `azure-iot-cli-ext`, это устаревшая версия расширения. Может быть установлена только одна версия расширения, поэтому удалите устаревшее расширение. Используйте эту команду:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```