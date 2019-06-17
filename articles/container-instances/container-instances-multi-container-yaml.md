---
title: Руководство. развертывание группу с несколькими контейнерами в экземплярах контейнеров Azure — YAML
description: В этом руководстве вы узнаете, как развернуть группу контейнеров с несколькими контейнерами в экземплярах контейнеров Azure с помощью yaml-файл с помощью Azure CLI.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149080"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Руководство по Развернуть группу с несколькими контейнерами, с помощью файла YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Служба "Экземпляры контейнеров Azure" поддерживает развертывание нескольких контейнеров в одном узле с использованием [группы контейнеров](container-instances-container-groups.md). Группа контейнеров полезно при создании сопроводительного приложения для ведения журнала, мониторинга или любой другой конфигурации когда службе требуется еще один прикрепленный процесс.

В этом руководстве вы выполните действия, чтобы запустить простой контейнер двух конфигурации сопроводительного приложения, развернув yaml-файл, с помощью Azure CLI. Yaml-файл предоставляет краткий формат для указания параметров экземпляра. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка файла YAML
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

> [!NOTE]
> Многоконтенерные группы сейчас ограничены контейнерами Linux.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Настройка файла YAML

Чтобы развернуть группу с несколькими контейнерами с [создать контейнер az] [ az-container-create] команды в интерфейсе командной строки Azure, необходимо указать конфигурацию контейнера группы в yaml-файл. Затем передается как параметр yaml-файл в команду.

Сначала скопируйте следующий YAML-файл в новый файл с именем **deploy-aci.yaml**. В Azure Cloud Shell можно использовать Visual Studio Code для создания файла в рабочем каталоге:

```
code deploy-aci.yaml
```

Этот YAML-файл определяет группу контейнеров с именем "myContainerGroup", имеющую два контейнера, общедоступный IP-адрес и два предоставленных порта. Эти контейнеры развертываются из общедоступных образов Microsoft. Первый контейнер в группе запускает веб-приложение с выходом в Интернет. Второй контейнер, расширение, периодически отправляет HTTP-запросы веб-приложению, запущенному в первом контейнере через локальную сеть группы контейнеров.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Чтобы использовать частный реестр образов контейнеров, добавьте `imageRegistryCredentials` свойства для группы контейнеров, со значениями, изменены для вашей среды:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Развертывание группы контейнеров

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Выполните развертывание группы контейнеров с помощью команды [az container create][az-container-create], передав YAML-файл как аргумент:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

В течение нескольких секунд вы должны получить исходный ответ Azure.

## <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте команду [az container show][az-container-show] ниже.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Если вы хотите просмотреть запущенное приложение, перейдите к его IP-адресу в своем браузере. Например, в этом примере выходных данных используется IP-адрес `52.168.26.124`:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Просмотр журналов контейнеров

Просмотрите выходные данные журнала контейнера с помощью команды [az container logs][az-container-logs]. Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере `aci-tutorial-app` указан контейнера.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Выходные данные:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Чтобы просмотреть журналы для сопроводительного приложения контейнера, выполните аналогичные команды указания `aci-tutorial-sidecar` контейнера.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Выходные данные:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. В этом примере расширение может быть развернут для активации оповещения в том случае, если он получен код ответа HTTP, отличных от `200 OK`.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы использовали файл YAML для развертывания группу с несколькими контейнерами в экземплярах контейнеров Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка файла YAML для многоконтейнерной группы
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

Можно также указать многоконтейнерной группы с помощью [шаблона Resource Manager](container-instances-multi-container-group.md). Шаблон Resource Manager может быть легко адаптирован для сценариев, когда необходимо развернуть ресурсы дополнительных служб Azure с помощью группы контейнеров.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
