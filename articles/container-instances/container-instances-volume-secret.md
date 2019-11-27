---
title: Подключить секретный том к группе контейнеров
description: Узнайте, как подключить том secret для хранения конфиденциальной информации, чтобы обеспечить доступ экземплярам контейнеров
ms.topic: article
ms.date: 07/19/2018
ms.openlocfilehash: 7f212a5090923a7d7bf00fc8ac78299f2edcc9c1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533190"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Подключение тома secret в службе "Экземпляры контейнеров Azure"

Используйте том *secret*, чтобы предоставить конфиденциальную информацию контейнерам в группе контейнеров. В томе *secret* в файлах хранятся секреты, к которым у контейнеров в группе контейнеров есть доступ. С помощью секретов в томе *secret* можно избежать добавления конфиденциальных данных, например ключей SSH или учетных данных базы данных, в коде приложения.

Все *секретные* тома поддерживаются [тмпфс][tmpfs], файловой системой, поддерживающей ОЗУ; их содержимое никогда не записывается в долговременное хранилище.

> [!NOTE]
> Тома *secret* сейчас ограничены контейнерами Linux. Сведения о том, как передавать переменные безопасной среды для контейнеров Windows и Linux см. в статье [Настройка переменных среды](container-instances-environment-variables.md). Пока мы работаем над переносом всех компонентов в контейнеры Windows, в [обзоре](container-instances-overview.md#linux-and-windows-containers)можно найти различия в текущих платформах.

## <a name="mount-secret-volume---azure-cli"></a>Подключение тома secret в Azure CLI

Чтобы развернуть контейнер с одним или несколькими секретами с помощью Azure CLI, включите параметры `--secrets` и `--secrets-mount-path` в команду [AZ Container Create][az-container-create] . В этом примере том *secret*, состоящий из двух секретов, "mysecret1" и "mysecret2", подключается к `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

В следующем выводе [AZ Container Exec][az-container-exec] показано, как открыть оболочку в работающем контейнере, вывести список файлов на секретном томе, а затем отобразить их содержимое:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Подключение тома secret с помощью YAML

Вы также можете развернуть группы контейнеров с помощью Azure CLI и [шаблона YAML](container-instances-multi-container-yaml.md). При развертывании группы контейнеров, состоящей из нескольких контейнеров, рекомендуется использовать шаблон YAML.

При развертывании с помощью шаблона YAML значения секретов в шаблоне должны быть в **кодировке Base64**. Тем не менее значения секретов отображаются в виде обычного текста в файлах в контейнере.

Следующий шаблон YAML определяет группу контейнеров с одним контейнером, где том *secret* подключается к `/mnt/secrets`. Том secret имеет два секрета, "mysecret1" и "mysecret2".

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Чтобы выполнить развертывание с помощью шаблона YAML, сохраните предыдущую YAML в файл с именем `deploy-aci.yaml`, а затем выполните команду [AZ Container Create][az-container-create] с параметром `--file`:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Подключение тома secret с помощью Resource Manager

Помимо развертывания с помощью CLI и шаблона YAML группу контейнеров также можно развернуть с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. При развертывании с помощью шаблона Resource Manager значения секретов в шаблоне должны быть в **кодировке Base64**. Тем не менее значения секретов отображаются в виде обычного текста в файлах в контейнере.

Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *secret*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Следующий шаблон Resource Manager определяет группу контейнеров с одним контейнером, где том *secret* подключается к `/mnt/secrets`. Том secret имеет два секрета, "mysecret1" и "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Чтобы выполнить развертывание с помощью шаблона диспетчер ресурсов, сохраните предыдущий JSON в файл с именем `deploy-aci.json`, а затем выполните команду [AZ Group Deployment Create][az-group-deployment-create] с параметром `--template-file`:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Дополнительная информация

### <a name="volumes"></a>Тома

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Подключение файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure")
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Подключение тома emptyDir в службе "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Защита переменных среды

Другой способ предоставления конфиденциальных данных в контейнеры (включая контейнеры Windows) — воспользоваться [защищенными переменными среды](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
