---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 7a0ea2330e682e1e60eca6540151b0f5f620571c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453708"
---
## <a name="prepare-an-iot-hub"></a>Подготовка Центра Интернета вещей

Для выполнения инструкций, приведенных в этом кратком руководстве, также необходим Центр Интернета вещей Azure в подписке Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. Если у вас нет Центра Интернета вещей, создайте его, выполнив инструкции, указанные в [этой статье](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> На этапе общедоступной предварительной версии функции IoT Plug and Play доступны только в центрах Интернета вещей, созданных в таких регионах, как **Центральная часть США**, **Северная Европа** и **Восточная Япония**.

Если вы используете Azure CLI локально, сначала войдите в подписку Azure с помощью `az login`. Если вы используете эти команды в Azure Cloud Shell, то вход выполняется автоматически.

Если вы используете Azure CLI локально, версия `az` должна быть **2.0.73** или выше. В Azure Cloud Shell используется последняя версия. Используйте команду `az --version`, чтобы проверить версию, установленную на компьютере.

Выполните следующую команду, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI в экземпляр:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Выполните приведенную ниже команду, чтобы создать удостоверение устройства в Центре Интернета вещей. Замените заполнители **YourIoTHubName** и **YourDeviceID** своими значениями _имени Центра Интернета вещей_ и _идентификатора устройства_.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Выполните следующую команду, чтобы получить _строку подключения устройства_ для зарегистрированного устройства (запишите ее для использования в будущем):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```