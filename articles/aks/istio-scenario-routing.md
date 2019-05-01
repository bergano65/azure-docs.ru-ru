---
title: Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в Службе Azure Kubernetes (AKS)
description: Сведения об использовании Istio для обеспечения интеллектуальной маршрутизации и развертывания ранних выпусков в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702203"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в службе Azure Kubernetes (AKS)

Сетка [Istio][istio-github] — это решение с открытым исходным кодом, которое предоставляет набор ключевых функций для микрослужб в кластере Kubernetes, включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. См. дополнительные сведения об [Istio][istio-docs-concepts].

В этой статье показано, как использовать возможности управления трафиком в Istio. Пример приложения AKS для голосования позволит ознакомиться с интеллектуальной маршрутизацией и ранними выпусками.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Развертывание приложения
> * Обновление приложения
> * Ранний выпуск приложения
> * Финализация выпуска

## <a name="before-you-begin"></a>Перед началом работы

> [!NOTE]
> Этот сценарий был проверен на соответствие версии Istio `1.1.3`.

Действия, описанные в этой статье предполагается, вы создали кластер службы контейнеров AZURE (Kubernetes `1.11` и более поздних версий, с помощью RBAC включен) и установить `kubectl` соединение с кластером. Вам также потребуется Istio, установленный в кластере.

Если вам нужна помощь с любой из этих элементов, затем см. в разделе [краткое руководство AKS] [ aks-quickstart] и [установить Istio в AKS] [ istio-install] рекомендации.

## <a name="about-this-application-scenario"></a>Сведения об этом сценарии приложения

Приложение для голосования AKS образец предоставляет два варианта голосования (**кошки** или **собаки**) для пользователей. Предоставляется компонент хранения, который сохраняет число голосов за каждый вариант. Кроме того, есть компонент аналитики, предоставляющий подробные сведения о голосах, поданных за каждый вариант.

В этом случае приложение, начните с развертывания версии `1.0` приложение для голосования и версии `1.0` компонента аналитики. Компонент аналитики обеспечивает простой подсчет количества голосов. Приложение для голосования и analytics компонента взаимодействуют с версии `1.0` компонента хранилища, поддерживаемый Redis.

Обновление компонента аналитики до версии `1.1`, который предоставляет счетчики и теперь итогов и процентов.

Подмножество пользователей тестовой версии `2.0` приложения с помощью раннего выпуска. В этой новой версии используется компонент хранения, поддерживаемый базой данных MySQL.

Когда вы уверены в этой версии `2.0` работает нужным образом на подмножеством пользователей, развертыванием версии `2.0` для всех пользователей.

## <a name="deploy-the-application"></a>Развертывание приложения

Начнем с развертывания приложения в кластере службы Azure Kubernetes (AKS). На следующей схеме показано, что работает в конце этого раздела — версия `1.0` всех компонентов с помощью входящих запросов, обслуживаемых через шлюз входящего трафика Istio:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-01.png)

Необходимые для выполнения инструкций этой статьи артефакты доступны в репозитории [Azure-Samples/aks-voting-app][github-azure-sample] на GitHub. Вы можете скачать эти артефакты или клонировать репозиторий следующим образом:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Перейдите в следующую папку в скачанном или клонированном репозитории и выполните из этой папки все следующие шаги:

```console
cd scenarios/intelligent-routing-with-istio
```

Во-первых, создание пространства имен в кластере AKS для примера AKS голосования приложения с именем `voting` следующим образом:

```azurecli
kubectl create namespace voting
```

Задайте для пространства имен метку `istio-injection=enabled`. Эта метка указывает Istio автоматически внедрить прокси-серверы istio-proxy в качестве службы-расширения во все группы pod в этом пространстве имен.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Теперь давайте создадим компоненты для приложения AKS Voting. Создание этих компонентов в `voting` пространство имен, созданное на предыдущем шаге.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

В следующем примере выходных данных показаны создаваемые ресурсы:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio устанавливает определенные требования к группам pod и службам. Дополнительные сведения см. в [документации по требованиям к группам pod и службам в Istio][istio-requirements-pods-and-services].

Чтобы просмотреть созданные группы pod, выполните команду [kubectl get pods][kubectl-get] следующим образом:

```azurecli
kubectl get pods -n voting
```

