---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 33d86ab8c88b45c7787620773f0df6e7fe888cf3
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850419"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

Сетка [Istio][istio-github] — это решение с открытым исходным кодом, которое предоставляет набор ключевых функций для микрослужб в кластере Kubernetes, включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. См. дополнительные сведения об [Istio][istio-docs-concepts].

В этой статье описано, как установить Istio. Istio `istioctl` двоичный файл клиента устанавливается на клиентском компьютере и Istio компоненты устанавливаются в кластер Kubernetes в AKS.

> [!NOTE]
> Эти инструкции ссылаются на версии Istio `1.1.3`.
>
> Istio `1.1.x` выпуски будут проверены командой Istio для версий Kubernetes `1.11`, `1.12`, `1.13`. Можно найти дополнительные версии Istio в [GitHub - освобождает Istio] [ istio-github-releases] и сведения о каждом из выпусков в [Istio - заметки о выпуске] [ istio-release-notes].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * скачивание Istio;
> * Установка клиента istioctl Istio двоичные
> * Установка Istio CRDs в AKS
> * Установите компоненты Istio в AKS
> * Проверка установки Istio
> * Доступ к надстройкам
> * Удалить Istio с AKS

## <a name="before-you-begin"></a>Перед началом работы

Действия, описанные в этой статье предполагается, что вы создали кластер службы контейнеров AZURE (Kubernetes `1.11` и более поздних версий, с помощью RBAC включен) и установить `kubectl` соединение с кластером. Дополнительные сведения см. в [кратком руководстве по AKS][aks-quickstart].

Вам потребуется [Helm] [ helm] выполните эти инструкции и установить Istio. Рекомендуется использовать версию `2.12.2` или более поздней версии правильно установлен и настроен в кластере. Если вам нужна помощь при установке Helm, затем см. в разделе [инструкции по установке AKS Helm][helm-install]. Также все модули Istio необходимо запланировать выполнение на узлах Linux.

В этой статье инструкции по установке Istio разделены на несколько этапов. Результат установки будет идентичным результату согласно [официальной инструкции][istio-install-helm].

## <a name="download-istio"></a>Скачивание Istio

Прежде всего, скачайте и извлеките последний выпуск Istio. Шаги для оболочки bash в MacOS, Linux или подсистеме Windows для Linux, а также для оболочки PowerShell немного отличаются. Выберите один из следующих шагов установки, извлекающий предпочтительные окружения.

