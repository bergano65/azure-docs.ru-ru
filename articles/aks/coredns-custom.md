---
title: Настройка Кореднс для службы Kubernetes Azure (AKS)
description: Узнайте, как настроить Кореднс для добавления поддоменов или расширения пользовательских конечных точек DNS с помощью службы Kubernetes Azure (AKS).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 909b32890ea7ff33d6b5b5db3bb55f36f7007c6b
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018660"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Настройка Кореднс с помощью службы Kubernetes Azure

Служба Azure Kubernetes Service (AKS) использует проект [кореднс][coredns] для управления DNS кластера и разрешения для всех кластеров *1.12. x* и более поздних версий. Ранее использовался проект KUBE-DNS. Этот проект KUBE-DNS теперь устарел. Дополнительные сведения о настройке Кореднс и Kubernetes см. в [официальной вышестоящей документации][corednsk8s].

Так как AKS является управляемой службой, вы не можете изменить основную конфигурацию Кореднс ( *корефиле*). Вместо этого используйте Kubernetes *ConfigMap* для переопределения параметров по умолчанию. Чтобы просмотреть AKS кореднс конфигмапс по умолчанию, используйте `kubectl get configmaps --namespace=kube-system coredns -o yaml` команду.

В этой статье показано, как использовать Конфигмапс для базовых параметров настройки Кореднс в AKS.

> [!NOTE]
> `kube-dns`предложены различные [варианты настройки][kubednsblog] через карту конфигурации Kubernetes. Кореднс **не** поддерживает обратную совместимость с KUBE-DNS. Все ранее использовавшиеся настройки должны быть обновлены для использования с Кореднс.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Поддерживаемые и неподдерживаемые

Поддерживаются все встроенные подключаемые модули Кореднс. Не поддерживаются надстройки и подключаемые модули сторонних производителей. 

## <a name="rewrite-dns"></a>Перезапись DNS

Один из сценариев — выполнить перезапись DNS-имени на лету. В следующем примере замените `<domain to be written>` на собственное полное доменное имя. Создайте файл с именем `corednsms.yaml` и вставьте следующий пример конфигурации:

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
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Создайте ConfigMap с помощью команды [kubectl Apply ConfigMap][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f corednsms.yaml
```

Чтобы проверить, применены ли настройки, используйте [kubectl Get конфигмапс][kubectl-get] и укажите *кореднс-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Теперь принудительно перезагрузите ConfigMap Кореднс. Команда [kubectl Delete Pod][kubectl delete] не является обратимой и не приводит к простою. `kube-dns` Удаляются модули, а затем планировщик Kubernetes воссоздает их. Эти новые модули Pod содержат изменение значения TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Указанная выше команда верна. Пока мы изменим `coredns`, развертывание находится под именем **KUBE-DNS** .

## <a name="custom-proxy-server"></a>Настраиваемый прокси-сервер

Если необходимо указать прокси-сервер для сетевого трафика, можно создать ConfigMap для настройки DNS. В следующем примере обновите `proxy` имя и адрес, указав значения для своей среды. Создайте файл с именем `corednsms.yaml` и вставьте следующий пример конфигурации:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Как и в предыдущих примерах, создайте ConfigMap с помощью команды [kubectl Apply ConfigMap][kubectl-apply] и укажите имя манифеста YAML. Затем принудительно Кореднс перезагрузите ConfigMap с помощью [kubectl Delete Pod][kubectl delete] , чтобы планировщик Kubernetes воссоздать их.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Использовать личные домены

Может потребоваться настроить пользовательские домены, которые могут разрешаться только внутри. Например, может потребоваться разрешить личный домен *пуглифе. local*, который не является допустимым доменом верхнего уровня. Без пользовательского домена ConfigMap кластер AKS не сможет разрешить адрес.

В следующем примере измените личный домен и IP-адрес, чтобы направить трафик на значения для вашей среды. Создайте файл с именем `corednsms.yaml` и вставьте следующий пример конфигурации:

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
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Как и в предыдущих примерах, создайте ConfigMap с помощью команды [kubectl Apply ConfigMap][kubectl-apply] и укажите имя манифеста YAML. Затем принудительно Кореднс перезагрузите ConfigMap с помощью [kubectl Delete Pod][kubectl delete] , чтобы планировщик Kubernetes воссоздать их.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Домены-заглушки

Кореднс также можно использовать для настройки доменов-заглушек. В следующем примере обновите личные домены и IP-адреса, указав значения для своей среды. Создайте файл с именем `corednsms.yaml` и вставьте следующий пример конфигурации:

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
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Как и в предыдущих примерах, создайте ConfigMap с помощью команды [kubectl Apply ConfigMap][kubectl-apply] и укажите имя манифеста YAML. Затем принудительно Кореднс перезагрузите ConfigMap с помощью [kubectl Delete Pod][kubectl delete] , чтобы планировщик Kubernetes воссоздать их.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Подключаемый модуль узлов

Так как поддерживаются все встроенные подключаемые модули, это означает, что CoreDNS [Hosts][coredns hosts] также можно настроить подключаемый модуль.

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

## <a name="next-steps"></a>Следующие шаги

В этой статье показано несколько примеров сценариев для настройки Кореднс. Сведения о проекте Кореднс см. [на странице вышестоящего проекта кореднс][coredns].

Дополнительные сведения о основных понятиях сети см. [в разделе Основные понятия сети для приложений в AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[aks-quickstart-cli]: https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough
[aks-quickstart-portal]: https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md