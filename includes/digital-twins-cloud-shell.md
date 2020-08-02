---
author: baanders
description: файл включения для Azure Digital Twins — настройка Cloud Shell и расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507319"
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

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Теперь вы готовы к работе с Azure Digital Twins в Cloud Shell.

Это можно проверить в любой момент, выполнив команду `az dt -h`, чтобы вывести список доступных команд Azure Digital Twins верхнего уровня.