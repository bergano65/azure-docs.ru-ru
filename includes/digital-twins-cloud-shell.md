---
author: baanders
description: файл включения для Azure Digital Twins — настройка Cloud Shell и расширения Интернета вещей
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611477"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Создание сеанса Cloud Shell

После открытия окна Cloud Shell необходимо войти в систему и задать контекст оболочки для подписки на этот сеанс. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Если вы впервые используете эту подписку с Azure Digital Twins, выполните эту команду, чтобы зарегистрироваться в пространстве имен Azure Digital Twins. (Если вы не уверены, то можете запустить команду снова, даже если вы уже запускали ее.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Выполните следующую команду в экземпляре Cloud Shell, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> В этой статье используется последняя версия расширения Azure IoT под названием `azure-iot`. Прежняя версия называется `azure-iot-cli-ext`. В каждый момент времени должна быть установлена только одна версия. Проверить установленные расширения можно с помощью команды `az extension list`.
> Используйте `az extension remove --name azure-cli-iot-ext`, чтобы удалить устаревшую версию расширения.
> Используйте `az extension add --name azure-iot`, чтобы добавить новую версию расширения. Чтобы узнать, какие расширения установлены, используйте `az extension list`.

> [!TIP]
> Вы можете запустить `az dt -h`, чтобы просмотреть команды верхнего уровня Azure Digital Twins.