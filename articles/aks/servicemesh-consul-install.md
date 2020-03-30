---
title: Установка консула в Azure Kubernetes Service (AKS)
description: Узнайте, как установить и использовать Consul для создания сетки службы в кластере службы Azure Kubernetes (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273739"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Установка и использование консула в службе Azure Kubernetes (AKS)

[Consul][consul-github] — это сетка служб с открытым исходным кодом, которая обеспечивает набор ключевых функций в микрослужбах в кластере Kubernetes. Эти функции включают обнаружение служб, проверку работоспособности, сегментацию служб и наблюдаемость. Для получения дополнительной информации о консуле, смотрите официальную [Что такое Консул?][consul-docs-concepts] документация.

В этой статье показано, как установить Консула. Компоненты Консула устанавливаются в кластер Кубернете на АКС.

> [!NOTE]
> Эти инструкции ссылаются `1.6.0`на версию Consul, и использовать по крайней мере версия `2.14.2`Helm .
>
> Консул релизы `1.6.x` могут быть запущены против `1.13+`версий Kubernetes . Вы можете найти дополнительные версии консула на [GitHub - Консул релизы][consul-github-releases] и информацию о каждом из релизов на [Консул- Релиз Отмечает][consul-release-notes].

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Установка компонентов консула на AKS
> * Проверка установки консула
> * Удалить консула из AKS

## <a name="before-you-begin"></a>Перед началом

Шаги, описанные в этой статье, предполагают, что вы создали `1.13` кластер AKS (Kubernetes `kubectl` и выше, с включенным RBAC) и установили связь с кластером. Дополнительные сведения см. в [кратком руководстве по AKS][aks-quickstart]. Убедитесь, что в кластере есть по крайней мере 3 узла в пуле узлов Linux.

Вам понадобится [Хелм,][helm] чтобы следовать этим инструкциям и установить консула. Рекомендуется, чтобы в кластере была правильно установлена и настроена последняя стабильная версия. Если вам нужна помощь в установке Helm, то смотрите [руководство установки AKS Helm.][helm-install] Все стручки Консула также должны быть запланированы для запуска на узлах Linux.

Эта статья разделяет руководство установки Консула в несколько дискретных шагов. Конечный результат такой же по структуре, как и официальное [руководство][consul-install-k8]по установке консула.

### <a name="install-the-consul-components-on-aks"></a>Установка компонентов консула на AKS

Начнем с загрузки `v0.10.0` версии диаграммы Consul Helm. Эта версия диаграммы включает `1.6.0`в себя версию Консула .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Используйте шлем и `consul-helm` загруженную диаграмму для `consul` установки компонентов Consul в пространство имен в кластере AKS. 

> [!NOTE]
> **Параметры установки**
> 
> Мы используем следующие варианты в рамках нашей установки:
> - `connectInject.enabled=true`- позволяют прокси- вводить сявок в стручки
> - `client.enabled=true`- позволяют клиентам Consul работать на каждом узлах
> - `client.grpc=true`- включить gRPC слушателя для подключенияInject
> - `syncCatalog.enabled=true`- синхронизация Kubernetes и консульских услуг
>
> **Селекторы узлов**
>
> Консул в настоящее время должен быть запланирован для запуска на узлах Linux. Если в кластере есть узлы Windows Server, необходимо убедиться, что стручки Консула будут запущены только на узлах Linux. Мы будем использовать [селекторы узлов,][kubernetes-node-selectors] чтобы убедиться, что стручки запланированы на правильные узлы.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Диаграмма `Consul` Хелма развертывает несколько объектов. Вы можете увидеть список из `helm install` вывода команды выше. Развертывание компонентов Consul может занять около 3 минут, в зависимости от среды кластера.

На этом этапе вы направили консула в кластер AKS. Чтобы обеспечить успешное развертывание Consul, давайте перейдем к следующему разделу для проверки установки Консула.

## <a name="validate-the-consul-installation"></a>Проверка установки консула

Подтвердите, что ресурсы успешно созданы. Используйте [kubectl получить svc][kubectl-get] и [kubectl получить стручок][kubectl-get] команды для запроса `consul` пространства `helm install` имен, где компоненты консула были установлены командой:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Следующий пример вывода показывает службы и стручки (запланированные на узлах Linux), которые теперь должны быть запущены:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Все стручки должны показать статус `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если для групп pod отображаются проблемы, выполните команду [describe pod][kubectl-describe], чтобы просмотреть выходные данные и состояние.

## <a name="accessing-the-consul-ui"></a>Доступ к UI-доступу к консулу

UI Consul был установлен в нашей вышеуказанной установке и обеспечивает конфигурацию на основе uI для Консула. UI для консула не разоблачается публично через внешний IP-адрес. Чтобы получить доступ к пользовательскому интерфейсу Consul, используйте команду [kubectl port-forward.][kubectl-port-forward] Эта команда создает безопасное соединение между клиентской машиной и соответствующим стручками в кластере AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Теперь вы можете открыть браузер `http://localhost:8080/ui` и указать его, чтобы открыть интерфейс Консула. При открытии uI следует видеть следующее:

![Консул UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Удалить консула из AKS

> [!WARNING]
> Изъятие консула из запущенной системы может привести к проблемам, связанным с трафиком между вашими службами. Убедитесь, что вы сделали положения для вашей системы по-прежнему работают правильно без консула, прежде чем продолжить.

### <a name="remove-consul-components-and-namespace"></a>Удалить компоненты консула и пространство имен

Чтобы удалить консула из кластера AKS, используйте следующие команды. Команды `helm delete` удалит `consul` диаграмму, `kubectl delete namespace` а команда `consul` удалит пространство имен.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы изучить больше вариантов установки и конфигурации для Консула, см.

- [Консул - Руководство по установке helm][consul-install-k8]
- [Консул - Параметры установки helm][consul-install-helm-options]

Вы также можете следить за дополнительными сценариями, используя:

- [Заявка на консульс-пример][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
