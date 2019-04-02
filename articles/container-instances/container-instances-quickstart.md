---
title: Краткое руководство. Развертывание контейнера Docker в службе "Экземпляры контейнеров Azure". CLI
description: В этом кратком руководстве описано, как быстро развернуть контейнерное веб-приложение, выполняющееся в изолированном экземпляре контейнера Azure с помощью Azure CLI
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8e504a081f8685107871aed920077dd75a70dfa7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368516"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Краткое руководство. Развертывание экземпляра контейнера в Azure с помощью Azure CLI

Служба "Экземпляры контейнеров Azure" позволяет легко и быстро запускать бессерверные контейнеры Docker в Azure. Развертывайте приложения в экземпляр контейнера по требованию, когда вам не нужна полная платформа оркестрации контейнера, такая как Служба Azure Kubernetes.

Пользуясь этим кратким руководством, вы развернете изолированный контейнер Docker и сделаете его приложение доступным по полному доменному имени (FQDN) с помощью Azure CLI. Через несколько секунд после выполнения единой команды развертывания вы можете перейти к приложению, которое выполняется в контейнере.

![Приложение, развернутое в службе "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-app-browser]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][azure-account], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для выполнения инструкций этого краткого руководства можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Если вы хотите использовать его локально, рекомендуется использовать версию 2.0.55 или более позднюю. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Экземпляры контейнеров Azure, как и все ресурсы Azure, должны быть развернуты в группе ресурсов. Группы ресурсов позволяют организовать соответствующие ресурсы Azure и управлять ими.

Для начала создайте группу ресурсов с именем *myResourceGroup* в регионе *eastus* с помощью следующей команды [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Создание контейнера

Теперь, когда вы создали группу ресурсов, можно запустить контейнер в Azure. Чтобы создать экземпляр контейнера с помощью Azure CLI, укажите имя группы ресурсов, имя экземпляра контейнера и образ контейнера Docker для команды [az container create][az-container-create]. В этом кратком руководстве используется общедоступный образ `mcr.microsoft.com/azuredocs/aci-helloworld`. Этот образ содержит небольшое веб-приложение, написанное на Node.js, которое обслуживает статические HTML-страницы.

Вы можете предоставить контейнеры в Интернете, указав один или несколько портов для открытия, метку имени DNS или и то и другое. При работы с этим руководством вы развернете контейнер с меткой имени DNS, чтобы сделать веб-приложение общедоступным.

Выполните следующую команду для запуска экземпляра контейнера. Задайте значение `--dns-name-label`, которое является уникальным в пределах региона Azure, в котором создается экземпляр. Если появится сообщение об ошибке "Метка имени DNS недоступна", попробуйте другую метку имени DNS.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Через несколько секунд вы должны получить ответ из интерфейса командной строки Azure, указывающий, что развертывание завершено. Проверьте состояние контейнера с помощью команды [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

При выполнении команды отображается полное доменное имя (FQDN) и состояние подготовки контейнера.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Когда `ProvisioningState` контейнера перейдет в состояние **успешного запуска**, перейдите к нему в браузере, указав полное доменное имя. Если появится примерно такая веб-страница, поздравляем! Вы успешно развернули приложение, выполняющееся в контейнере Docker в Azure.

![Снимок экрана браузера: приложение, выполняющееся в экземпляре контейнера Azure][aci-app-browser]

Если сначала приложение не отображается, может потребоваться подождать несколько секунд, пока DNS распространится, а затем попробуйте обновить страницу в браузере.

## <a name="pull-the-container-logs"></a>Извлечение журналов контейнера

При необходимости устранения неполадок с контейнером или запущенным в нем приложением (или просто чтобы просмотреть выходные данные) начните с просмотра журналов экземпляра контейнера.

Извлеките журналы экземпляра контейнера с помощью команды [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

В выходных данных будут содержаться журналы для контейнера, а также должны отобразиться запросы HTTP GET, созданные при просмотре приложения в браузере.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Присоединение потоков вывода

Кроме просмотра журналов можно присоединять локальные стандартные потоки вывода и стандартные потоки для вывода сообщений об ошибках контейнера.

Сначала выполните команду [az container attach][az-container-attach], чтобы присоединить локальную консоль к потокам вывода контейнера:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

После присоединения несколько раз обновите страницу в браузере, чтобы создать некоторые дополнительные выходные данные. Когда все будет готово, отсоедините консоль с помощью `Control+C`. Вы должны увидеть результат, аналогичный приведенному ниже:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с контейнером удалите его с помощью команды [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Чтобы проверить, удален ли контейнер, выполните команду [az container list](/cli/azure/container#az-container-list).

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Контейнер **mycontainer** не должен отображаться в выходных данных этой команды. Если у вас нет других контейнеров в группе ресурсов, выходные данные будут отсутствовать.

После завершения работы с группой ресурсов *myResourceGroup* и всеми ресурсами, которые она содержит, удалите эту группу ресурсов с помощью команды [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали экземпляр контейнера Azure с помощью общедоступного образа Microsoft. Если вы хотите создать образ контейнера и развернуть его через частный реестр контейнеров Azure, перейдите к руководству по использованию службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)

Чтобы проверить параметры запуска контейнеров в системе оркестрации Azure, см. краткие руководства по [Службе Azure Kubernetes (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
