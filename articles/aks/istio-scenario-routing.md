---
title: Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в Службе Azure Kubernetes (AKS)
description: Сведения об использовании Istio для обеспечения интеллектуальной маршрутизации и развертывания ранних выпусков в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000178"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в службе Azure Kubernetes (AKS)

[Istio][istio-github] — это сетка служб с открытым исходным кодом, которая предоставляет набор ключевых функций для микрослужб в кластере Kubernetes. включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. Дополнительные сведения о Istio см. в официальной документации о [Istio?][istio-docs-concepts] .

В этой статье показано, как использовать возможности управления трафиком в Istio. Пример приложения AKS для голосования позволит ознакомиться с интеллектуальной маршрутизацией и ранними выпусками.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Развертывание приложения
> * Обновление приложения
> * Ранний выпуск приложения
> * Финализация выпуска

## <a name="before-you-begin"></a>Перед началом работы

> [!NOTE]
> Этот сценарий был протестирован на соответствие версии `1.1.3`Istio.

В шагах, описанных в этой статье, предполагается, что вы `1.11` создали кластер AKS (Kubernetes и выше, с включенным `kubectl` RBAC) и установили подключение к кластеру. Вам также потребуется установить Istio в кластере.

Если вам нужна помощь по любому из этих элементов, ознакомьтесь с [кратким][aks-quickstart] руководством по AKS и [установите Istio в][istio-install] руководстве по AKS.

## <a name="about-this-application-scenario"></a>Сведения об этом сценарии приложения

Пример приложения AKS для голосования предоставляет пользователям два варианта голосования (**кошки** или **собаки**). Предоставляется компонент хранения, который сохраняет число голосов за каждый вариант. Кроме того, есть компонент аналитики, предоставляющий подробные сведения о голосах, поданных за каждый вариант.

В этом сценарии приложения вы начинаете с развертывания версии `1.0` приложения для голосования и версии `1.0` компонента аналитики. Компонент аналитики обеспечивает простой подсчет количества голосов. Приложение для голосования и компонент аналитики взаимодействуют с `1.0` версией компонента хранилища, который поддерживается Redis.

Вы обновляете компонент аналитики до версии `1.1`, которая предоставляет счетчики, а теперь суммирует и проценты.

Подмножество пользователей тестовой версии `2.0` приложения с помощью ранний выпуска. В этой новой версии используется компонент хранения, поддерживаемый базой данных MySQL.

Убедившись, что эта версия `2.0` будет правильно работать в подмножестве пользователей, вы сможете развернуть версию `2.0` для всех пользователей.

## <a name="deploy-the-application"></a>Развертывание приложения

Начнем с развертывания приложения в кластере службы Azure Kubernetes (AKS). На следующей схеме показано, что выполняется в конце этого раздела — версии `1.0` всех компонентов с входящими запросами, обслуживаемыми через шлюз входящих данных Istio:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-01.png)

Артефакты, которые необходимо выполнить вместе с этой статьей, доступны в репозитории [Azure-Samples/AKS-голосование-приложение][github-azure-sample] GitHub. Вы можете скачать эти артефакты или клонировать репозиторий следующим образом:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Перейдите в следующую папку в скачанном или клонированном репозитории и выполните из этой папки все следующие шаги:

```console
cd scenarios/intelligent-routing-with-istio
```

Сначала создайте пространство имен в кластере AKS для примера приложения AKS голосование с именем `voting` следующим образом:

```azurecli
kubectl create namespace voting
```

Задайте для пространства имен метку `istio-injection=enabled`. Эта метка указывает Istio автоматически внедрить прокси-серверы istio-proxy в качестве службы-расширения во все группы pod в этом пространстве имен.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Теперь давайте создадим компоненты для приложения AKS Voting. Создайте эти компоненты в пространстве `voting` имен, созданном на предыдущем шаге.

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
> Istio устанавливает определенные требования к группам pod и службам. Дополнительные сведения см. в [документации по требованиям к Istio для модулей Pod и служб][istio-requirements-pods-and-services].

Чтобы просмотреть созданные модули, используйте команду [kubectl Get][kubectl-get] Pod, как показано ниже.

```azurecli
kubectl get pods -n voting
```

