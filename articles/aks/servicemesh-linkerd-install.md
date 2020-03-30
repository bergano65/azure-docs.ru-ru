---
title: Установка Linkerd в azure Kubernetes Service (AKS)
description: Узнайте, как установить и использовать Linkerd для создания сетки службы в кластере службы Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593747"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Установка Linkerd в azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] является сеткой с открытым исходным кодом и [проектом инкубации CNCF.][linkerd-cncf] Linkerd — это сверхлегкая сервисная сетка, которая предоставляет функции, включающие управление трафиком, идентификацию службы и безопасность, надежность и наблюдаемость. Для получения дополнительной информации о Linkerd, [Linkerd Architecture][linkerd-architecture] см. [Linkerd FAQ][linkerd-faq]

В этой статье показано, как установить Linkerd. Двоичная `linkerd` система клиента Linkerd установлена на клиентской машине, а компоненты Linkerd устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Эти инструкции ссылаются `stable-2.6.0`на версию Linkerd .
>
> Linkerd `stable-2.6.x` можно запустить против версий `1.13+`Kubernetes . Вы можете найти дополнительные стабильные и края Linkerd версии на [GitHub - Linkerd релизы][linkerd-github-releases].

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Скачать и установить Linkerd linkerd клиент двоичный
> * Установка Linkerd на AKS
> * Проверка установки Linkerd
> * Доступ к панели мониторинга
> * Удаление Linkerd от AKS

## <a name="before-you-begin"></a>Перед началом

Шаги, описанные в этой статье, предполагают, что вы создали `1.13` кластер AKS (Kubernetes `kubectl` и выше, с включенным RBAC) и установили связь с кластером. Дополнительные сведения см. в [кратком руководстве по AKS][aks-quickstart].

Все стручки Linkerd должны быть запланированы для запуска на узлах Linux - эта настройка является по умолчанию в методе установки, описанном ниже, и не требует дополнительной конфигурации.

Эта статья разделяет руководство установки Linkerd на несколько дискретных шагов. Результат такой же по структуре, как и официальный Linkerd начала [руководство][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Установка Linkerd на AKS

Перед установкой Linkerd мы займем предустановку, чтобы определить, можно ли установить плоскость управления на нашем кластере AKS:

```console
linkerd check --pre
```

Вы должны увидеть что-то вроде следующего, чтобы указать, что ваш кластер AKS является действительной целью установки для Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Теперь пришло время установить компоненты Linkerd. Используйте `linkerd` `kubectl` и бинарные файлы для установки компонентов Linkerd в кластер AKS. Пространство `linkerd` имен будет автоматически создано, и компоненты будут установлены в этом пространстве имен.

```console
linkerd install | kubectl apply -f -
```

Linkerd развертывает несколько объектов. Список будет приведен выше из `linkerd install` вывода команды. Развертывание компонентов Linkerd должно занять около 1 минуты, в зависимости от среды кластера.

На этом этапе вы развернули Linkerd в кластер AKS. Чтобы обеспечить успешное развертывание Linkerd, давайте перейдем к следующему разделу для [проверки установки Linkerd.](#validate-the-linkerd-installation)

## <a name="validate-the-linkerd-installation"></a>Проверка установки Linkerd

Подтвердите, что ресурсы успешно созданы. Используйте [kubectl получить svc][kubectl-get] и [kubectl получить стручок][kubectl-get] команды для запроса пространства `linkerd` `linkerd install` имен, где компоненты Linkerd были установлены командой:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Следующий пример вывода показывает службы и стручки (запланированные на узлах Linux), которые теперь должны быть запущены:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd предоставляет команду `linkerd` через клиентбинарный, чтобы подтвердить, что плоскость управления Linkerd была успешно установлена и настроена.

```console
linkerd check
```

Вы должны увидеть что-то вроде следующего, чтобы указать, что ваша установка была успешной:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Доступ к панели мониторинга

Linkerd поставляется с панелью мониторинга, которая обеспечивает понимание сетки службы и рабочих нагрузок. Чтобы получить доступ к `linkerd dashboard` панели мониторинга, используйте команду. Эта команда использует [kubectl port-forward][kubectl-port-forward] для создания безопасного соединения между клиентской машиной и соответствующими стручками в кластере AKS. Затем он автоматически откроет панель мониторинга в браузере по умолчанию.

```console
linkerd dashboard
```

Команда также создаст порт-форвард и вернет ссылку для панелей мониторинга Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Удаление Linkerd от AKS

> [!WARNING]
> Изъятие Linkerd из запущенной системы может привести к проблемам, связанным с трафиком между вашими службами. Убедитесь, что вы сделали положения для вашей системы по-прежнему работают правильно без Linkerd перед началом работы.

Сначала вам нужно удалить прокси-данные плоскости данных. Удалите любые [автоматические аннотации][linkerd-automatic-proxy-injection] прокси-инъекций из областей имен рабочей нагрузки и раскатать развертывание рабочей нагрузки. Рабочие нагрузки больше не должны иметь связанных компонентов плоскости данных.

Наконец, удалите плоскость управления следующим образом:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы изучить дополнительные варианты установки и конфигурации для Linkerd, см.

- [Linkerd - Установка хелма][linkerd-install-with-helm]
- [Linkerd - Многоступенчатая установка для обслуживания привилегий роли][linkerd-multi-stage-installation]

Вы также можете следить за дополнительными сценариями, используя:

- [Linkerd emojivoto демо][linkerd-demo-emojivoto]
- [Linkerd книги демо][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