В следующем примере выходных данных показано, существует три экземпляра `voting-app` pod и один экземпляр оба `voting-analytics` и `voting-storage` модулей POD. Каждая группа pod содержит два контейнера. Один из этих контейнеров — это компонент, а другой — `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Чтобы просмотреть сведения о группе pod, воспользуйтесь командой [kubectl describe pod][kubectl-describe]. Замените имя группы pod ее именем в вашем кластере AKS из предыдущих выходных данных:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Контейнера автоматически был вставлен с Istio для управления сетевым трафиком и из компонентов, как показано в следующем примере выходных данных:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Подключиться к приложению для голосования не удастся до тех пор, пока не будут созданы [шлюз Istio][istio-reference-gateway] и [виртуальная служба][istio-reference-virtualservice]. Эти ресурсы Istio направляют трафик из шлюза Istio для входящего трафика по умолчанию в наше приложение.

> [!NOTE]
> **Шлюз** — это компонент на границе сетки служб, который получает входящий или исходящий трафик HTTP и TCP.
> 
> **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.

Используйте `kubectl apply` команду, чтобы развернуть yaml шлюза и службы виртуальной сети. Не забудьте указать пространство имен, эти ресурсы развертываются в.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

В следующем примере выходных данных показывает новый шлюз и создаваемой виртуальной службы:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Получите IP-адрес шлюза входящего трафика Istio с помощью следующей команды:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

В следующем примере выходных данных показан IP-адрес шлюза входящего трафика:

```
20.188.211.19
```

Откройте браузер и вставьте в адресную строку IP-адрес. Отобразится пример приложения AKS для голосования.

![Приложение AKS для голосования, работающее в кластере AKS с поддержкой Istio.](media/istio/deploy-app-01.png)

Информация в нижней части экрана показывает, что приложение использует версию `1.0` из `voting-app` и версии `1.0` из `voting-storage` (Redis).

## <a name="update-the-application"></a>Обновление приложения

Давайте развернем новую версию компонента аналитики. Эта новая версия `1.1` отображает суммы и проценты в дополнение к счетчик для каждой категории.

На следующей схеме показано, что выполняется в конце этого раздела — только версии `1.1` из наших `voting-analytics` компонент имеет трафик направляется из `voting-app` компонента. Несмотря на то что версия `1.0` из наших `voting-analytics` компонент продолжает выполняться и ссылается `voting-analytics` службы, прокси-серверы Istio запрещают трафик к и из него.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-02.png)

Давайте Развернем версии `1.1` из `voting-analytics` компонента. Создать этот компонент в `voting` пространство имен:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

В следующем примере выходных данных показаны создаваемые ресурсы:

```console
deployment.apps/voting-analytics-1-1 created
```

Откройте снова пример приложения AKS для голосования в браузере, используя IP-адрес шлюза входящего трафика Istio, полученный на предыдущем шаге.

Браузер переключается между двумя представлениями, показанными ниже. Так как при использовании Kubernetes [службы] [ kubernetes-service] для `voting-analytics` компонент с помощью только одну метку селектора (`app: voting-analytics`), Kubernetes использует поведение по умолчанию циклического модули, которые соответствуют этом селекторе. В данном случае это обе версии `1.0` и `1.1` из вашей `voting-analytics` модулей POD.

![Версия 1.0 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/deploy-app-01.png)

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Вы можете визуализировать, переключение между двумя версиями `voting-analytics` компонент следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

В следующем примере выходных данных показана значимая часть возвращаемого веб-сайта при переключении между версиями.

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Ограничение трафика версией 1.1 приложения

Теперь давайте заблокировать трафик только версии `1.1` из `voting-analytics` компонента и до версии `1.0` из `voting-storage` компонента. Затем следует определить правила маршрутизации для всех остальных компонентов.

> * **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.
> * **Правило назначения** определяет политики трафика и политики, связанные с определенной версией.
> * **Политика** определяет, какие методы проверки подлинности допустимы для рабочих нагрузок.

Используйте `kubectl apply` команду, чтобы заменить определение службы виртуальной сети на ваш `voting-app` и добавьте [правила назначения] [ istio-reference-destinationrule] и [виртуальные службы] [ istio-reference-virtualservice] для других компонентов. Вы добавите [политики] [ istio-reference-policy] для `voting` пространства имен, чтобы убедиться, что все обмена данными между службами защищается с помощью сертификатов клиента и взаимной TLS.

* Политика имеет `peers.mtls.mode` присвоено `STRICT` чтобы обеспечить соблюдение взаимной TLS между службами в `voting` пространства имен.
* Мы также зададим `trafficPolicy.tls.mode` для `ISTIO_MUTUAL` в все наши правила назначения. Istio предоставляет службы с надежными удостоверениями и обеспечивает защиту обмена данными между службами с помощью взаимной поддержки TLS и сертификатов клиентов, которыми Istio управляет прозрачным образом.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

В следующем примере выходных данных показано новую политику, правила назначения, а также виртуальной службы, будут обновлены или создать.

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

При открытии в нем приложение Vote AKS в браузере снова, только новая версия `1.1` из `voting-analytics` компонент используется `voting-app` компонента.

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Вы можете визуализировать, что вы теперь только направляются к версии `1.1` из вашей `voting-analytics` компонент следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

В следующем примере выходных данных показана требуемая часть возвращаемого веб-сайта:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Давайте теперь убедитесь, что Istio использует взаимной TLS для защиты обмена данными между каждым из наших служб. Для этого мы будем использовать [проверка tls authn] [ istioctl-authn-tls-check] команды `istioctl` клиент двоичных данных, который имеет следующий вид.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Этот набор команд предоставляют сведения о доступе к указанным службам из всех модулей, которые находятся в пространстве имен и соответствовать набору метки:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Это ниже показано пример применения взаимной TLS для каждого из наших запросов выше. Выходные данные также показывает, политики и правила назначения, обеспечивающий взаимной TLS:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Ранний выпуск приложения

Теперь давайте Развернем новую версию `2.0` из `voting-app`, `voting-analytics`, и `voting-storage` компонентов. Новый `voting-storage` компонент использовать MySQL вместо Redis и `voting-app` и `voting-analytics` компоненты обновляются, чтобы они могли использовать это новое `voting-storage` компонента.

`voting-app` Компонент теперь поддерживает флаг функциональные возможности. Флаг компонента позволяет протестировать возможность Istio осуществить ранний выпуск для группы пользователей.

На следующей схеме показано, что у вас будет в конце этого раздела.

* Версия `1.0` из `voting-app` компонент, версия `1.1` из `voting-analytics` компонент и версия `1.0` из `voting-storage` компонента, могут взаимодействовать друг с другом.
* Версия `2.0` из `voting-app` компонент, версия `2.0` из `voting-analytics` компонент и версия `2.0` из `voting-storage` компонента, могут взаимодействовать друг с другом.
* Версия `2.0` из `voting-app` компонентов доступны только тем пользователям, которым установлен флаг определенной функции. Управление этим изменением осуществляется с помощью флага компонента через файл cookie.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-03.png)

Прежде всего обновите правила назначения и виртуальные службы Istio для работы с новыми компонентами. Это гарантирует, что трафик не будет несоответствующим образом направляться к новым компонентам, и пользователи не получат доступ непредвиденным образом:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

В следующем примере выходных данных показано правила назначения и обновление виртуальных служб.

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Далее добавим объекты Kubernetes для новой версии `2.0` компонентов. Можно также обновить `voting-storage` сервису включать `3306` порт для MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

В следующем примере выходных данных показано, что объекты Kubernetes успешно обновлены или созданы:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Подождите, пока все версии `2.0` POD будут запущены. Используйте [kubectl get pods] [ kubectl-get] команду, чтобы просмотреть все модули в `voting` пространство имен:

```azurecli
kubectl get pods --namespace voting
```

Теперь можно переключиться между версией `1.0` и версии `2.0` (ранний) приложения для голосования. Переключатель флага компонента в нижней части экрана устанавливает файл cookie. Этот файл cookie используется `voting-app` виртуальной службы для перенаправления пользователей в новой версии `2.0`.

![Версия 1.0 приложения AKS Voting — флаг компонента НЕ задан.](media/istio/canary-release-01.png)

![Версия 2.0 приложения AKS Voting — флаг компонента ЗАДАН.](media/istio/canary-release-02.png)

Количество голосов различается между версиями приложения. Это различие подчеркивает, что вы используете два разных внутренних средства для хранения данных.

## <a name="finalize-the-rollout"></a>Финализация выпуска

После успешного тестирования раннего выпуска, обновить `voting-app` виртуальной службы, чтобы направить весь трафик к версии `2.0` из `voting-app` компонента. Затем будет видна всем пользователям версии `2.0` приложения, независимо от того, задан ли флаг компонента:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-04.png)

Обновите все правила назначения, чтобы удалить версии компонентов, которые больше не нужны в активном состоянии. Затем обновите все виртуальные службы, убрав ссылки на эти версии.

Так как трафик больше не направляется ни на одну из старых версий компонентов, все эти развернутые компоненты теперь можно безопасно удалить.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-05.png)

Вы успешно выпустили новую версию приложения для голосования AKS Voting.

## <a name="clean-up"></a>Очистка 

Можно удалить приложение голосования AKS, мы использовали в этом сценарии из кластера AKS, удалив `voting` пространства имен следующим образом:

```azurecli
kubectl delete namespace voting
```

В следующем примере выходных данных показано, что все компоненты приложения голосования AKS были удалены из кластера AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете исследовать дополнительные сценарии, используя [пример приложения Istio Bookinfo][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
