---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877700"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

Сетка [Istio][istio-github] — это решение с открытым исходным кодом, которое предоставляет набор ключевых функций для микрослужб в кластере Kubernetes, включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. См. дополнительные сведения об [Istio][istio-docs-concepts].

В этой статье описано, как установить Istio. Двоичная `istioctl` система клиента Istio установлена на клиентской машине, а компоненты Istio устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Следующие инструкции ссылаются `1.4.0`версия Istio .
>
> Istio `1.4.x` релизы были протестированы командой Istio против `1.13` `1.14`версий Kubernetes , `1.15`. Вы можете найти дополнительные версии Istio на [GitHub - Istio релизы][istio-github-releases], информация о каждом из релизов на [Istio Новости][istio-release-notes] и поддерживается Kubernetes версии на [Istio Общие часто задаваемые вопросы][istio-faq].

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Скачать и установить двоичный клиент Istio istioctl
> * Установка Istio на AKS
> * Проверка установки Istio
> * Доступ к дополнениям
> * Удалить Istio из AKS

## <a name="before-you-begin"></a>Подготовка к работе

Шаги, описанные в этой статье, предполагают, что вы создали `1.13` кластер AKS (Kubernetes `kubectl` и выше, с включенным RBAC) и установили связь с кластером. Дополнительные сведения см. в [кратком руководстве по AKS][aks-quickstart].

