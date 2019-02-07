---
title: Краткое руководство. Развертывание приложения Hello World в Сетке Azure Service Fabric | Документация Майкрософт
description: В этом кратком руководстве показано, как развернуть приложение Сетки Service Fabric в Сетке Azure Service Fabric.
services: service-fabric-mesh
keywords: Не добавляйте и не изменяйте ключевые слова, не посоветовавшись с консультантом SEO.
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2f54f11ffc6db9af4c7d74ef6c0806a8b8b8fe74
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754767"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Краткое руководство. Развертывание приложения Hello World в Сетке Service Fabric

[Сетка Service Fabric](service-fabric-mesh-overview.md) упрощает создание и управление приложениями для микрослужб в Azure без необходимости предоставлять виртуальные машины. В этом кратком руководстве вы создадите приложение Hello World в Azure и опубликуете его в Интернете. Эта операция выполняется при помощи одной команды. Всего за несколько минут вы увидите это представление в своем браузере.

![Приложение Hello World в браузере][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Если у вас еще нет учетной записи Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric 
Для выполнения инструкций этого краткого руководства можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Установите модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив следующие [инструкции](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите в Azure и настройте подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Создать группу ресурсов
Создайте группу ресурсов, в которую будет развертываться приложение. Можно использовать существующую группу ресурсов и пропустить этот шаг. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Развертывание приложения
Создайте приложение в группе ресурсов с помощью команды `az mesh deployment create`.  Выполните следующую команду:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Предыдущая команда развертывает приложение Linux, используя [шаблон linux.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Если необходимо развернуть приложение Windows, используйте [шаблон windows.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). Образы контейнера для ОС Windows больше, чем образы контейнера для Linux, поэтому может потребоваться больше времени для развертывания.

Эта команда создает такой фрагмент кода JSON. В разделе ```outputs``` выходных данных JSON скопируйте свойство ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Эти сведения поступают из раздела ```outputs``` шаблона ARM. Как показано ниже, этот раздел ссылается на ресурс шлюза, чтобы получить общедоступный IP-адрес. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Запуск приложения
После успешного развертывания приложения скопируйте общедоступный IP-адрес службы конечной точки из выхода CLI. Откройте IP-адрес в веб-браузере. Отображается веб-страница с логотипом Сетки Azure Service Fabric.

## <a name="check-the-application-details"></a>Проверка сведений о приложении
Состояние приложения можно проверить с помощью команды `az mesh app show`. Эта команда предоставляет полезную информацию, которую можно отслеживать.

Имя приложения в этом кратком руководстве — `helloWorldApp`, для сбора сведений о приложении, выполните следующую команду.

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Обзор журналов приложений

Изучите журналы для развернутого приложения используя команду `az mesh code-package-log get`.

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда все готово для удаления приложения, запустите команду [​​az group delete][az-group-delete], чтобы удалить группу ресурсов, а также ресурсы приложения и сети, которые она содержит.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о создании и развертывании приложений службы "Сетка Service Fabric" доступны в руководстве.
> [!div class="nextstepaction"]
> [Создание и развертывание веб-приложения на базе нескольких служб](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
