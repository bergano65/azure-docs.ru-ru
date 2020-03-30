---
title: Взаимодействие с контейнерами Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Узнайте, как запустить пробелы Azure Dev в существующем кластере с контейнерами Windows
keywords: Пространства Azure Dev, Dev Spaces, Докер, Кубернетес, Лазурный, AKS, Служба Azure Kubernetes, контейнеры, контейнеры для Windows
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240482"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Взаимодействие с контейнерами Windows с помощью пространства Azure Dev

Вы можете включить пространства Azure Dev как в новых, так и в существующих пространствах имен Kubernetes. Azure Dev Spaces будет работать и инструменты услуг, которые работают на linux контейнеров. Эти службы также могут взаимодействовать с приложениями, запускаемыми в контейнерах Windows в том же пространстве имен. В этой статье показано, как использовать Dev Spaces для запуска служб в пространстве имен с существующими контейнерами Windows. В настоящее время нельзя отладить или прикрепить контейнеры Windows с помощью Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Настройка кластера

В этой статье предполагается, что у вас уже есть кластер с пулами узлов Linux и Windows. Если вам нужно создать кластер с пулами узлов Linux и Windows, вы можете следовать инструкциям [здесь.][windows-container-cli]

Подключитесь к кластеру с помощью [kubectl,][kubectl]клиент командной строки Kubernetes. Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере вывод показывает кластер с узлами Windows и Linux. Убедитесь, что статус *готов для* каждого узла перед началом работы.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Примените [запятнание][using-taints] к узлам Windows. Запятнание узлов Windows не позволяет Dev Spaces планировать контейнеры Linux для работы на узлах Windows. В следующем примере команды команда применяет запятнание к узлам Windows *aksnpwin987654* из предыдущего примера.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> При применении запятнания к уему необходимо настроить соответствующую терпимую кондиционацию в шаблоне развертывания службы для запуска службы на этом уде. Приложение образца уже настроено с [соответствующей терпимости][sample-application-toleration-example] к запятнанию, настроенной в предыдущей команде.

## <a name="run-your-windows-service"></a>Запустите службу Windows

Запустите службу Windows в кластере AKS и убедитесь, что он находится в состоянии *запуска.* В этой статье используется [пример приложения][sample-application] для демонстрации службы Windows и Linux, работая в кластере.

Клонировать образец приложения от GitHub `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` и перемещаться в каталоге:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Пример приложения использует [Helm 3][helm-installed] для запуска службы Windows в кластере. Перейдите `charts` в каталог и используйте службу Руля, запускайте службу Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Вышеупомянутая команда использует Шлем для запуска службы Windows в пространстве имен *разработчиков.* Если у вас нет пространства имен с именем *dev,* оно будет создано.

Используйте `kubectl get pods` команду для проверки того, что служба Windows работает в кластере. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Включить пространства Azure Dev

Включить Dev Spaces в том же пространстве имен, что и для запуска службы Windows. Следующая команда позволяет Dev Spaces в пространстве имен *разработчиков:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Обновление службы Windows для Dev Spaces

При вводе Dev Spaces в существующем пространстве имен с контейнерами, которые уже запущены, по умолчанию Dev Spaces попытается сделать инструмент любые новые контейнеры, которые работают в этом пространстве имен. Dev Spaces также попытается сделать инструмент ы любыми новыми контейнерами, созданными для обслуживания, уже работающими в пространстве имен. Чтобы предотвратить дев пробелы от инструментирования контейнера работает в вашем `deployment.yaml`пространстве имен, добавить *не-прокси* заголовок в .

Добавить `azds.io/no-proxy: "true"` `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` в файл:

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

Используйте `helm list` для рассылки развертывания для службы Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

В приведенном выше примере название развертывания — *служба windows.* Обновление службы Windows с `helm upgrade`помощью новой конфигурации:

```cmd
helm upgrade windows-service . --namespace dev
```

Так как `deployment.yaml`вы обновили ваш, Dev пространства не будет пытаться инструмент вашего сервиса.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Запустите приложение Linux с помощью Azure Dev Spaces

Перейдите `webfrontend` в каталог и `azds prep` `azds up` используйте команды и команды для запуска приложения Linux в кластере.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Команда `azds prep --enable-ingress` генерирует диаграмму Хелма и Dockerfiles для вашего приложения.

> [!TIP]
> Azure Dev Spaces использует [Dockerfile и диаграмму Helm](../how-dev-spaces-works-prep.md#prepare-your-code) проекта для сборки и выполнения кода. Но вы можете изменить эти файлы, если нужно определить другой способ сборки и запуска проекта.

Команда `azds up` запускает службу в пространстве имен.

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

Вы можете увидеть службу, запущенную, открыв общедоступный URL-адрес, который отображается в выводе из команды azds up. В этом примере общедоступный URL-адрес — `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Перейдите к службе в браузере и нажмите на *О'кей* в верхней части. Убедитесь, что вы видите сообщение от службы *mywebapi,* содержащее версию Windows контейнер использует.

![Пример приложения, показывающего версию Windows от mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