Убедитесь, что вы прочитали документацию [о производительности и масштабируемости Istio,](https://istio.io/docs/concepts/performance-and-scalability/) чтобы понять дополнительные требования к ресурсам для выполнения Istio в кластере AKS. Требования к ядру и памяти будут варьироваться в зависимости от вашей конкретной рабочей нагрузки. Выберите соответствующее количество узлов и размер VM, чтобы удовлетворить вашу настройку.

В этой статье инструкции по установке Istio разделены на несколько этапов. Результат установки будет идентичным результату согласно [официальной инструкции][istio-install-istioctl].

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

Мы будем устанавливать [Grafana][grafana] и [Kiali][kiali] как часть нашей установки Istio. Grafana предоставляет панели мониторинга и мониторинга, а Kiali обеспечивает панель мониторинга сетки обслуживания. В нашей настройке, каждый из этих компонентов требует учетных данных, которые должны быть предоставлены в качестве [секрета.][kubernetes-secrets]

Прежде чем мы сможем установить компоненты Istio, мы должны создать секреты как для Grafana и Kiali. Эти секреты должны быть `istio-system` установлены в пространстве имен, которые будут использоваться Istio, так что нам нужно создать пространство имен тоже. Мы должны использовать `--save-config` опцию при создании `kubectl create` пространства имен с помощью `kubectl apply` того, чтобы установщик Istio мог работать на этом объекте в будущем.

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

Теперь, когда мы успешно создали секреты Grafana и Kiali в нашем кластере AKS, пришло время установить компоненты Istio. 

В будущем подход к установке [Helm][helm] для Istio будет универслен. Новый подход к установке для `istioctl` Istio использует клиент двоичный, [профили конфигурации Istio][istio-configuration-profiles], и новый [istio управления плоскости спецификации и api][istio-control-plane]. Этот новый подход является то, что мы будем использовать для установки Istio.

> [!NOTE]
> Istio в настоящее время должен быть запланирован для запуска на узлах Linux. Если в кластере есть узлы Windows Server, необходимо убедиться, что стручки Istio будут запущены только на узлах Linux. Мы будем использовать [селекторы узлов,][kubernetes-node-selectors] чтобы убедиться, что стручки запланированы на правильные узлы.

> [!CAUTION]
> [SDS (секретная служба обнаружения)][istio-feature-sds] и [Istio CNI][istio-feature-cni] Istio особенности в настоящее время в [Альфа][istio-feature-stages], так что мысли должны быть даны, прежде чем включить эти. 
>
> Обратите внимание, что функция проекции [объема токенов счета обслуживания][kubernetes-feature-sa-projected-volume] Kubernetes (требование для SDS) теперь **включена** для всех Kubernetes 1.13 и более высоких версий на AKS.

Создайте файл, вызванный `istio.aks.yaml` следующим содержанием. Этот файл будет содержать детали [спецификации плоскости управления Istio][istio-control-plane] для настройки Istio.

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

Установите istio `istioctl apply` с помощью команды и выше `istio.aks.yaml` Istio управления плоскости спецификации файл аследующим образом:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Установщик развернет несколько [CRD,][kubernetes-crd] а затем будет управлять зависимостями для установки всех соответствующих объектов, определенных для этой конфигурации Istio. Вы должны увидеть что-то вроде следующего фрагмента вывода.

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

На этом этапе вы развернули Istio в кластер AKS. Чтобы обеспечить успешное развертывание Istio, давайте перейдем к следующему разделу для [проверки установки Istio.](#validate-the-istio-installation)

## <a name="validate-the-istio-installation"></a>Проверка установки Istio

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl get svc][kubectl-get], чтобы просмотреть список запущенных служб. Запрос пространства `istio-system` имен, где Istio и дополнительные компоненты `istio` были установлены на диаграмме Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

- Службы `istio-*`
- `jaeger-*`, `tracing`, `zipkin` и дополнительные услуги отслеживания
- `prometheus`служба дополнительных метрик
- `grafana`дополнительная аналитика и служба мониторинга приборной панели
- `kiali`служба панели мониторинга дополнительных служб

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

Затем убедитесь, что созданы все необходимые группы pod. Используйте [команду kubectl получить стручки,][kubectl-get] `istio-system` и снова задавить пространство имен:

```console
kubectl get pods --namespace istio-system
```

В следующем примере выходных данных представлены запущенные группы pod:

- `istio-*` стручки
- стручка `prometheus-*` метрик дополнений
- надстройка аналитики `grafana-*` и мониторингприборной панели стручка
- стручка приборной панели сетки `kiali` службы дополненной службы

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

Все стручки должны показать статус `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если для групп pod отображаются проблемы, выполните команду [describe pod][kubectl-describe], чтобы просмотреть выходные данные и состояние.

## <a name="accessing-the-add-ons"></a>Доступ к надстройкам

Ряд дополнений были установлены Istio в нашей вышеуказанной установке, которые обеспечивают дополнительную функциональность. Веб-приложения для дополнений **не** разглашаются публично через внешний IP-адрес. 

Чтобы получить доступ к надстройке `istioctl dashboard` пользовательских интерфейсов, используйте команду. Эта команда использует [kubectl port-forward][kubectl-port-forward] и случайный порт для создания безопасного соединения между клиентской машиной и соответствующим стручками в кластере AKS. Затем он автоматически откроет надстройку веб-приложения в браузере по умолчанию.

Мы добавили дополнительный уровень безопасности для Grafana и Kiali, указав учетные данные для них ранее в этой статье.

### <a name="grafana"></a>Grafana

Аналитические и мониторинговые панели мониторинга для Istio предоставляются [Grafana][grafana]. Не забудьте использовать учетные данные, созданные с помощью секрета Grafana ранее, когда предложено. Откройте приборную панель Grafana надежно следующим образом:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Метрики для Istio предоставляются [Prometheus][prometheus]. Откройте панель мониторинга Prometheus надежно следующим образом:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Отслеживание в Istio обеспечивается [Jaeger][jaeger]. Откройте панель мониторинга Jaeger надежно следующим образом:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Панель мониторинга для сетки служб предоставляется [Kiali][kiali]. Не забудьте использовать учетные данные, созданные с помощью секрета Kiali ранее, когда предложено. Откройте приборную панель Kiali надежно следующим образом:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Доступен простой интерфейс для доверенных данных [Посланника.][envoy] Он предоставляет информацию о конфигурации и метрики для прокси-сервера envoy, работаемого в указанном стручке. Откройте интерфейс Envoy надежно следующим образом:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Удалить Istio из AKS

> [!WARNING]
> Удаляние Istio из запущенной системы может привести к проблемам, связанным с трафиком между вашими службами. Убедитесь, что вы сделали положения для вашей системы по-прежнему работают правильно без Istio, прежде чем продолжить.

### <a name="remove-istio-components-and-namespace"></a>Удалить компоненты Istio и пространство имен

Чтобы удалить Istio из кластера `istioctl manifest generate` AKS, `istio.aks.yaml` используйте команду с файлом спецификации плоскости управления Istio. Это будет генерировать развернутый манифест, который `kubectl delete` мы будем трубы для того, чтобы удалить все установленные компоненты и пространство `istio-system` имен.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Удалить Istio CRDs и секреты

Вышеуказанные команды удаляют все компоненты Istio и пространство имен, но у нас все еще остаются сгенерированные секреты Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

В следующей документации описывается, как можно использовать Istio для обеспечения интеллектуальной реуктора для развертывания канарейки:

> [!div class="nextstepaction"]
> [Описание сценария интеллектуальной маршрутизации Istio для AKS][istio-scenario-routing]

Чтобы изучить дополнительные параметры установки и конфигурации для Istio, см.

- [Istio - инсталляция направляющих][istio-installation-guides]

Вы также можете следить за дополнительными сценариями, используя:

- [Пример применения Istio Bookinfo][istio-bookinfo-example]

Чтобы узнать, как контролировать приложение AKS с помощью Application Insights и Istio, смотрите следующую документацию Azure Monitor:

- [Нулевой мониторинг приложений приборов для Kubernetes размещаемых приложений][app-insights]

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
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
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
