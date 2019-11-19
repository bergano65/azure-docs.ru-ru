---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2768c2d4cef68dcf25e25c047aaa69653af5e0b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170872"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

[Istio][istio-github] — это сетка служб с открытым исходным кодом, которая предоставляет набор ключевых функций для микрослужб в кластере Kubernetes. включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. Дополнительные сведения о Istio см. в официальной документации о [Istio?][istio-docs-concepts] .

В этой статье описано, как установить Istio. Двоичный файл клиента Istio `istioctl` устанавливается на клиентский компьютер, а компоненты Istio устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Следующие инструкции ссылаются на Istio версии `1.4.0`.
>
> Istio выпуски `1.4.x` были протестированы группой Istio по сравнению с Kubernetes версиями `1.13`, `1.14``1.15`. Дополнительные версии Istio можно найти в разделе [GitHub-Istio][istio-github-releases], сведения о каждой из выпусков в [Istio News][istio-release-notes] и поддерживаемых версиях Kubernetes в [Istio общие вопросы и ответы][istio-faq].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Скачивание и установка двоичного файла клиента Istio истиоктл
> * Установка Istio на AKS
> * Проверка установки Istio
> * Доступ к надстройкам
> * Удаление Istio из AKS

## <a name="before-you-begin"></a>Перед началом работы

В шагах, описанных в этой статье, предполагается, что вы создали кластер AKS (Kubernetes `1.13` и выше, с включенным RBAC) и установили `kubectl` подключение к кластеру. Если вам нужна помощь по любому из этих элементов, см. [Краткое руководство по AKS][aks-quickstart].

