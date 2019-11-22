---
title: Использование Azure Dev Spaces для взаимодействия с контейнерами Windows
services: azure-dev-spaces
ms.date: 07/25/2019
ms.topic: conceptual
description: Узнайте, как запускать Azure Dev Spaces в существующем кластере с контейнерами Windows
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры, контейнеры Windows
ms.openlocfilehash: ad91d8e48a9242795d4f5d5cd165e658339ebe08
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280031"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Использование Azure Dev Spaces для взаимодействия с контейнерами Windows

Можно включить Azure Dev Spaces как для новых, так и для существующих пространств имен Kubernetes. Azure Dev Spaces будут запускать и инструментированные службы, работающие в контейнерах Linux. Эти службы также могут взаимодействовать с приложениями, которые работают в контейнерах Windows, в одном пространстве имен. В этой статье показано, как использовать пространства разработки для запуска служб в пространстве имен с существующими контейнерами Windows.

## <a name="set-up-your-cluster"></a>Настройка кластера

В этой статье предполагается, что у вас уже есть кластер с пулами узлов Windows и Linux. Если необходимо создать кластер с пулами узлов Windows и Linux, следуйте приведенным [здесь][windows-container-cli]инструкциям.

Подключитесь к кластеру с помощью [kubectl][kubectl], клиента командной строки Kubernetes. Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере выходных данных показан кластер с узлом Windows и Linux. Перед продолжением убедитесь, что состояние *Готово* для каждого узла.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Примените [таинт][using-taints] к узлам Windows. Таинт на узлах Windows не позволяет пространствам разработки планировать работу контейнеров Linux на узлах Windows. Следующая команда примера команды применяет таинт к узлу Windows *aksnpwin987654* из предыдущего примера.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> При применении таинт к узлу необходимо настроить сопоставление в шаблоне развертывания службы для запуска службы на этом узле. В примере приложения уже настроено [сопоставление][sample-application-toleration-example] с таинт, настроенным в предыдущей команде.

## <a name="run-your-windows-service"></a>Запуск службы Windows

Запустите службу Windows в кластере AKS и убедитесь, что она находится в *запущенном* состоянии. В этой статье используется [пример приложения][sample-application] для демонстрации службы Windows и Linux, работающей в кластере.

Клонировать пример приложения из GitHub и переходить в каталог `dev-spaces/samples/existingWindowsBackend/mywebapi-windows`:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Пример приложения использует [Helm][helm-installed] для запуска службы Windows в кластере. Установите Helm в кластер и предоставьте ему правильные разрешения:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Перейдите в каталог `charts` и запустите службу Windows:

```console
cd charts/
helm install . --namespace dev
```

Приведенная выше команда использует Helm для запуска службы Windows в пространстве имен *dev* . Если у вас нет пространства имен с именем *dev*, оно будет создано.

Используйте команду `kubectl get pods`, чтобы убедиться в том, что служба Windows работает в кластере. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Включить Azure Dev Spaces

Включите пространства разработки в том же пространстве имен, которое использовалось для запуска службы Windows. Следующая команда включает пространства разработки в пространстве имен *dev* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Обновление службы Windows для пространств разработки

При включении пространств разработки в существующем пространстве имен с уже запущенными контейнерами пространства разработки по умолчанию будут пытаться использовать все новые контейнеры, выполняемые в этом пространстве имен. Пространства разработки также попытаются и инструментировать все новые контейнеры, созданные для службы, уже запущенной в пространстве имен. Чтобы запретить пространства разработки инструментирования контейнера, работающего в пространстве имен, добавьте заголовок *без прокси-сервера* в `deployment.yaml`.

Добавьте `azds.io/no-proxy: "true"` в файл `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Используйте `helm list`, чтобы составить список развертывания для службы Windows:

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

В приведенном выше примере имя развертывания — *Гилдед-жаккал*. Обновите службу Windows с помощью новой конфигурации, используя `helm upgrade`:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Так как вы обновили `deployment.yaml`, пространства разработки не будут пытаться выполнять инструментирование службы.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Запуск приложения Linux с Azure Dev Spaces

Перейдите в каталог `webfrontend` и используйте команды `azds prep` и `azds up` для запуска приложения Linux в кластере.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

Команда `azds prep --public` создает диаграмму Helm и файлы dockerfile для приложения. Команда `azds up` запускает службу в пространстве имен.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Вы можете увидеть, как работает служба, открыв общедоступный URL-адрес, который отображается в выходных данных команды аздс up. В этом примере общедоступный URL-адрес `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Перейдите к службе в браузере и щелкните *About (сведения* ) вверху. Убедитесь, что вы видите сообщение от службы *mywebapi* , содержащей версию Windows, используемую контейнером.

![Пример приложения, в котором отображается версия Windows из mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
