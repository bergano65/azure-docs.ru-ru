---
author: baanders
description: файл включения для Azure Digital Twins — настройка Cloud Shell и расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032240"
---
Чтобы начать работу с Azure Digital Twins, сначала выполните вход в открытом окне [Azure Cloud Shell](https://shell.azure.com) и задайте контекст оболочки для подписки для этого сеанса. Выполните в Cloud Shell следующие команды:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> В команде выше вы также можете использовать имя подписки вместо идентификатора. 

Если вы впервые используете эту подписку с Azure Digital Twins, выполните эту команду, чтобы зарегистрироваться в пространстве имен Azure Digital Twins. (Если вы не уверены, то можете запустить команду снова, даже если вы уже запускали ее.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Затем добавьте в Cloud Shell расширение [**Microsoft Azure IoT для Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), чтобы разрешить использовать команды для взаимодействия с Azure Digital Twins и другими службами Интернета вещей. 

Сначала выполните эту команду, чтобы просмотреть список всех установленных расширений.

```azurecli-interactive
az extension list
```

Данные выводятся в виде массива уже имеющихся у вас расширений. Имена расширений можно увидеть в поле `"name"` для каждой записи списка.

С помощью выходных данных определите, какую из следующих команд нужно выполнить для настройки расширения (команд может быть несколько).
* Если список содержит `azure-iot`, это расширение уже установлено. Выполните эту команду, чтобы убедиться, что у вас установлены самые последние обновления:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Если список **не** содержит `azure-iot`, расширение необходимо установить. Используйте эту команду:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Если список содержит `azure-iot-cli-ext`, это устаревшая версия расширения. Может быть установлена только одна версия расширения, поэтому удалите устаревшее расширение. Используйте эту команду:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Теперь вы готовы к работе с Azure Digital Twins в Cloud Shell.

Это можно проверить в любой момент, выполнив команду `az dt -h`, чтобы вывести список доступных команд Azure Digital Twins верхнего уровня.