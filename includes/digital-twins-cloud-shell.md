---
author: baanders
description: файл включения для Azure Digital Twins — настройка Cloud Shell и расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277896"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Создание сеанса Cloud Shell

После открытия окна Cloud Shell необходимо войти в систему и задать контекст оболочки для подписки на этот сеанс. Выполните в Cloud Shell следующие команды:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Вы также можете настроить подписку с помощью имени подписки. Используйте следующую команду: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Если вы впервые используете эту подписку с Azure Digital Twins, выполните эту команду, чтобы зарегистрироваться в пространстве имен Azure Digital Twins. (Если вы не уверены, то можете запустить команду снова, даже если вы уже запускали ее.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Затем добавьте в Cloud Shell расширение [**Microsoft Azure IoT для Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), чтобы разрешить использовать команды для взаимодействия с Azure Digital Twins и другими службами Интернета вещей. 

Сначала выполните эту команду, чтобы просмотреть список всех уже установленных расширений.

```azurecli-interactive
az extension list
```

В выходных данных найдите `"name"` поле для каждой записи списка, чтобы просмотреть имена расширений.

Используйте выходные данные, чтобы определить, какую из следующих команд нужно выполнить для установки расширения (вы можете запустить несколько).
* Если список содержит `azure-iot` : у вас уже есть расширение. Выполните следующую команду, чтобы убедиться, что у вас есть Последнее обновление:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Если список **не** содержит: необходимо `azure-iot` установить расширение. Используйте следующую команду:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Если список содержит `azure-iot-cli-ext` : это устаревшая версия расширения. В каждый момент времени должна быть установлена только одна версия расширения, поэтому следует удалить устаревшее расширение. Используйте следующую команду:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Теперь вы готовы к работе с Azure Digital Twins в Cloud Shell.

Это можно проверить в любой момент, выполнив команду `az dt -h`, чтобы вывести список доступных команд Azure Digital Twins верхнего уровня.