* [Bash для MacOS, Linux или подсистемы Linux для Windows](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

В MacOS выполните `curl`, чтобы скачать последний выпуск Istio, и извлеките его с помощью `tar`, как показано ниже:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

В Linux или подсистеме Linux для Windows выполните `curl`, чтобы скачать последний выпуск Istio, и извлеките его с помощью `tar`, как показано ниже:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Теперь перейти к раздела [установить двоичные клиент istioctl Istio](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

В PowerShell, используйте `Invoke-WebRequest` загрузить последний выпуск Istio и извлеките с `Expand-Archive` следующим образом:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Теперь перейти к раздела [установить двоичные клиент istioctl Istio](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Установка клиента istioctl Istio двоичные

> [!IMPORTANT]
> Выполняйте шаги в этом разделе описано, из папки верхнего уровня выпуск Istio, который вы загрузили и извлекли.

`istioctl` Двоичный файл клиента выполняется на клиентском компьютере и позволяет взаимодействовать с ней Istio. Инструкции по установке немного отличаются друг от друга клиентских операционных систем. Выберите один из следующих шагов установки, извлекающий предпочтительные окружения.

* [MacOS](#macos)
* [Linux или подсистеме Windows для Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в MacOS, используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Теперь перейдем к следующему разделу, чтобы [установить Istio CRDs в AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux или подсистема Linux для Windows

Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в Linux или [подсистеме Linux для Windows][install-wsl], используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Теперь перейдем к следующему разделу, чтобы [установить Istio CRDs в AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a> Windows

Чтобы установить Istio `istioctl` в двоичные файлы клиента **Powershell**-на основе оболочки на Windows, используйте следующие команды. Эти команды копируют `istioctl` двоичный файл в папку Istio клиента и сделать его постоянно доступным через ваш `PATH`. Не требуется привилегий (администратор) для выполнения этих команд.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Теперь перейдем к следующему разделу, чтобы [установить Istio CRDs в AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Установка Istio CRDs в AKS

> [!IMPORTANT]
> Выполняйте шаги в этом разделе описано, из папки верхнего уровня выпуск Istio, который вы загрузили и извлекли.

Использует Istio [определения пользовательских ресурсов (CRDs)] [ kubernetes-crd] управление его конфигурацией среды выполнения. Нам нужно установить Istio CRDs во-первых, в том случае, так как компоненты Istio зависят от их. Использование Helm и `istio-init` диаграмму, чтобы установить CRDs Istio в `istio-system` пространства имен в кластере AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Задания] [ kubernetes-jobs] были развернуты как часть `istio-init` Чарта Helm для установки CRDs. Эти задания может занять от 1 до 2 минут, в зависимости от вашей кластерной среды. Вы можете убедиться, что задания успешно выполнить следующим образом:

```azurecli
kubectl get jobs -n istio-system
```

В следующем примере выходных данных показано, успешно выполненные задания.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Теперь, когда мы подтверждения успешного завершения задания, Давайте проверим, что у нас есть правильное количество CRDs Istio установлен. Чтобы узнать, что все 53 CRDs Istio были установлены, выполнив команду, соответствующую вашей среде. Команда должна вернуть номер `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Если у вас есть к этой точке, то это означает, что Istio CRDs успешно установлен. Теперь перейдем к следующему разделу, чтобы [установки компонентов Istio в AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Установите компоненты Istio в AKS

> [!IMPORTANT]
> Выполняйте шаги в этом разделе описано, из папки верхнего уровня выпуск Istio, который вы загрузили и извлекли.

Мы будет устанавливаться [Grafana] [ grafana] и [Kiali] [ kiali] как часть наших Istio установки. Grafana аналитику и панели мониторинга, и Kiali предоставляет возможность наблюдения сетки панели мониторинга обслуживания. В нашей программе установки, каждый из этих компонентов требуются учетные данные, необходимо передать в качестве [секрет][kubernetes-secrets].

Прежде чем можно установить компоненты Istio, необходимо создать секреты Kiali и Grafana. Создайте эти секреты, выполнив соответствующие команды для вашей среды.

### <a name="add-grafana-secret"></a>Добавление секрета Grafana

Замените `REPLACE_WITH_YOUR_SECURE_PASSWORD` маркеров с помощью пароля и выполните следующие команды:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a> Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Добавление секрета Kiali

Замените `REPLACE_WITH_YOUR_SECURE_PASSWORD` маркеров с помощью пароля и выполните следующие команды:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a> Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Установка компонентов Istio

Теперь, когда мы успешно создали Grafana и Kiali секреты в нашем кластере AKS, пришло время для установки компонентов Istio. Использование Helm и `istio` диаграмму, чтобы установить компоненты Istio в `istio-system` пространства имен в кластере AKS. Используйте соответствующие команды для вашей среды.

> [!NOTE]
> Как часть нашей установки мы используем следующие параметры:
> - `global.controlPlaneSecurityEnabled=true` -взаимной TLS включена для плоскости управления
> - `mixer.adapters.useAdapterCRDs=false` -удалить контролируемые данные в адаптере mixer CRDs, как они будет признан устаревшим и это улучшит производительность
> - `grafana.enabled=true` -включить развертывание Grafana для аналитики и панели мониторинга
> - `grafana.security.enabled=true` -Включите проверку подлинности для Grafana
> - `tracing.enabled=true` -включить развертывание Jaeger для трассировки
> - `kiali.enabled=true` -включить развертывание Kiali для панели мониторинга обслуживания наблюдаемость сетки

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Чарта Helm развертывает большое число объектов. Вы можете увидеть список из выходных данных вашей `helm install` команды выше. Развертывание компонентов Istio может занять 4 – 5 минут, в зависимости от вашей кластерной среды.

> [!NOTE]
> Все модули Istio необходимо запланировать выполнение на узлах Linux. При наличии пулы узлов Windows Server в дополнение к Linux пулы узлов в кластере, убедитесь, что все модули Istio запланированы для запуска на узлах Linux.

На этом этапе вы развернули Istio в кластере AKS. Чтобы убедиться, что у нас есть успешное развертывание Istio, давайте перейдем к следующему разделу, чтобы [проверка установки Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Проверка установки Istio

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl get svc][kubectl-get], чтобы просмотреть список запущенных служб. Запрос `istio-system` пространства имен, где были установлены компоненты Istio и надстройки, `istio` чарта Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

- `istio-*` службы
- `jaeger-*`, `tracing`, и `zipkin` трассировки службы надстройки
- `prometheus` метрики службы надстройки
- `grafana` Надстройка аналитики и наблюдающую функцию на панели мониторинга
- `kiali` Надстройка службы сетки панели мониторинга службы

Если `istio-ingressgateway` отображает значение `<pending>` в поле внешнего IP-адреса, подождите несколько минут, пока сеть Azure не назначит новый IP-адрес.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Затем убедитесь, что созданы все необходимые группы pod. Используйте [kubectl get pods] [ kubectl-get] команды и снова запроса `istio-system` пространство имен:

```console
kubectl get pods --namespace istio-system
```

В следующем примере выходных данных представлены запущенные группы pod:

- `istio-*` модулей
- `prometheus-*` pod метрики надстройки
- `grafana-*` надстройку аналитики и мониторинга pod панели мониторинга
- `kiali` pod информационной панели сетки службы надстройки

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Должно быть два `istio-init-crd-*` модулей с `Completed` состояния. Эти модули были отвечает за запуск заданий, которые созданы CRDs на предыдущем шаге. Все другие модули должно отображаться состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если для групп pod отображаются проблемы, выполните команду [describe pod][kubectl-describe], чтобы просмотреть выходные данные и состояние.

## <a name="accessing-the-add-ons"></a>Доступ к надстройкам

В ходе описанного выше процесса для Istio устанавливается несколько надстроек, которые предоставляют дополнительные функции. Пользовательские интерфейсы для надстроек не предоставляются в открытый доступ через внешний IP-адрес. Чтобы получить доступ к пользовательским интерфейсам надстроек, используйте команду [kubectl port-forward][kubectl-port-forward]. Эта команда создает безопасное подключение между клиентском компьютере и соответствующие pod в кластере AKS.

Мы добавили дополнительный уровень безопасности для Grafana и Kiali, указав учетные данные для их ранее в этой статье.

### <a name="grafana"></a>Grafana

Аналитика и мониторинг панели мониторинга для Istio предоставляются [Grafana][grafana]. Пересылать локальный порт `3000` на клиентском компьютере с портом `3000` на pod, который выполняется в кластере AKS Grafana:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Теперь вы можете обратиться к Grafana по следующему URL-адресу на клиентском компьютере — [http://localhost:3000](http://localhost:3000). Не забывайте использовать учетные данные, которые были созданы с помощью Grafana секретный ранее при появлении запроса.

### <a name="prometheus"></a>Prometheus

Метрики для Istio, предоставляются [Prometheus][prometheus]. Пересылать локальный порт `9090` на клиентском компьютере с портом `9090` на pod, который выполняется в кластере AKS Prometheus:

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

Трассировка в Istio обеспечивается [Jaeger][jaeger]. Пересылать локальный порт `16686` на клиентском компьютере с портом `16686` на pod, который выполняется в кластере AKS Jaeger:

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

Панель мониторинга для сетки служб предоставляется [Kiali][kiali]. Пересылать локальный порт `20001` на клиентском компьютере с портом `20001` на pod, который выполняется в кластере AKS Kiali:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Теперь вы можете обратиться к панели мониторинга Kiali для мониторинга сетки служб по следующему URL-адресу на клиентском компьютере — [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Не забывайте использовать учетные данные, которые были созданы через Kiali секретный ранее при появлении запроса.

## <a name="uninstall-istio-from-aks"></a>Удалить Istio с AKS

> [!WARNING]
> Удаление Istio из работающей системы может привести к трафика проблемы между службами. Убедитесь, что внесены подготавливает системы, чтобы по-прежнему работать без Istio прежде чем продолжить.

### <a name="remove-istio-components-and-namespace"></a>Удаление компонентов Istio и пространства имен

Чтобы удалить Istio из кластера AKS, используйте следующие команды. `helm delete` Команды удаляют `istio` и `istio-init` диаграмм и `kubectl delete ns` команда удалит `istio-system` пространства имен.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Удалить Istio CRDs

Указанные выше команды удаления всех компонентов Istio и пространство имен, но мы по-прежнему остаются с Istio CRDs. Чтобы удалить CRDs, можно использовать один следующих подходов.

Подход 1 # - Эта команда предполагает, что этот шаг выполняется в папке верхнего уровня выпуска скачанного и извлеченного Istio, который использовался для установки Istio с.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Подход #2 - используйте один из этих команд, если отсутствует доступ к выпуску скачанного и извлеченного Istio, который использовался для установки Istio с. Эта команда займет немного больше - это может занять несколько минут.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Дальнейшие действия

В следующей документации описаны способы Istio для intelligent маршрутизации, чтобы развернуть раннего выпуска:

> [!div class="nextstepaction"]
> [Описание сценария интеллектуальной маршрутизации Istio для AKS][istio-scenario-routing]

Дополнительные сведения о других вариантах установки и настройки для Istio см. здесь:

- [Istio - Helm installation guide][istio-install-helm] (Istio: руководство по установке Helm)
- [Istio - Helm installation options][istio-install-helm-options] (Istio: параметры установки Helm)

Вы также можете реализовать дополнительные сценарии, используя [пример приложения Istio Bookinfo][istio-bookinfo-example].

Чтобы узнать, как наблюдать за приложением AKS с помощью Application Insights и Istio, см. в следующей документации Azure Monitor:
- [Мониторинг приложений ноль инструментирования для Kubernetes размещенных приложений][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md