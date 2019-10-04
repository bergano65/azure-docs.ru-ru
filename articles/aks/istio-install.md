---
title: Установка Istio в Службе Azure Kubernetes (AKS)
description: Сведения об установке и применении Istio для создания сетки службы в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827324"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Установка и использование Istio в Службе Azure Kubernetes (AKS)

[Istio][istio-github] — это сетка служб с открытым исходным кодом, которая предоставляет набор ключевых функций для микрослужб в кластере Kubernetes. включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. Дополнительные сведения о Istio см. в официальной документации о [Istio?][istio-docs-concepts] .

В этой статье описано, как установить Istio. Клиентский двоичный файл Istio `istioctl` устанавливается на клиентский компьютер, а компоненты Istio устанавливаются в кластер Kubernetes на AKS.

> [!NOTE]
> Эти инструкции ссылаются на Istio Version `1.1.3`.
>
> Выпуски Istio `1.1.x` были протестированы группой Istio по сравнению с Kubernetes версиями `1.11`, `1.12`, `1.13`. Дополнительные версии Istio можно найти в [выпусках GitHub-Istio][istio-github-releases] , а сведения о каждой из выпусков — в [Istio News][istio-release-notes].

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * скачивание Istio;
> * Установка двоичного файла клиента Istio истиоктл
> * Установка Istio КРДС на AKS
> * Установка компонентов Istio на AKS
> * Проверка установки Istio
> * Доступ к надстройкам
> * Удаление Istio из AKS

## <a name="before-you-begin"></a>Перед началом работы

В шагах, описанных в этой статье, предполагается, что вы создали кластер AKS (Kubernetes `1.11` и более поздней версии с включенным RBAC) и установили подключение `kubectl` к кластеру. Если вам нужна помощь по любому из этих элементов, см. [Краткое руководство по AKS][aks-quickstart].

Вам потребуется [Helm][helm] , чтобы выполнить эти инструкции и установить Istio. Рекомендуется правильно установить или настроить версию `2.12.2` или более поздней версии в кластере. Если вам нужна помощь с установкой Helm, см. [руководство по установке AKS Helm][helm-install]. Все Istio Pod также должны быть запланированы для запуска на узлах Linux.

