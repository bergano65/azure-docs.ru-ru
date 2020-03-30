---
title: Аутентификат из кластера Кубернете
description: Узнайте, как предоставить кластерkubernetes доступ к изображениям в реестре контейнеров Azure, создав секрет притяжения с помощью принципа службы
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154898"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Вытяните изображения из реестра контейнеров Azure в кластер Kubernetes

Реестр контейнеров Azure можно использовать в качестве источника изображений контейнеров с любым кластером Kubernetes, включая «местные» кластеры Kubernetes, такие как [minikube](https://minikube.sigs.k8s.io/) и [kind.](https://kind.sigs.k8s.io/) В этой статье показано, как создать секрет притяжения Kubernetes на основе принципа службы Active Directory Azure. Затем используйте секрет для вытягивания изображений из реестра контейнеров Azure в развертывании Kubernetes.

> [!TIP]
> Если вы используете управляемую [службу Azure Kubernetes,](../aks/intro-kubernetes.md)вы также можете [интегрировать кластер](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) с целевым реестром контейнеров Azure для вытягивания изображений. 

В этой статье предполагается, что вы уже создали частный реестр контейнеров Azure. Кроме того, необходимо, чтобы кластер Kubernetes `kubectl` работал и был доступен с помощью инструмента командной строки.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Если вы не сохранили или не запоминают основной пароль службы, вы можете сбросить его с помощью команды [сбросить учетные данные az ad sp:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Эта команда возвращает новый, действительный пароль для основного обслуживания.

## <a name="create-an-image-pull-secret"></a>Создание секрета притяжения изображения

Kubernetes использует *секрет притяжения изображения* для хранения информации, необходимой для проверки подлинности в вашем реестре. Чтобы создать секрет притяжения для реестра контейнеров Azure, вы предоставляете основной идентификатор службы, пароль и URL-адрес реестра. 

Создайте секрет притяжения `kubectl` изображения со следующей командой:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
где:

| Значение | Описание |
| :--- | :--- |
| `secret-name` | Название изображения тянуть секрет, например, *акр-секретно* |
| `namespace` | Kubernetes namespace, чтобы положить секрет в <br/> Только необходимо, если вы хотите разместить секрет в пространстве имен, кроме пространства имен по умолчанию |
| `container-registry-name` | Имя вашего реестра контейнеров Azure |
| `service-principal-ID` | Идентификатор основного обслуживания, который будет использоваться Kubernetes для доступа к вашему реестру |
| `service-principal-password` | Основной пароль службы |

## <a name="use-the-image-pull-secret"></a>Используйте секрет притяжения изображения

После того как вы создали секрет притяжения изображения, вы можете использовать его для создания стручков И развертывания Kubernetes. Укажите имя секрета `imagePullSecrets` в файле развертывания. Пример:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

В предыдущем примере это имя изображения, которое можно вытащить `acr-secret` из реестра контейнеров Azure, и это имя секрета притяжения, `your-awesome-app:v1` созданного для доступа к реестру. При развертывании стручка Kubernetes автоматически вытягивает изображение из реестра, если оно еще не присутствует в кластере.


## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о работе с принципами обслуживания и реестром контейнеров Azure можно узнать в [реестре контейнеров Azure с принципами обслуживания](container-registry-auth-service-principal.md)
* Подробнее о секретах вытягивания изображений читайте в [документации Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset