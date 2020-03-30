---
title: Учебник - Развертывание мультиконтейнерной группы - YAML
description: В этом уроке вы узнаете, как развернуть группу контейнеров с несколькими контейнерами в экземплярах контейнеров Azure, используя файл YAML с Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533586"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Учебник: Развертывание группы с несколькими контейнерами с помощью файла YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Менеджер ресурсов](container-instances-multi-container-group.md)
>

Служба "Экземпляры контейнеров Azure" поддерживает развертывание нескольких контейнеров в одном узле с использованием [группы контейнеров](container-instances-container-groups.md). Контейнерная группа полезна при создании бакера приложения для ведения журнала, мониторинга или любой другой конфигурации, где служба нуждается во втором прилагаемом процессе.

В этом уроке вы выполните шаги для запуска простой конфигурации двухконтейнерного бокового вагона, развернув [файл YAML](container-instances-reference-yaml.md) с помощью Azure CLI. Файл YAML предоставляет краткий формат для определения настроек экземпляра. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка файла YAML
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

> [!NOTE]
> Многоконтенерные группы сейчас ограничены контейнерами Linux.

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Настройка файла YAML

Для развертывания многоконтейнерной группы с [группой аз-контейнера создается][az-container-create] команда в Azure CLI, необходимо указать конфигурацию группы контейнеров в файле YAML. Затем передайте файл YAML в качестве параметра команде.

Сначала скопируйте следующий YAML-файл в новый файл с именем **deploy-aci.yaml**. В Azure Cloud Shell вы можете использовать Visual Studio Code для создания файла в рабочем каталоге:

```
code deploy-aci.yaml
```

Этот YAML-файл определяет группу контейнеров с именем "myContainerGroup", имеющую два контейнера, общедоступный IP-адрес и два предоставленных порта. Контейнеры развертываются из общедоступных изображений Майкрософт. Первый контейнер в группе запускает веб-приложение с выходом в Интернет. Второй контейнер, расширение, периодически отправляет HTTP-запросы веб-приложению, запущенному в первом контейнере через локальную сеть группы контейнеров.

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

Чтобы использовать реестр изображений частного контейнера, добавьте `imageRegistryCredentials` свойство в группу контейнеров, при этом значения изменены для среды:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Развертывание группы контейнеров

Создайте группу ресурсов с [группой az, создающей][az-group-create] команду:

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

Просмотрите выходные данные журнала контейнера с помощью команды [az container logs][az-container-logs]. Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере `aci-tutorial-app` указан контейнер.

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

Чтобы увидеть журналы для контейнера для sidecar, `aci-tutorial-sidecar` запустите аналогичную команду, определяющую контейнер.

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

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. Этот пример sidecar может быть расширен, чтобы вызвать оповещение, если он получил код ответа HTTP, кроме. `200 OK`

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы использовали файл YAML для развертывания группы с несколькими контейнерами в экземплярах контейнеров Azure. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> * Настройка файла YAML для группы мультиконтейнеров
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

Вы также можете указать многоконтейнерную группу с помощью [шаблона «Менеджер ресурсов».](container-instances-multi-container-group.md) Шаблон менеджера ресурсов может быть легко адаптирован для сценариев, когда необходимо развернуть дополнительные ресурсы службы Azure с контейнерной группой.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
