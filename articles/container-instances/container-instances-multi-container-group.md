---
title: Руководство по развертыванию группы с несколькими контейнерами — шаблон
description: В этом руководстве описано, как развернуть группу контейнеров с несколькими контейнерами в службе "экземпляры контейнеров Azure" с помощью шаблона Azure Resource Manager с Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533625"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Руководство. Развертывание группы с несколькими контейнерами с помощью шаблона диспетчер ресурсов

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Диспетчер ресурсов](container-instances-multi-container-group.md)

Служба "Экземпляры контейнеров Azure" поддерживает развертывание нескольких контейнеров в одном узле с использованием [группы контейнеров](container-instances-container-groups.md). Группа контейнеров полезна при создании приложения расширения для ведения журнала, мониторинга или любой другой конфигурации, где службе требуется второй присоединенный процесс.

В этом руководстве описано, как выполнить простую конфигурацию расширения с двумя контейнерами, развернув шаблон Azure Resource Manager с помощью Azure CLI. Вы узнаете, как выполнять такие задачи.

> [!div class="checklist"]
> * Настройка шаблона группы с несколькими контейнерами
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

Шаблон диспетчер ресурсов можно легко адаптировать для сценариев, когда необходимо развернуть дополнительные ресурсы службы Azure (например, общую папку Azure или виртуальную сеть) с группой контейнеров. 

> [!NOTE]
> Многоконтенерные группы сейчас ограничены контейнерами Linux. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Настройка шаблона

Начните с копирования следующего JSON-файла в новый файл с именем `azuredeploy.json`. В Azure Cloud Shell можно использовать Visual Studio Code для создания файла в рабочем каталоге:

```
code azuredeploy.json
```

Этот шаблон Resource Manager определяет группу контейнеров с двумя контейнерами, общедоступным IP-адресом и двумя предоставленными портами. Первый контейнер в группе запускает веб-приложение с выходом в Интернет. Второй контейнер (сопроводительный) осуществляет HTTP-запрос к основному веб-приложению через локальную сеть группы.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Чтобы использовать частный реестр образов контейнеров, добавьте объект в документ JSON в следующем формате: Пример реализации этой конфигурации см. в [справочной документации по шаблонам диспетчер ресурсов ACI][template-reference] .

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

В течение нескольких секунд вы должны получить исходный ответ Azure.

## <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте следующую команду [AZ Container Показать][az-container-show] :

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

Просмотрите выходные данные журнала контейнера с помощью команды [AZ Container Logs][az-container-logs] . Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере указан контейнер `aci-tutorial-app`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Выходные данные:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Чтобы просмотреть журналы для контейнера расширения, выполните аналогичную команду, указав контейнер `aci-tutorial-sidecar`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Выходные данные:

```bash
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

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. Этот пример расширения можно расширить, чтобы активировать оповещение, если получен код HTTP-ответа, отличный от `200 OK`.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы использовали шаблон Azure Resource Manager для развертывания группы с несколькими контейнерами в службе "экземпляры контейнеров Azure". Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка шаблона группы с несколькими контейнерами
> * Развертывание группы контейнеров
> * Просмотр журналов контейнеров

Дополнительные примеры шаблонов см. в статье [шаблоны Azure Resource Manager для службы "экземпляры контейнеров Azure](container-instances-samples-rm.md)".

Группу с несколькими контейнерами также можно указать с помощью [файла YAML](container-instances-multi-container-yaml.md). Из-за более краткой природы формата YAML развертывание с помощью файла YAML является хорошим выбором, если развертывание включает только экземпляры контейнеров.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
