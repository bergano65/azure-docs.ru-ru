---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138156"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

Сетка [Istio][istio-github] — это решение с открытым исходным кодом, которое предоставляет набор ключевых функций для микрослужб в кластере Kubernetes, включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. См. дополнительные сведения об [Istio][istio-docs-concepts].

В этой статье описано, как установить Istio. Двоичный файл клиента Istio `istioctl` устанавливается на клиентском компьютере, а затем компоненты Istio устанавливаются в кластер Kubernetes в AKS. Представленные здесь инструкции относятся к версии Istio *1.0.4*. Другие версии Istio вы найдете в репозитории [GitHub с выпусками Istio][istio-github-releases].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * скачивание Istio;
> * установка двоичного файла клиента Istio;
> * установка компонентов Istio для Kubernetes;
> * проверка установки

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS (Kubernetes 1.10 или более новой версии с поддержкой RBAC), к которому установлено подключение `kubectl`. Дополнительные сведения см. в [кратком руководстве по AKS][aks-quickstart].

Чтобы установить Istio, вам потребуется [Helm][helm] версии *2.10.0* или выше с правильной установкой и настройкой в этом кластере. Дополнительные сведения см. в [руководстве по установке AKS Helm][helm-install]. Если вы уже установили Helm версии *2.10.0* или выше, можно выполнить обновление. Другие варианты установки см. в [руководстве по установке Istio с помощью Helm][istio-install-helm].

