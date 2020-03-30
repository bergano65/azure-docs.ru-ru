---
title: Настройка CoreDNS для сервиса Azure Kubernetes (AKS)
description: Узнайте, как настроить CoreDNS для добавления поддоменов или расширения пользовательских конечных точек DNS с помощью службы Azure Kubernetes (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595830"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Настройка CoreDNS с использованием Службы Azure Kubernetes

Служба Azure Kubernetes (AKS) использует проект [CoreDNS][coredns] для управления и разрешения кластеров DNS со всеми *кластерами 1.12.x* и более высокими. Ранее использовался проект kube-dns. Этот проект кубе-дн в настоящее время обесточен. Для получения дополнительной информации о настройке CoreDNS и Kubernetes, [см.][corednsk8s]

Поскольку AKS является управляемой службой, вы не можете изменить основную конфигурацию для CoreDNS *(CoreFile).* Вместо этого вы используете *Kubernetes ConfigMap* для переопределения настроек по умолчанию. Чтобы увидеть по умолчанию AKS CoreDNS `kubectl get configmaps --namespace=kube-system coredns -o yaml` ConfigMaps, используйте команду.

В этой статье показано, как использовать ConfigMaps для основных вариантов настройки CoreDNS в AKS. Этот подход отличается от настройки CoreDNS в других контекстах, таких как использование CoreFile. Проверьте версию CoreDNS, которая работает, так как значения конфигурации могут меняться между версиями.

> [!NOTE]
> `kube-dns`предлагают различные [варианты настройки][kubednsblog] через конфигурационную карту Kubernetes. CoreDNS **не** совместим с кубе-днами. Любые настройки, которые вы использовали ранее, должны быть обновлены для использования с Помощью CoreDNS.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Что поддерживается/не поддерживается

Все встроенные плагины CoreDNS поддерживаются. Нет дополнительных /третьих плагинов не поддерживаются.

## <a name="rewrite-dns"></a>Переписать DNS

Один из сценариев, который у вас есть, это выполнение перезаписи имен DNS на лету. В следующем примере `<domain to be written>` замените собственное полностью квалифицированное доменное имя. Создайте имя `corednsms.yaml` файла и вставьте следующую конфигурацию примера:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Создайте ConfigMap с помощью команды [configmap kubectl][kubectl-apply] и укажите название вашего манифеста YAML:

```console
kubectl apply -f corednsms.yaml
```

Для проверки применения настроек, используйте [кубектли и][kubectl-get] укажите свой *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Теперь заставить CoreDNS перезагрузить ConfigMap. Команда [kubectl удалить стручок][kubectl delete] не является разрушительным и не вызывает время простоя. Стручки `kube-dns` удаляются, и Kubernetes Scheduler затем воссоздает их. Эти новые стручки содержат изменение значения TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Приведенная выше команда верна. В то время `coredns`как мы меняемся, развертывание находится под именем **kube-dns.**

## <a name="custom-forward-server"></a>Пользовательский передний сервер

Если вам нужно указать передний сервер для сетевого трафика, можно создать ConfigMap для настройки DNS. В следующем примере `forward` обновите имя и адрес значениями для собственной среды. Создайте имя `corednsms.yaml` файла и вставьте следующую конфигурацию примера:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Как и в предыдущих примерах, создайте ConfigMap, используя [команду kubectl, применить команду конфигурации][kubectl-apply] и указать имя вашего манифеста YAML. Затем заставить CoreDNS перезагрузить ConfigMap с помощью [стручка для удаления kubectl][kubectl delete] для Kubernetes Scheduler, чтобы воссоздать их:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Используйте пользовательские домены

Вы можете настроить пользовательские домены, которые могут быть решены только внутри страны. Например, вы можете решить пользовательский домен *puglife.local*, который не является действительным доменом верхнего уровня. Без пользовательского домена ConfigMap кластер AKS не может решить этот адрес.

В следующем примере обновите пользовательский домен и IP-адрес, чтобы направить трафик на значения для вашей собственной среды. Создайте имя `corednsms.yaml` файла и вставьте следующую конфигурацию примера:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Как и в предыдущих примерах, создайте ConfigMap, используя [команду kubectl, применить команду конфигурации][kubectl-apply] и указать имя вашего манифеста YAML. Затем заставить CoreDNS перезагрузить ConfigMap с помощью [стручка для удаления kubectl][kubectl delete] для Kubernetes Scheduler, чтобы воссоздать их:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub домены

CoreDNS также может быть использован для настройки доменов. В следующем примере обновите пользовательские домены и IP-адреса со значениями для вашей собственной среды. Создайте имя `corednsms.yaml` файла и вставьте следующую конфигурацию примера:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Как и в предыдущих примерах, создайте ConfigMap, используя [команду kubectl, применить команду конфигурации][kubectl-apply] и указать имя вашего манифеста YAML. Затем заставить CoreDNS перезагрузить ConfigMap с помощью [стручка для удаления kubectl][kubectl delete] для Kubernetes Scheduler, чтобы воссоздать их:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Хосты плагин

Поскольку все встроенные плагины поддерживаются, это означает, что плагин CoreDNS [Hosts][coredns hosts] также доступен для настройки:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Включить журнал для отладки dNS-запроса 

Чтобы включить журнал DNS запросов, примените следующую конфигурацию в настраиваемом на сердце ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показаны некоторые примеры сценариев настройки CoreDNS. Для получения информации о проекте CoreDNS [см.][coredns]

Чтобы узнать больше о основных концепциях сети, [см.][concepts-network]

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
