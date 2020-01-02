---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152021"
---
## <a name="prepare-an-iot-hub"></a>Подготовка Центра Интернета вещей

Для выполнения инструкций, приведенных в этом кратком руководстве, также необходим Центр Интернета вещей Azure в подписке Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. Если у вас нет Центра Интернета вещей, создайте его, выполнив инструкции, указанные в [этой статье](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> На этапе общедоступной предварительной версии функции IoT Plug and Play доступны только в центрах Интернета вещей, созданных в таких регионах, как **Центральная часть США**, **Северная Европа** и **Восточная Япония**.

Выполните следующую команду, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI в экземпляр Cloud Shell:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Выполните приведенную ниже команду, чтобы создать удостоверение устройства в Центре Интернета вещей. Замените заполнители **YourIoTHubName** и **YourDeviceID** своими значениями _имени Центра Интернета вещей_ и _идентификатора устройства_.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Выполните следующую команду, чтобы получить _строку подключения устройства_ для зарегистрированного устройства (запишите ее для использования в будущем):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Выполните следующую команду, чтобы получить _строку подключения к вашему Центру Интернета вещей_ (запишите для использования в будущем):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```