В этой статье инструкции по установке Istio разделены на несколько этапов. Каждое из этих действий подробно описано, что позволит вам разобраться не только в особенностях установке Istio, но и во взаимодействии Istio с Kubernetes. Результат установки будет идентичным результату согласно [официальной инструкции][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Скачивание Istio

Прежде всего, скачайте и извлеките последний выпуск Istio. Шаги для оболочки bash в MacOS, Linux или подсистеме Windows для Linux, а также для оболочки PowerShell немного отличаются. Выберите один из следующих вариантов установки для используемого окружения.

* [Bash для MacOS, Linux или подсистемы Linux для Windows](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

В MacOS выполните `curl`, чтобы скачать последний выпуск Istio, и извлеките его с помощью `tar`, как показано ниже:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

В Linux или подсистеме Linux для Windows выполните `curl`, чтобы скачать последний выпуск Istio, и извлеките его с помощью `tar`, как показано ниже:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

В PowerShell выполните [Invoke-WebRequest][Invoke-WebRequest], чтобы скачать последний выпуск Istio, и извлеките его с помощью [Expand-Archive][Expand-Archive], как показано ниже:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Установка двоичного файла клиента Istio

Двоичный файл клиента `istioctl` выполняется на клиентском компьютере и позволяет управлять правилами и политиками маршрутизации Istio. Инструкции по установке также немного отличаются в разных клиентских операционных системах. Выберите один из следующих вариантов установки для используемого окружения.

> [!IMPORTANT]
> Выполняйте шаги в этом разделе описано, из папки верхнего уровня выпуск Istio, который вы загрузили и извлекли.

### <a name="macos"></a>MacOS

Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в MacOS, используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Если вам нужна функция завершения командной строки для двоичного файла `istioctl` клиента Istio, настройте ее следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Теперь перейдите в раздел для [установки компонентов Istio для Kubernetes](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux или подсистема Linux для Windows

Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в Linux или [подсистеме Linux для Windows][install-wsl], используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Если вам нужна функция завершения командной строки для двоичного файла `istioctl` клиента Istio, настройте ее следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Теперь перейдите в раздел для [установки компонентов Istio для Kubernetes](#install-the-istio-kubernetes-components).

### <a name="windows"></a> Windows

Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке Powershell в Windows, используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в расположение программ для нового пользователя и делают его доступным через `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Установка компонентов Istio для Kubernetes

> [!IMPORTANT]
> Выполняйте шаги в этом разделе описано, из папки верхнего уровня выпуск Istio, который вы загрузили и извлекли.

> [!NOTE]
> Версии `1.0.6` и более новой версии на диаграмме Istio Helm содержит критические изменения. Если выбрать для установки этой версии, теперь необходимо будет вручную создать секрет для Kiali. Также необходимо будет вручную создать секрет для Grafana, если вы задали `grafana.security.enabled=true`. См. в диаграмме Helm Istio [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) Дополнительные сведения о том, как создать эти секреты.

Чтобы установить компоненты Istio в кластере AKS, используйте Helm. Установите ресурсы Istio в пространстве имен `istio-system` и включите дополнительные параметры безопасности и мониторинга, как показано ниже:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Схема Helm развертывает большое число объектов. В зависимости от окружения кластера развертывание может занять 2–3 минуты.

## <a name="validate-the-installation"></a>проверка установки

Чтобы убедиться, что развертывание Istio прошло успешно, давайте проверим установку.

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl get svc][kubectl-get], чтобы просмотреть список запущенных служб. Создайте запрос к пространству имен *istio-system*, где установлены компоненты и выполнена надстройка Istio с помощью схемы Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

- службы *istio-**;
- службы надстройки для трассировки *jaeger-**, *tracing* и *zipkin*;
- служба надстройки метрик *prometheus*;
- служба надстройки панели аналитики и мониторинга *grafana*;
- служба надстройки панели мониторинга для сетки служб *kiali*.

Если `istio-ingressgateway` отображает значение `<pending>` в поле внешнего IP-адреса, подождите несколько минут, пока сеть Azure не назначит новый IP-адрес.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Затем убедитесь, что созданы все необходимые группы pod. Используйте команду [kubectl get pods][kubectl-get] и снова создайте запрос к пространству имен *istio-system*:

```console
kubectl get pods --namespace istio-system
```

В следующем примере выходных данных представлены запущенные группы pod:

- pod *istio -**;
- pod надстройки *prometheus-* метрик;
- pod надстройки *grafana* панели аналитики и мониторинга;
- pod надстройки *kiali* панели мониторинга сетки служб.

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Все группы pod отображают состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если для групп pod отображаются проблемы, выполните команду [describe pod][kubectl-describe], чтобы просмотреть выходные данные и состояние.

## <a name="accessing-the-add-ons"></a>Доступ к надстройкам

В ходе описанного выше процесса для Istio устанавливается несколько надстроек, которые предоставляют дополнительные функции. Пользовательские интерфейсы для надстроек не предоставляются в открытый доступ через внешний IP-адрес. Чтобы получить доступ к пользовательским интерфейсам надстроек, используйте команду [kubectl port-forward][kubectl-port-forward]. Эта команда создает безопасное подключение между локальным портом на клиентском компьютере и соответствующей группой pod в кластере AKS.

### <a name="grafana"></a>Grafana

Панель аналитики и мониторинга для Istio предоставляется [Grafana][grafana]. Перенаправьте локальный порт клиентского компьютера *3000* на порт группы pod *3000*, в которой выполняется Grafana в кластере AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Теперь вы можете обратиться к Grafana по следующему URL-адресу на клиентском компьютере — [http://localhost:3000](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Обозреватель выражений для метрик предоставляется [Prometheus][prometheus]. Перенаправьте локальный порт клиентского компьютера *9090* на порт группы pod *9090*, в которой выполняется Prometheus в кластере AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Теперь вы можете обратиться к обозревателю выражений Prometheus по следующему URL-адресу на клиентском компьютере — [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Пользовательский интерфейс трассировки предоставляется [Jaeger][jaeger]. Перенаправьте локальный порт клиентского компьютера *16686* на порт группы pod *16686*, в которой выполняется Jaeger в кластере AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Теперь вы можете обратиться к интерфейсу трассировки Jaeger по следующему URL-адресу на клиентском компьютере — [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Панель мониторинга для сетки служб предоставляется [Kiali][kiali]. Перенаправьте локальный порт клиентского компьютера *20001* на порт группы pod *20001*, в которой выполняется Kiali в кластере AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Теперь вы можете обратиться к панели мониторинга Kiali для мониторинга сетки служб по следующему URL-адресу на клиентском компьютере — [http://localhost:20001](http://localhost:20001).

По умолчанию для панели мониторинга Kiali используются следующие имя пользователя и пароль: *username:admin/password:admin*. Эти учетные данные можно настроить с помощью параметров Helm *kiali.dashboard.username* и *kiali.dashboard.passphrase*.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как можно использовать Istio для организации интеллектуальной маршрутизации между несколькими версиями приложения и развертывания раннего выпуска, см. здесь:

> [!div class="nextstepaction"]
> [Описание сценария интеллектуальной маршрутизации Istio для AKS][istio-scenario-routing]

Дополнительные сведения о других вариантах установки и настройки для Istio см. здесь:

- [Istio - Kubernetes installation quickstart][istio-install-k8s-quickstart] (Istio: краткое руководство по установке Kubernetes)
- [Istio - Helm installation guide][istio-install-helm] (Istio: руководство по установке Helm)
- [Istio - Helm installation options][istio-install-helm-options] (Istio: параметры установки Helm)

Вы также можете реализовать дополнительные сценарии, используя [пример приложения Istio Bookinfo][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