Убедитесь, что вы прочитали документацию по [производительности и масштабируемости Istio](https://istio.io/docs/concepts/performance-and-scalability/) , чтобы понять требования к дополнительным ресурсам для запуска Istio в кластере AKS. Требования к основному и оперативной памяти будут зависеть от конкретной рабочей нагрузки. Выберите подходящее количество узлов и размер виртуальной машины для настройки.

В этой статье инструкции по установке Istio разделены на несколько этапов. Конечный результат аналогичен в структуре в качестве официального [руководства][istio-install-istioctl]по установке Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Установка компонентов Istio на AKS

Мы будем устанавливать [Grafana][grafana] и [киали][kiali] в рамках нашей Istio установки. Grafana предоставляет панели мониторинга для аналитики и мониторинга, а Киали — панель мониторинга сетки служб. В нашей установке каждому из этих компонентов требуются учетные данные, которые должны быть предоставлены в качестве [секрета][kubernetes-secrets].

Прежде чем можно будет установить компоненты Istio, необходимо создать секреты для Grafana и Киали. Эти секреты должны быть установлены в пространство имен `istio-system`, которое будет использоваться Istio, поэтому нам нужно также создать пространство имен. При создании пространства имен с помощью `kubectl create` необходимо использовать параметр `--save-config`, чтобы установщик Istio мог выполнить `kubectl apply` в этом объекте в будущем.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Установка компонентов Istio

Теперь, когда мы успешно создали секреты Grafana и Киали в нашем кластере AKS, пришло время установить компоненты Istio. 

Подход к установке [Helm][helm] для Istio будет устаревшим в будущем. Новый подход к установке для Istio использует `istioctl` клиентский двоичный файл, [профили конфигурации Istio][istio-configuration-profiles], а также новую [спецификацию и API-интерфейс Istio плоскости управления][istio-control-plane]. Этот новый подход мы будем использовать для установки Istio.

> [!NOTE]
> Istio в настоящее время необходимо запланировать запуск на узлах Linux. Если в кластере есть узлы Windows Server, необходимо убедиться, что Istio Pods планируется запускать только на узлах Linux. Мы будем использовать [селекторы узлов][kubernetes-node-selectors] , чтобы убедиться, что для модулей Pod запланированы правильные узлы.

> [!CAUTION]
> Компоненты [SDS (служба обнаружения секретов)][istio-feature-sds] и [Istio CNI][istio-feature-cni] Istio в настоящее время относятся к [альфа-каналу][istio-feature-stages], поэтому перед включением необходимо выполнить мысль. Кроме того, в текущей версии AKS не включена функция [проекции тома маркера учетной записи службы][kubernetes-feature-sa-projected-volume] Kubernetes (требование для SDS).

Создайте файл с именем `istio.aks.yaml` со следующим содержимым. Этот файл будет содержать подробную информацию о [спецификации плоскости управления Istio][istio-control-plane] для настройки Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Установите istio с помощью команды `istioctl apply` и приведенного выше файла спецификации `istio.aks.yaml` Istio Control плоскость следующим образом:

```console
istioctl manifest apply -f istio.aks.yaml
```

Установщик развертывает несколько [КРДС][kubernetes-crd] , а затем управляет зависимостями, чтобы установить все соответствующие объекты, определенные для этой конфигурации Istio. Вы должны увидеть нечто вроде приведенного ниже выходного фрагмента.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

На этом этапе вы развернули Istio в кластере AKS. Чтобы убедиться в успешном развертывании Istio, перейдем к следующему разделу, чтобы [проверить установку Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Проверка установки Istio

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl Get SVC][kubectl-get] для просмотра выполняющихся служб. Запросите пространство имен `istio-system`, где компоненты Istio и Add-On были установлены `istio` диаграмме Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

- службы `istio-*`
- службы трассировки надстроек `jaeger-*`, `tracing`и `zipkin`
- Служба метрик для `prometheus`
- `grafana` службы "аналитика и мониторинг" надстройки
- `kiali` службы панели мониторинга для сетки службы надстройки

Если `istio-ingressgateway` отображает значение `<pending>` в поле внешнего IP-адреса, подождите несколько минут, пока сеть Azure не назначит новый IP-адрес.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Затем убедитесь, что созданы все необходимые группы pod. Используйте команду [kubectl Get][kubectl-get] Pod и повторите запрос к пространству имен `istio-system`:

```console
kubectl get pods --namespace istio-system
```

В следующем примере выходных данных представлены запущенные группы pod:

- модули `istio-*`
- модуль метрик для надстройки `prometheus-*`
- Модуль мониторинга `grafana-*` надстройки и мониторинг
- модуль панели мониторинга `kiali` надстройки для сетки служб

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Все модули Pod должны отображать состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если какая либо из модулей Pod сообщает о возникшей ошибке, используйте команду [kubectl, описывающую][kubectl-describe] , чтобы проверить их выходные данные и состояние.

## <a name="accessing-the-add-ons"></a>Доступ к надстройкам

В нашей установке Istio были установлены некоторые надстройки, обеспечивающие дополнительные функциональные возможности. Веб-приложения для надстроек **не** предоставляются публично через внешний IP-адрес. 

Чтобы получить доступ к пользовательским интерфейсам надстройки, используйте команду `istioctl dashboard`. Эта команда использует [kubectl порт][kubectl-port-forward] и случайный порт для создания безопасного подключения между клиентским компьютером и соответствующим модулем-модулем в кластере AKS. После этого будет автоматически открыто веб-приложение надстройки в браузере по умолчанию.

Мы добавили дополнительный уровень безопасности для Grafana и Киали, указав учетные данные для них ранее в этой статье.

### <a name="grafana"></a>Grafana

Информационные панели аналитики и мониторинга для Istio предоставляются [Grafana][grafana]. При появлении запроса не забудьте использовать учетные данные, созданные с помощью секрета Grafana ранее. Безопасно откройте панель мониторинга Grafana следующим образом:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Метрики для Istio предоставляются [Prometheus][prometheus]. Безопасно откройте панель мониторинга Prometheus следующим образом:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Трассировка в Istio предоставляется [жаежер][jaeger]. Безопасно откройте панель мониторинга Жаежер следующим образом:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Панель мониторинга сетки служб предоставляется [киали][kiali]. При появлении запроса не забудьте использовать учетные данные, созданные с помощью секрета Киали ранее. Безопасно откройте панель мониторинга Киали следующим образом:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Доступен простой интерфейс для прокси-серверов [делегата][envoy] . Он предоставляет сведения о конфигурации и метрики для прокси-сервера делегата, работающего в указанном Pod. Безопасно Откройте интерфейс делегата следующим образом:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Удаление Istio из AKS

> [!WARNING]
> Удаление Istio из работающей системы может привести к проблемам с трафиком между Вашими службами. Перед продолжением убедитесь, что вы выполнили предварительные условия для правильной работы системы без Istio.

### <a name="remove-istio-components-and-namespace"></a>Удалить компоненты и пространство имен Istio

Чтобы удалить Istio из кластера AKS, используйте команду `istioctl manifest generate` с файлом спецификации `istio.aks.yaml` Istio Control плоскость. Будет создан развернутый манифест, который будет передавать `kubectl delete` для удаления всех установленных компонентов и пространства имен `istio-system`.

```console
istioctl manifest generate -f istio.aks.yaml | kubectl delete -f -
```

### <a name="remove-istio-crds-and-secrets"></a>Удалить Istio КРДС и секреты

Приведенные выше команды удаляют все компоненты и пространство имен Istio, но мы по-прежнему оставили созданные секреты Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Дополнительная информация

В следующей документации описывается, как можно использовать Istio для обеспечения интеллектуальной маршрутизации для развертывания ранний выпуска.

> [!div class="nextstepaction"]
> [Сценарий интеллектуальной маршрутизации AKS Istio][istio-scenario-routing]

Дополнительные сведения об установке и параметрах конфигурации для Istio см. в следующих официальных руководствах по Istio:

- [Istio — руководства по установке][istio-installation-guides]

Кроме того, можно выполнять дополнительные сценарии, используя:

- [Пример приложения Istio BookInfo][istio-bookinfo-example]

Сведения о мониторинге приложения AKS с помощью Application Insights и Istio см. в следующей документации Azure Monitor.

- [Мониторинг приложений с нулевым инструментированием для приложений, размещенных в Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