Убедитесь, что вы прочитали документацию по [производительности и масштабируемости Istio](https://istio.io/docs/concepts/performance-and-scalability/) , чтобы понять требования к дополнительным ресурсам для запуска Istio в кластере AKS. Требования к основному и оперативной памяти будут зависеть от конкретной рабочей нагрузки. Выберите подходящее количество узлов и размер виртуальной машины для настройки.

В этой статье инструкции по установке Istio разделены на несколько этапов. Конечный результат аналогичен в структуре в качестве официального [руководства][istio-install-helm]по установке Istio.

## <a name="download-istio"></a>скачивание Istio;

Прежде всего, скачайте и извлеките последний выпуск Istio. Эти шаги немного отличаются для интерпретатора bash в подсистеме MacOS, Linux или Windows для Linux, а также для оболочки PowerShell. Выберите один из следующих шагов установки, соответствующий вашей предпочитаемой среде:

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

Теперь перейдите в раздел, чтобы [установить двоичный файл клиента Istio истиоктл](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

В PowerShell используйте `Invoke-WebRequest`, чтобы скачать последнюю версию Istio, а затем извлеките с `Expand-Archive` следующим образом:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Теперь перейдите в раздел, чтобы [установить двоичный файл клиента Istio истиоктл](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Установка двоичного файла клиента Istio истиоктл

> [!IMPORTANT]
> Убедитесь, что выполняются действия, описанные в этом разделе, из папки верхнего уровня в выпуске Istio, который вы скачали и извлекли.

Клиентский двоичный файл `istioctl` выполняется на клиентском компьютере и позволяет взаимодействовать с Istio службы. Этапы установки в клиентских операционных системах немного отличаются. Выберите один из следующих шагов установки, соответствующий вашей предпочитаемой среде:

* [MacOS](#macos)
* [Подсистема Linux или Windows для Linux](#linux-or-windows-subsystem-for-linux)
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

Теперь перейдите к следующему разделу, чтобы [установить Istio КРДС на AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux или подсистема Linux для Windows

Используйте следующие команды, чтобы установить клиентский двоичный файл Istio `istioctl` в оболочке на основе Bash в Linux или [подсистеме Windows для Linux][install-wsl]. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

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

Теперь перейдите к следующему разделу, чтобы [установить Istio КРДС на AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Чтобы установить клиентский двоичный файл Istio `istioctl` в оболочке на основе **PowerShell**в Windows, используйте следующие команды. Эти команды копируют клиентский двоичный файл `istioctl` в папку Istio, а затем делают его доступными как немедленно (в текущей оболочке), так и постоянно (при перезапуске оболочки) с помощью `PATH`. Для выполнения этих команд не требуются повышенные привилегии (администратора), и вам не нужно перезапускать оболочку.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Теперь перейдите к следующему разделу, чтобы [установить Istio КРДС на AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Установка Istio КРДС на AKS

> [!IMPORTANT]
> Убедитесь, что выполняются действия, описанные в этом разделе, из папки верхнего уровня в выпуске Istio, который вы скачали и извлекли.

Istio использует [пользовательские определения ресурсов (КРДС)][kubernetes-crd] для управления конфигурацией среды выполнения. Сначала необходимо установить Istio КРДС, так как компоненты Istio имеют зависимость от них. Используйте Helm и диаграмму `istio-init` для установки Istio КРДС в пространство имен `istio-system` в кластере AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Задания][kubernetes-jobs] развертываются как часть диаграммы Helm `istio-init` для установки КРДС. Выполнение этих заданий должно занять от 1 до 2 минут в зависимости от среды кластера. Убедиться в успешном завершении заданий можно следующим образом.

```azurecli
kubectl get jobs -n istio-system
```

В следующем примере выходных данных показаны успешно выполненные задания.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Теперь, когда мы подтвердили успешное завершение заданий, давайте проверим, что установлено правильное число КРДС Istio. Чтобы убедиться, что все 53 Istio КРДС установлены, запустите соответствующую команду для своей среды. Команда должна возвращать число `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Если вы приступили к этому моменту, то это означает, что вы успешно установили Istio КРДС. Теперь перейдите к следующему разделу, чтобы [установить компоненты Istio на AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Установка компонентов Istio на AKS

> [!IMPORTANT]
> Убедитесь, что выполняются действия, описанные в этом разделе, из папки верхнего уровня в выпуске Istio, который вы скачали и извлекли.

Мы будем устанавливать [Grafana][grafana] и [киали][kiali] в рамках нашей Istio установки. Grafana предоставляет панели мониторинга для аналитики и мониторинга, а Киали — панель мониторинга сетки служб. В нашей установке каждому из этих компонентов требуются учетные данные, которые должны быть предоставлены в качестве [секрета][kubernetes-secrets].

Прежде чем можно будет установить компоненты Istio, необходимо создать секреты для Grafana и Киали. Создайте эти секреты, выполнив соответствующие команды для своей среды.

### <a name="add-grafana-secret"></a>Добавить секрет Grafana

Замените маркер `REPLACE_WITH_YOUR_SECURE_PASSWORD` паролем и выполните следующие команды:

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

#### <a name="windows"></a>Windows

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

### <a name="add-kiali-secret"></a>Добавить секрет Киали

Замените маркер `REPLACE_WITH_YOUR_SECURE_PASSWORD` паролем и выполните следующие команды:

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

#### <a name="windows"></a>Windows

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

Теперь, когда мы успешно создали секреты Grafana и Киали в нашем кластере AKS, пришло время установить компоненты Istio. Используйте Helm и диаграмму `istio` для установки компонентов Istio в пространство имен `istio-system` в кластере AKS. Используйте соответствующие команды для своей среды.

> [!NOTE]
> В процессе установки мы используем следующие варианты:
> - `global.controlPlaneSecurityEnabled=true` — взаимная поддержка TLS для плоскости управления
> - `mixer.adapters.useAdapterCRDs=false` — извлеките контрольные значения для адаптера микшера КРДС, так как они являются устаревшими, и это улучшит производительность
> - `grafana.enabled=true` — включить развертывание Grafana для аналитиков и мониторинга.
> - `grafana.security.enabled=true` — включение проверки подлинности для Grafana
> - `tracing.enabled=true` — включить развертывание Жаежер для трассировки
> - `kiali.enabled=true` — включение развертывания Киали для панели мониторинга сетки служб

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Helm-0 диаграмма развертывает большое количество объектов. @no__t Список можно просмотреть на основе выходных данных команды `helm install` выше. Для завершения развертывания компонентов Istio в зависимости от среды кластера может потребоваться от 4 до 5 минут.

> [!NOTE]
> Все Istio Pod должны быть запланированы для запуска на узлах Linux. При наличии пулов узлов Windows Server в дополнение к пулам узлов Linux в кластере убедитесь, что на узлах Linux запланировано выполнение всех Istioных модулей.

На этом этапе вы развернули Istio в кластере AKS. Чтобы убедиться в успешном развертывании Istio, перейдем к следующему разделу, чтобы [проверить установку Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Проверка установки Istio

Сначала убедитесь, что созданы все нужные службы. Используйте команду [kubectl Get SVC][kubectl-get] для просмотра выполняющихся служб. Запросите пространство имен `istio-system`, где компоненты Istio и Add-On были установлены `istio` диаграммой Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

В следующем примере выходных данных показаны службы, которые должны быть запущены:

- `istio-*` служб
- службы трассировки надстроек `jaeger-*`, `tracing` и `zipkin`
- `prometheus` — служба метрик
- `grafana` надстройка и мониторинг службы панели мониторинга
- `kiali` служба панели мониторинга для сетки службы надстроек

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

Затем убедитесь, что созданы все необходимые группы pod. Используйте команду [kubectl Get][kubectl-get] Pod и повторите запрос к пространству имен `istio-system`:

```console
kubectl get pods --namespace istio-system
```

В следующем примере выходных данных представлены запущенные группы pod:

- модули `istio-*`
- модуль метрик для надстройки `prometheus-*`
- Модуль мониторинга `grafana-*` надстройки и мониторинг
- модуль панели мониторинга для сетки службы надстроек `kiali`

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

Необходимо наличие двух модулей `istio-init-crd-*` с состоянием `Completed`. Эти модули не отвечают за выполнение заданий, создавших КРДС на предыдущем шаге. Все остальные модули типа данных должны отображать состояние `Running`. Если группы pod имеют другое состояние, подождите пару минут, чтобы отобразилось нужное. Если какая либо из модулей Pod сообщает о возникшей ошибке, используйте команду [kubectl, описывающую][kubectl-describe] , чтобы проверить их выходные данные и состояние.

## <a name="accessing-the-add-ons"></a>Доступ к надстройкам

В ходе описанного выше процесса для Istio устанавливается несколько надстроек, которые предоставляют дополнительные функции. Пользовательские интерфейсы для надстроек не предоставляются в открытый доступ через внешний IP-адрес. Чтобы получить доступ к пользовательским интерфейсам надстройки, используйте команду [переадресации порта kubectl][kubectl-port-forward] . Эта команда создает безопасное подключение между вашим клиентским компьютером и соответствующим Pod в кластере AKS.

Мы добавили дополнительный уровень безопасности для Grafana и Киали, указав учетные данные для них ранее в этой статье.

### <a name="grafana"></a>Grafana

Информационные панели аналитики и мониторинга для Istio предоставляются [Grafana][grafana]. Перешлите локальный порт `3000` на клиентском компьютере в порт `3000` в модуле Pod, на котором выполняется Grafana в кластере AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Теперь вы можете обратиться к Grafana по следующему URL-адресу на клиентском компьютере — [http://localhost:3000](http://localhost:3000). При появлении запроса не забудьте использовать учетные данные, созданные с помощью секрета Grafana ранее.

### <a name="prometheus"></a>Prometheus

Метрики для Istio предоставляются [Prometheus][prometheus]. Перешлите локальный порт `9090` на клиентском компьютере в порт `9090` в модуле Pod, на котором выполняется Prometheus в кластере AKS:

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

Трассировка в Istio предоставляется [жаежер][jaeger]. Перешлите локальный порт `16686` на клиентском компьютере в порт `16686` в модуле Pod, на котором выполняется Жаежер в кластере AKS:

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

Панель мониторинга сетки служб предоставляется [киали][kiali]. Перешлите локальный порт `20001` на клиентском компьютере в порт `20001` в модуле Pod, на котором выполняется Киали в кластере AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

В следующем примере выходных данных демонстрируется перенаправление портов, настроенное для Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Теперь вы можете обратиться к панели мониторинга Kiali для мониторинга сетки служб по следующему URL-адресу на клиентском компьютере — [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). При появлении запроса не забудьте использовать учетные данные, созданные с помощью секрета Киали ранее.

## <a name="uninstall-istio-from-aks"></a>Удаление Istio из AKS

> [!WARNING]
> Удаление Istio из работающей системы может привести к проблемам с трафиком между Вашими службами. Перед продолжением убедитесь, что вы выполнили предварительные условия для правильной работы системы без Istio.

### <a name="remove-istio-components-and-namespace"></a>Удалить компоненты и пространство имен Istio

Чтобы удалить Istio из кластера AKS, используйте следующие команды. Команды `helm delete` будут удалять диаграммы `istio` и `istio-init`, а команда `kubectl delete ns` удалит пространство имен `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Удалить Istio КРДС

Приведенные выше команды удаляют все компоненты и пространство имен Istio, но по-прежнему остались с Istio КРДС. Чтобы удалить КРДС, можно использовать один из следующих подходов.

Подход #1. Эта команда предполагает, что вы запускаете этот шаг из папки верхнего уровня скачанного и извлеченного выпуска Istio, который использовался для установки Istio с.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Подход #2 — Используйте одну из этих команд, если у вас больше нет доступа к скачанному и извлеченному выпуску Istio, который использовался для установки Istio с. Выполнение этой команды займет немного больше времени. это может занять несколько минут.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Следующие шаги

В следующей документации описывается, как можно использовать Istio для обеспечения интеллектуальной маршрутизации для развертывания ранний выпуска.

> [!div class="nextstepaction"]
> [Сценарий интеллектуальной маршрутизации AKS Istio][istio-scenario-routing]

Дополнительные сведения о других вариантах установки и настройки для Istio см. здесь:

- [Istio — руководства по установке Helm][istio-install-helm]
- [Параметры установки Istio-Helm][istio-install-helm-options]

Вы также можете выполнить дополнительные сценарии, используя [пример приложения Istio BookInfo][istio-bookinfo-example].

Сведения о мониторинге приложения AKS с помощью Application Insights и Istio см. в следующей документации Azure Monitor.
- [Мониторинг приложений с нулевым инструментированием для приложений, размещенных в Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
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
