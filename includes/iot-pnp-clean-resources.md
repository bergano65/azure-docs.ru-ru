---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152007"
---
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с дополнительными статьями об IoT Plug and Play, вы можете сохранить и повторно задействовать ресурсы, которые вы использовали в этом кратком руководстве. В противном случае вы можете удалить их, чтобы избежать дополнительных расходов.

Вы можете одновременно удалить концентратор и зарегистрированное устройство, удалив всю группу ресурсов с помощью следующей команды для Azure CLI. (Не делайте так, если эти ресурсы совместно используют группу ресурсов с другими нужными вам ресурсами.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Чтобы удалить только Центр Интернета вещей, выполните следующую команду с помощью Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Чтобы удалить только то удостоверение устройства, которое вы зарегистрировали в Центре Интернета вещей, выполните следующую команду с помощью интерфейса командной строки Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Вы также можете удалить клонированные файлы пакета SDK с компьютера, на котором выполняется разработка.