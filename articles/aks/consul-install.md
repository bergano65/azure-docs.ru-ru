---
title: Установка Hashicorp Consul в службе Kubernetes Azure (AKS)
description: Узнайте, как установить и использовать Consul для создания сетки службы в кластере службы Kubernetes Azure (AKS).
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189228"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Установка и использование Consul Connect в службе Kubernetes Azure (AKS)

[Consul][consul-github] — это сетка служб с открытым исходным кодом, которая предоставляет набор ключевых функций для микрослужб в кластере Kubernetes. Эти функции включают обнаружение служб, проверку работоспособности, сегментацию службы и наблюдаемость. Дополнительные сведения о Consul см. в официальной документации о [Consul?][consul-docs-concepts] .

В этой статье показано, как установить Consul. Компоненты Consul устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Эти инструкции ссылаются на версию `1.5`Consul.
>
> Выпуски Consul `1.5.x` были протестированы группой Hashicorp по сравнению с `1.12`версиями `1.14`Kubernetes `1.14`,,. Дополнительные версии Consul можно найти в выпусках [GitHub-Consul][consul-github-releases] , а сведения о каждой из выпусков см. в заметках о выпуске [Consul][consul-release-notes].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Установка компонентов Consul на AKS
> * Проверка установки Consul
> * Удаление Consul из AKS

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этой статье, предполагается, что вы создали `1.11` кластер AKS (Kubernetes и выше, с включенным RBAC `kubectl` ) и установили подключение к кластеру. Если вам нужна помощь по любому из этих элементов, см. [Краткое руководство по AKS][aks-quickstart].

Вам потребуется [Helm][helm] , чтобы выполнить эти инструкции и установить Consul. Рекомендуется правильно установить и настроить в кластере версию `2.12.2` или более позднюю. Если вам нужна помощь с установкой Helm, см. [руководство по установке AKS Helm][helm-install]. Все Consul Pod также должны быть запланированы для запуска на узлах Linux.

В этой статье Руководство по установке Consul разделено на несколько отдельных этапов. Конечный результат аналогичен в структуре в качестве официального [руководства][consul-install-k8]по установке Consul.

### <a name="install-the-consul-components-on-aks"></a>Установка компонентов Consul на AKS

Сначала нужно клонировать официальный HashiCorp Consul в репозитории Kubernetes GitHub.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Затем нужно будет создать пользовательский файл значений Helm для установки Consul. Перед установкой Consul создайте следующий файл пользовательских значений.

```bash
vim consul-values.yaml
```

Затем скопируйте и вставьте следующие значения в файл Consul-Values. YAML.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Теперь, когда у нас есть файл пользовательских значений, мы можем установить Consul в наш кластер AKS.

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
`Consul` Helm диаграмма развертывает несколько объектов. Список можно просмотреть на основе выходных данных `helm install` команды выше. Для завершения развертывания компонентов Consul в зависимости от среды кластера может потребоваться от 4 до 5 минут.

> [!NOTE]
> Все Consul Pod должны быть запланированы для запуска на узлах Linux. При наличии пулов узлов Windows Server в дополнение к пулам узлов Linux в кластере убедитесь, что на узлах Linux запланировано выполнение всех Consulных модулей.

На этом этапе вы развернули Consul в кластере AKS. Чтобы убедиться в успешном развертывании Consul, перейдем к следующему разделу, чтобы проверить установку Consul.

## <a name="validate-the-consul-installation"></a>Проверка установки Consul

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl Get SVC][kubectl-get] для просмотра выполняющихся служб. Запросите `consul`пространствоимен , где компоненты Consul были установлены на диаграмме Helm: `consul`

```console
kubectl get svc --namespace consul
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Затем убедитесь, что созданы все необходимые группы pod. Используйте команду [kubectl Get][kubectl-get] Pod и повторите запрос к `consul` пространству имен:

```console
kubectl get pods --namespace consul
```

В следующем примере выходных данных представлены запущенные группы pod:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Все модули Pod должны отображать состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если какая либо из модулей Pod сообщает о возникшей ошибке, используйте команду [kubectl][kubectl-describe] , описывающую, чтобы проверить их выходные данные и состояние.

## <a name="accessing-the-consul-ui"></a>Доступ к пользовательскому интерфейсу Consul

В нашей настройке был установлен пользовательский интерфейс Consul, предоставляющий конфигурацию на основе пользовательского интерфейса для Consul. Пользовательский интерфейс для Consul недоступен через внешний IP-адрес. Чтобы получить доступ к пользовательским интерфейсам надстройки, используйте команду [переадресации порта kubectl][kubectl-port-forward] . Эта команда создает безопасное подключение между вашим клиентским компьютером и соответствующим Pod в кластере AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Теперь можно открыть браузер и указать `http://localhost:8080/ui` , чтобы открыть пользовательский интерфейс Consul. При открытии пользовательского интерфейса вы должны увидеть следующее:

![Пользовательский интерфейс Consul](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Удаление Consul из AKS

> [!WARNING]
> Удаление Consul из работающей системы может привести к проблемам с трафиком между Вашими службами. Перед продолжением убедитесь, что вы выполнили предварительные условия для правильной работы системы без Consul.

### <a name="remove-consul-components-and-namespace"></a>Удалить компоненты и пространство имен Consul

Чтобы удалить Consul из кластера AKS, используйте следующие команды. Команда удалит `kubectl delete ns` диаграмму, и команда `consul` удалит пространство имен. `consul` `helm delete`

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об установке и параметрах конфигурации для Consul см. в следующих официальных статьях по Consul:

- [Consul — руководства по установке Helm][consul-install-k8]
- [Параметры установки Consul-Helm][consul-install-helm-options]

Вы также можете выполнить дополнительные сценарии, используя [пример приложения Consul][consul-app-example].

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
