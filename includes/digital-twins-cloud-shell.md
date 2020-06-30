---
author: baanders
description: файл включения для Azure Digital Twins — настройка Cloud Shell и расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296973"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Создание сеанса Cloud Shell

После открытия окна Cloud Shell необходимо войти в систему и задать контекст оболочки для подписки на этот сеанс. Выполните в Cloud Shell следующие команды:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Если вы впервые используете эту подписку с Azure Digital Twins, выполните эту команду, чтобы зарегистрироваться в пространстве имен Azure Digital Twins. (Если вы не уверены, то можете запустить команду снова, даже если вы уже запускали ее.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Затем добавьте в Cloud Shell расширение [**Microsoft Azure IoT для Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest), чтобы разрешить использовать команды для взаимодействия с Azure Digital Twins и другими службами Интернета вещей. Чтобы добавить расширение, используйте следующую команду:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Если вы установили расширение ранее, в выходных данных может отобразиться сообщение "Расширение «azure-iot» уже установлено". В таком случае выполните следующую команду, чтобы убедиться в наличии последнего обновления: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Теперь вы готовы к работе с Azure Digital Twins в Cloud Shell.

Это можно проверить в любой момент, выполнив команду `az dt -h`, чтобы вывести список доступных команд Azure Digital Twins верхнего уровня.