В следующем примере выходных данных показано, что имеются три `voting-app` экземпляра Pod и один экземпляр `voting-analytics` обоих модулей и `voting-storage` . Каждая группа pod содержит два контейнера. Один из этих контейнеров является компонентом, а второй — `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Чтобы просмотреть сведения о Pod, используйте [kubectl описание Pod][kubectl-describe]. Замените имя группы pod ее именем в вашем кластере AKS из предыдущих выходных данных:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Контейнер автоматически был вставлен с помощью Istio для управления сетевым трафиком из компонентов, как показано в следующем примере выходных данных:

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

Вы не можете подключиться к приложению для голосования, пока не создадите [шлюз][istio-reference-gateway] Istio и [виртуальную службу][istio-reference-virtualservice]. Эти ресурсы Istio направляют трафик из шлюза Istio для входящего трафика по умолчанию в наше приложение.

> [!NOTE]
> **Шлюз** — это компонент на границе сетки служб, который получает входящий или исходящий трафик HTTP и TCP.
> 
> **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.

`kubectl apply` Используйте команду, чтобы развернуть шлюз и виртуальную службу YAML. Не забудьте указать пространство имен, в которое развертываются эти ресурсы.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

В следующем примере выходных данных показано создание нового шлюза и виртуальной службы.

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

В нижней части экрана отображается информация о том, `1.0` что приложение использует версию `voting-app` и версию `1.0` `voting-storage` (Redis).

## <a name="update-the-application"></a>Обновление приложения

Давайте выполним развертывание новой версии компонента аналитики. Эта новая версия `1.1` отображает итоги и проценты в дополнение к количеству для каждой категории.

На следующей схеме показано, что будет выполняться в конце этой версии `1.1` `voting-analytics` компонента. `voting-app` трафик направляется из компонента. Несмотря на то `1.0` , что `voting-analytics` версия нашего компонента по-своему работает `voting-analytics` и на нее ссылается служба, прокси-серверы Istio запрещают входящий и исходящий трафик.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-02.png)

Давайте выполним развертывание версии `1.1` `voting-analytics` компонента. Создайте этот компонент в `voting` пространстве имен:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

В следующем примере выходных данных показаны создаваемые ресурсы:

```console
deployment.apps/voting-analytics-1-1 created
```

Откройте снова пример приложения AKS для голосования в браузере, используя IP-адрес шлюза входящего трафика Istio, полученный на предыдущем шаге.

Браузер переключается между двумя представлениями, показанными ниже. Так как вы используете [службу][kubernetes-service] Kubernetes для `voting-analytics` компонента с единственным селектором меток (`app: voting-analytics`), Kubernetes использует поведение по умолчанию для циклического перебора между модулями Pod, которые соответствуют этому селектору. В этом случае это версия `1.0` и `1.1` для `voting-analytics` модулей Pod.

![Версия 1.0 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/deploy-app-01.png)

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Переключение между двумя версиями `voting-analytics` компонента можно визуализировать следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

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

Теперь давайте `1.1` заблокируем трафик только `voting-analytics` до версии компонента `voting-storage` и до версии `1.0` компонента. Затем следует определить правила маршрутизации для всех остальных компонентов.

> * **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.
> * **Правило назначения** определяет политики трафика и политики, связанные с определенной версией.
> * **Политика** определяет, какие методы проверки подлинности допустимы для рабочих нагрузок.

Используйте команду, чтобы заменить определение виртуальной службы `voting-app` в и добавить [правила назначения][istio-reference-destinationrule] и [Виртуальные службы][istio-reference-virtualservice] для других компонентов. `kubectl apply` Вы добавите [политику][istio-reference-policy] в `voting` пространство имен, чтобы обеспечить защиту всех взаимосвязи между службами с помощью взаимного TLS и сертификатов клиента.

* Политика имеет `peers.mtls.mode` `STRICT` значение, чтобы обеспечить принудительное применение взаимного протокола TLS `voting` между Вашими службами в пространстве имен.
* Мы также устанавливаем параметр `trafficPolicy.tls.mode` `ISTIO_MUTUAL` в значение во всех правилах назначения. Istio предоставляет службы с надежными удостоверениями и обеспечивает защиту обмена данными между службами с помощью взаимной поддержки TLS и сертификатов клиентов, которыми Istio управляет прозрачным образом.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

В следующем примере выходных данных показана новая политика, правила назначения и виртуальные службы, которые обновляются или создаются.

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Если вы снова откроете приложение AKS для голосования в браузере, `1.1` `voting-app` компонент будет использовать только новую `voting-analytics` версию компонента.

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Вы можете визуализировать, что теперь вы направляете только к версии `1.1` `voting-analytics` компонента, как показано ниже. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

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

Теперь давайте подтверждаем, что Istio использует взаимное TLS-соединение для защиты обмена данными между каждой из наших служб. Для этого мы будем использовать команду [authn TLS-Check][istioctl-authn-tls-check] в `istioctl` клиентском двоичном файле, которая принимает следующую форму.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Этот набор команд предоставляет сведения о доступе к указанным службам из всех модулей Pod, которые находятся в пространстве имен и соответствуют набору меток:

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

Powershell

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

В следующем примере выходных данных показано, что для каждого из наших запросов применяется взаимное TLS-применение. Выходные данные также показывают правила политики и назначения, обеспечивающие взаимное TLS:

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

Теперь `2.0` выполним развертывание новой версии `voting-app`компонентов, `voting-analytics`и `voting-storage` . Новый `voting-storage` компонент использует MySQL вместо Redis, `voting-app` а компоненты и `voting-analytics` обновляются, чтобы позволить им использовать этот новый `voting-storage` компонент.

`voting-app` Компонент теперь поддерживает функции флагов функций. Флаг компонента позволяет протестировать возможность Istio осуществить ранний выпуск для группы пользователей.

На следующей схеме показано, что вы используете в конце этого раздела.

* `1.0` Версиякомпонента`voting-analytics` , версия `1.1` компонента и версия`1.0` компонента могутвзаимодействовать`voting-storage` друг с другом. `voting-app`
* `2.0` Версиякомпонента`voting-analytics` , версия `2.0` компонента и версия`2.0` компонента могутвзаимодействовать`voting-storage` друг с другом. `voting-app`
* Версия `2.0`компонентадоступнатолько дляпользователей,длякоторыхустановленконкретныйфлагкомпонента.`voting-app` Управление этим изменением осуществляется с помощью флага компонента через файл cookie.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-03.png)

Прежде всего обновите правила назначения и виртуальные службы Istio для работы с новыми компонентами. Это гарантирует, что трафик не будет несоответствующим образом направляться к новым компонентам, и пользователи не получат доступ непредвиденным образом:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

В следующем примере выходных данных показаны правила назначения и обновляемые виртуальные службы.

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Теперь добавим объекты Kubernetes для новых компонентов версии `2.0` . Вы также обновите `voting-storage` службу, включив в `3306` нее порт для MySQL:

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

Дождитесь завершения работы `2.0` всех модулей Pod версии. Используйте команду [kubectl Get][kubectl-get] Pod для просмотра всех модулей Pod в `voting` пространстве имен:

```azurecli
kubectl get pods --namespace voting
```

Теперь вы можете переключаться между версией `1.0` и версией `2.0` (ранний) приложения для голосования. Переключатель флага компонента в нижней части экрана устанавливает файл cookie. Этот файл cookie используется `voting-app` виртуальной службой для направления пользователей в новую версию. `2.0`

![Версия 1.0 приложения AKS Voting — флаг компонента НЕ задан.](media/istio/canary-release-01.png)

![Версия 2.0 приложения AKS Voting — флаг компонента ЗАДАН.](media/istio/canary-release-02.png)

Количество голосов различается между версиями приложения. Это различие подчеркивает, что вы используете два разных внутренних средства для хранения данных.

## <a name="finalize-the-rollout"></a>Финализация выпуска

После успешного тестирования выпуска ранний обновите `voting-app` виртуальную службу, чтобы направить весь трафик в версию `2.0` `voting-app` компонента. Все пользователи увидят версию `2.0` приложения независимо от того, установлен ли флаг компонента:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-04.png)

Обновите все правила назначения, чтобы удалить версии компонентов, которые больше не нужны в активном состоянии. Затем обновите все виртуальные службы, убрав ссылки на эти версии.

Так как трафик больше не направляется ни на одну из старых версий компонентов, все эти развернутые компоненты теперь можно безопасно удалить.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-05.png)

Вы успешно выпустили новую версию приложения для голосования AKS Voting.

## <a name="clean-up"></a>Очистка 

Вы можете удалить приложение для голосования AKS, которое мы использовали в этом сценарии, из кластера AKS, `voting` удалив пространство имен следующим образом:

```azurecli
kubectl delete namespace voting
```

В следующем примере выходных данных показано, что все компоненты приложения AKS голосование были удалены из кластера AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сценарии можно изучить с помощью [примера приложения Istio BookInfo][istio-bookinfo-example].

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
