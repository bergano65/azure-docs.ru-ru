---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью Azure CLI
description: Быстрый способ изучить создание частного реестра контейнеров Docker с помощью Azure CLI.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 24bdd52673c65d039166dc28f9f0a0a784569a1a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678706"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Краткое руководство. Создание закрытого реестра контейнеров с помощью Azure CLI

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра реестра контейнеров Azure с помощью Azure CLI. Затем используйте команды Docker, чтобы отправить образ контейнера в реестр, после чего извлеките образ из контейнера и запустите его.

Для этого руководства требуется Azure CLI (рекомендуется версия 2.0.55 и выше). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

Также необходим локально установленный модуль Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе: [macOS][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

Та как в службе Azure Cloud Shell нет всех необходимых компонентов Docker (управляющая программа `dockerd`), ее нельзя использовать в этом руководстве.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом кратком руководстве описано, как создать реестр ценовой категории *Базовый*. Это оптимальный (недорогой) вариант для разработчиков, которые знакомятся с Реестром контейнеров Azure. См. дополнительные сведения об уровнях служб в статье [Номера SKU реестра контейнеров][container-registry-skus].

Создайте экземпляр ACR с помощью команды [az acr create][az-acr-create]. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В следующем примере используется имя *myContainerRegistry007*. Замените его уникальным значением.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

При создании реестра выходные данные выглядят так:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Запишите `loginServer` в выходных данных (в нижнем регистре). Это полное доменное имя реестра. В дальнейшем в этом кратком руководстве будет использоваться `<acrName>` как заполнитель имени реестра контейнеров.

## <a name="log-in-to-registry"></a>Вход в раздел реестра

Перед отправкой и извлечением образов контейнеров необходимо войти в реестр. Чтобы сделать это, используйте команду [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Список образов контейнеров

В следующем примере перечисляются репозитории в реестре.

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```
Result
----------------
hello-world
```

В следующем примере перечисляются теги в репозитории **hello-world**.

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Выходные данные:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Ненужные группу ресурсов, реестр контейнеров и все образы контейнеров можно удалить с помощью команды [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали Реестр контейнеров Azure с использованием Azure CLI, отправили образ контейнера в реестр, а затем извлекли этот образ оттуда и запустили его. Чтобы продолжить работу с Реестром контейнеров Azure, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководства по использованию Реестра контейнеров Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
