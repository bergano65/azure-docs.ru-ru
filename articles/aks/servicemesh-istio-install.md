---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885384"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

[Istio][istio-github] — это сетка служб с открытым исходным кодом, которая предоставляет набор ключевых функций для микрослужб в кластере Kubernetes. включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. Дополнительные сведения о Istio см. в официальной документации о [Istio?][istio-docs-concepts] .

В этой статье описано, как установить Istio. Двоичный файл клиента Istio `istioctl` устанавливается на клиентский компьютер, а компоненты Istio устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Эти инструкции ссылаются на Istio версии `1.3.2`и используют по крайней мере Helm версии `2.14.2`.
>
> Istio выпуски `1.3.x` были протестированы группой Istio по сравнению с Kubernetes версиями `1.13`, `1.14``1.15`. Дополнительные версии Istio можно найти в разделе [GitHub-Istio][istio-github-releases], сведения о каждой из выпусков в [Istio News][istio-release-notes] и поддерживаемых версиях Kubernetes в [Istio общие вопросы и ответы][istio-faq].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Скачивание и установка двоичного файла клиента Istio истиоктл
> * Установка Istio на AKS
> * Проверка установки Istio
> * Доступ к надстройкам
> * Удаление Istio из AKS

## <a name="before-you-begin"></a>Перед началом работы

В шагах, описанных в этой статье, предполагается, что вы создали кластер AKS (Kubernetes `1.13` и выше, с включенным RBAC) и установили `kubectl` подключение к кластеру. Если вам нужна помощь по любому из этих элементов, см. [Краткое руководство по AKS][aks-quickstart].

Вам потребуется [Helm][helm] , чтобы выполнить эти инструкции и установить Istio. Рекомендуется правильно установить и настроить последнюю стабильную версию в кластере. Если вам нужна помощь с установкой Helm, см. [руководство по установке AKS Helm][helm-install]. Все Istio Pod также должны быть запланированы для запуска на узлах Linux.

Убедитесь, что вы прочитали документацию по [производительности и масштабируемости Istio](https://istio.io/docs/concepts/performance-and-scalability/) , чтобы понять требования к дополнительным ресурсам для запуска Istio в кластере AKS. Требования к основному и оперативной памяти будут зависеть от конкретной рабочей нагрузки. Выберите подходящее количество узлов и размер виртуальной машины для настройки.

В этой статье инструкции по установке Istio разделены на несколько этапов. Конечный результат аналогичен в структуре в качестве официального [руководства][istio-install-helm]по установке Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Добавление репозитория диаграммы Istio Helm

Добавьте репозиторий диаграммы Istio Helm для выпуска Istio. Убедитесь, что вы запускаете `helm repo update`, чтобы обновить локальную информацию для репозитория диаграммы.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Установка Istio КРДС на AKS

Istio использует [пользовательские определения ресурсов (КРДС)][kubernetes-crd] для управления конфигурацией среды выполнения. Сначала необходимо установить Istio КРДС, так как компоненты Istio имеют зависимость от них. Используйте Helm и диаграмму `istio-init` для установки Istio КРДС в пространство имен `istio-system` в кластере AKS:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[Задания][kubernetes-jobs] развертываются в составе `istio-init` диаграммы Helm для установки КРДС. В зависимости от среды кластера для выполнения этих заданий должно потребоваться меньше 20s. Убедиться в успешном завершении заданий можно следующим образом.

```azurecli
kubectl get jobs -n istio-system
```

В следующем примере выходных данных показаны успешно выполненные задания.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Теперь, когда мы подтвердили успешное завершение заданий, давайте проверим, что установлено правильное число КРДС Istio. Чтобы убедиться, что все 23 Istio КРДС установлены, запустите следующую команду. Команда должна возвращать число `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Если вы приступили к этому моменту, то это означает, что вы успешно установили Istio КРДС. Теперь перейдите к следующему разделу, чтобы [установить компоненты Istio на AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Установка компонентов Istio на AKS

Мы будем устанавливать [Grafana][grafana] и [киали][kiali] в рамках нашей Istio установки. Grafana предоставляет панели мониторинга для аналитики и мониторинга, а Киали — панель мониторинга сетки служб. В нашей установке каждому из этих компонентов требуются учетные данные, которые должны быть предоставлены в качестве [секрета][kubernetes-secrets].

Прежде чем можно будет установить компоненты Istio, необходимо создать секреты для Grafana и Киали. 

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

Теперь, когда мы успешно создали секреты Grafana и Киали в нашем кластере AKS, пришло время установить компоненты Istio. Используйте Helm и диаграмму `istio` для установки компонентов Istio в пространство имен `istio-system` в кластере AKS. 

> [!NOTE]
> **Параметры установки**
> 
> В процессе установки мы используем следующие варианты:
> - `global.controlPlaneSecurityEnabled=true`-взаимная поддержка TLS для плоскости управления
> - `global.mtls.enabled=true` — требовать, чтобы все службы могли обмениваться данными с MTLS
> - `grafana.enabled=true` — включить развертывание Grafana для аналитиков и мониторинга.
> - `grafana.security.enabled=true` — включить проверку подлинности для Grafana
> - `tracing.enabled=true` — включить развертывание Жаежер для трассировки
> - `kiali.enabled=true`. Включение развертывания Киали для панели мониторинга сетки служб
>
> **Селекторы узлов**
>
> Istio в настоящее время необходимо запланировать запуск на узлах Linux. Если в кластере есть узлы Windows Server, необходимо убедиться, что Istio Pods планируется запускать только на узлах Linux. Мы будем использовать [селекторы узлов][kubernetes-node-selectors] , чтобы убедиться, что для модулей Pod запланированы правильные узлы.

> [!CAUTION]
> Компоненты [SDS (служба обнаружения секретов)][istio-feature-sds] и [Istio CNI][istio-feature-cni] Istio в настоящее время относятся к [альфа-каналу][istio-feature-stages], поэтому перед включением необходимо выполнить мысль. Кроме того, в текущей версии AKS не включена функция [проекции тома маркера учетной записи службы][kubernetes-feature-sa-projected-volume] Kubernetes (требование для SDS).

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

`istio` диаграмма Helm развертывает большое количество объектов. Список можно просмотреть из выходных данных команды `helm install` выше. Для завершения развертывания компонентов Istio в зависимости от среды кластера необходимо выполнить до 2 минут.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Необходимо иметь три `istio-init-crd-*` модулей Pod с состоянием `Completed`. Эти модули не отвечают за выполнение заданий, создавших КРДС на предыдущем шаге. Все остальные модули Pod должны отображать состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если какая либо из модулей Pod сообщает о возникшей ошибке, используйте команду [kubectl, описывающую][kubectl-describe] , чтобы проверить их выходные данные и состояние.

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

Чтобы удалить Istio из кластера AKS, используйте следующие команды. `helm delete` команды будут удалять диаграммы `istio` и `istio-init`, а команда `kubectl delete namespace` удалит пространство имен `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Удалить Istio КРДС и секреты

Приведенные выше команды удаляют все компоненты и пространство имен Istio, но мы по-прежнему оставили Istio КРДС и секреты. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Дополнительная информация

В следующей документации описывается, как можно использовать Istio для обеспечения интеллектуальной маршрутизации для развертывания ранний выпуска.

> [!div class="nextstepaction"]
> [Сценарий интеллектуальной маршрутизации AKS Istio][istio-scenario-routing]

Дополнительные сведения о других вариантах установки и настройки для Istio см. здесь:

- [Istio — руководства по установке Helm][istio-install-helm]
- [Параметры установки Istio-Helm][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
