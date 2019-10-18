---
title: Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в Службе Azure Kubernetes (AKS)
description: Сведения об использовании Istio для обеспечения интеллектуальной маршрутизации и развертывания ранних выпусков в кластере Службы Azure Kubernetes (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 48daf2be4a05922982479a86e6574f3aa85d2130
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530294"
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
> Этот сценарий был протестирован с Istio версии `1.3.2`.

В шагах, описанных в этой статье, предполагается, что вы создали кластер AKS (Kubernetes `1.13` и выше, с включенным RBAC) и установили `kubectl`ное подключение к кластеру. Вам также потребуется установить Istio в кластере.

Если вам нужна помощь по любому из этих элементов, ознакомьтесь с [кратким][aks-quickstart] руководством по AKS и [установите Istio в][istio-install] руководстве по AKS.

## <a name="about-this-application-scenario"></a>Сведения об этом сценарии приложения

Пример приложения AKS для голосования предоставляет пользователям два варианта голосования (**кошки** или **собаки**). Предоставляется компонент хранения, который сохраняет число голосов за каждый вариант. Кроме того, есть компонент аналитики, предоставляющий подробные сведения о голосах, поданных за каждый вариант.

В этом сценарии приложения вы начинаете с развертывания версии `1.0` приложения для голосования и версии `1.0` компонента аналитики. Компонент аналитики обеспечивает простой подсчет количества голосов. Приложение для голосования и компонент аналитики взаимодействуют с версией `1.0` компонента хранилища, который поддерживается Redis.

Компонент аналитики обновляется до версии `1.1`, которая предоставляет счетчики, а теперь суммирует и проценты.

Подмножество пользователей тестовой версии `2.0` приложения с помощью выпуска ранний. В этой новой версии используется компонент хранения, поддерживаемый базой данных MySQL.

Убедившись, что версия `2.0` работает так же, как и в подмножестве пользователей, вы сможете развернуть версию `2.0` для всех пользователей.

## <a name="deploy-the-application"></a>Развертывание приложения

Начнем с развертывания приложения в кластере службы Azure Kubernetes (AKS). На следующей схеме показаны действия, выполняемые в конце этого раздела — версия `1.0` всех компонентов с входящими запросами, обслуживаемыми через шлюз входящих данных Istio:

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-01.png)

Артефакты, которые необходимо выполнить вместе с этой статьей, доступны в репозитории [Azure-Samples/AKS-голосование-приложение][github-azure-sample] GitHub. Вы можете скачать эти артефакты или клонировать репозиторий следующим образом:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Перейдите в следующую папку в скачанном или клонированном репозитории и выполните из этой папки все следующие шаги:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Сначала создайте пространство имен в кластере AKS для примера приложения AKS для голосования с именем `voting` следующим образом:

```azurecli
kubectl create namespace voting
```

Задайте для пространства имен метку `istio-injection=enabled`. Эта метка указывает Istio автоматически внедрить прокси-серверы istio-proxy в качестве службы-расширения во все группы pod в этом пространстве имен.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Теперь давайте создадим компоненты для приложения AKS Voting. Создайте эти компоненты в пространстве имен `voting`, созданном на предыдущем шаге.

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
kubectl get pods -n voting --show-labels
```

В следующем примере выходных данных показано, что имеются три экземпляра модуля `voting-app` и один экземпляр и `voting-analytics`, и `voting-storage` модулей Pod. Каждая группа pod содержит два контейнера. Один из этих контейнеров является компонентом, а другой — `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Чтобы просмотреть сведения о Pod, мы будем использовать kubectl, [описывающие команду Pod][kubectl-describe] с помощью селекторов меток, чтобы выбрать модуль `voting-analytics`. Мы отфильтрим выходные данные, чтобы отобразить сведения о двух контейнерах, имеющихся в Pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Вы не можете подключиться к приложению для голосования, пока не создадите [шлюз][istio-reference-gateway] Istio и [виртуальную службу][istio-reference-virtualservice]. Эти ресурсы Istio направляют трафик из шлюза Istio для входящего трафика по умолчанию в наше приложение.

> [!NOTE]
> **Шлюз** — это компонент на границе сетки служб, который получает входящий или исходящий трафик HTTP и TCP.
> 
> **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.

Выполните команду `kubectl apply`, чтобы развернуть шлюз и виртуальную службу YAML. Не забудьте указать пространство имен, в которое развертываются эти ресурсы.

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

![Приложение AKS для голосования, работающее в кластере AKS с поддержкой Istio.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

В нижней части экрана отображается информация о том, что приложение использует версию `1.0` `voting-app` и `1.0` версии `voting-storage` (Redis).

## <a name="update-the-application"></a>Обновление приложения

Давайте выполним развертывание новой версии компонента аналитики. Эта новая версия `1.1` отображает итоги и проценты в дополнение к количеству для каждой категории.

На следующей схеме показано, что будет выполняться в конце этого раздела. только `1.1` версии компонента `voting-analytics` трафик направляется из компонента `voting-app`. Несмотря на то, что версия `1.0` компонента `voting-analytics` по-своему запускается и на нее ссылается служба `voting-analytics`, прокси-серверы Istio запрещают входящий и исходящий трафик.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-02.png)

Давайте разберем версию `1.1` компонента `voting-analytics`. Создайте этот компонент в пространстве имен `voting`:

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

В следующем примере выходных данных показаны создаваемые ресурсы:

```console
deployment.apps/voting-analytics-1-1 created
```

Откройте снова пример приложения AKS для голосования в браузере, используя IP-адрес шлюза входящего трафика Istio, полученный на предыдущем шаге.

Браузер переключается между двумя представлениями, показанными ниже. Так как вы используете [службу][kubernetes-service] Kubernetes для компонента `voting-analytics` с единственным селектором меток (`app: voting-analytics`), Kubernetes использует поведение по умолчанию для циклического перебора между модулями Pod, которые соответствуют этому селектору. В этом случае это версия `1.0` и `1.1` `voting-analytics` модулей Pod.

![Версия 1.0 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Переключение между двумя версиями компонента `voting-analytics` можно визуализировать следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

В следующем примере выходных данных показана значимая часть возвращаемого веб-сайта при переключении между версиями.

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Ограничение трафика версией 1.1 приложения

Теперь давайте заблокируем трафик только `1.1` версии компонента `voting-analytics` и до версии `1.0` компонента `voting-storage`. Затем следует определить правила маршрутизации для всех остальных компонентов.

> * **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.
> * **Правило назначения** определяет политики трафика и политики, связанные с определенной версией.
> * **Политика** определяет, какие методы проверки подлинности допустимы для рабочих нагрузок.

Используйте команду `kubectl apply`, чтобы заменить определение виртуальной службы на `voting-app` и добавить [правила назначения][istio-reference-destinationrule] и [Виртуальные службы][istio-reference-virtualservice] для других компонентов. Вы добавите [политику][istio-reference-policy] в пространство имен `voting`, чтобы обеспечить защиту всех взаимодействии между службами с помощью взаимного TLS и сертификатов клиента.

* Политика имеет `peers.mtls.mode` установлен в значение `STRICT`, чтобы обеспечить применение взаимного TLS-связи между Вашими службами в пространстве имен `voting`.
* Мы также устанавливаем `trafficPolicy.tls.mode` для `ISTIO_MUTUAL` во всех правилах назначения. Istio предоставляет службы с надежными удостоверениями и обеспечивает защиту обмена данными между службами с помощью взаимной поддержки TLS и сертификатов клиентов, которыми Istio управляет прозрачным образом.

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

Если вы снова откроете приложение AKS для голосования в браузере, компонент `voting-app` использует только новую версию `1.1` компонента `voting-analytics`.

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Вы можете визуализировать, что теперь вы направляете только `1.1` версии компонента `voting-analytics`, как показано ниже. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

В следующем примере выходных данных показана требуемая часть возвращаемого веб-сайта:

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Теперь давайте подтверждаем, что Istio использует взаимное TLS-соединение для защиты обмена данными между каждой из наших служб. Для этого мы будем использовать команду [authn TLS-Check][istioctl-authn-tls-check] для двоичного файла клиента `istioctl`, который принимает следующую форму.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Этот набор команд предоставляет сведения о доступе к указанным службам из всех модулей Pod, которые находятся в пространстве имен и соответствуют набору меток:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

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

Теперь выполним развертывание новой версии `2.0` `voting-app`, `voting-analytics` и компонентов `voting-storage`. Новый компонент `voting-storage` использует MySQL вместо Redis, а `voting-app` и `voting-analytics` компоненты обновляются, чтобы их можно было использовать в новом компоненте `voting-storage`.

Компонент `voting-app` теперь поддерживает функции флагов функций. Флаг компонента позволяет протестировать возможность Istio осуществить ранний выпуск для группы пользователей.

На следующей схеме показано, что вы используете в конце этого раздела.

* Версия `1.0` компонента `voting-app`, версия `1.1` компонента `voting-analytics` и версия `1.0` компонента `voting-storage` могут взаимодействовать друг с другом.
* Версия `2.0` компонента `voting-app`, версия `2.0` компонента `voting-analytics` и версия `2.0` компонента `voting-storage` могут взаимодействовать друг с другом.
* Версия `2.0` компонента `voting-app` доступна только для пользователей, для которых установлен флаг определенного компонента. Управление этим изменением осуществляется с помощью флага компонента через файл cookie.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-03.png)

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

Теперь добавим объекты Kubernetes для новой версии `2.0` компонентов. Вы также обновите службу `voting-storage`, включив в нее порт `3306` для MySQL:

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

Дождитесь, пока не будут работать все версии модулей `2.0`. Используйте команду [kubectl Get][kubectl-get] Pod с параметром наблюдения `-w`, чтобы отслеживать изменения всех модулей Pod в пространстве имен `voting`.

```azurecli
kubectl get pods --namespace voting -w
```

Теперь вы можете переключаться между версией `1.0` и версией `2.0` (ранний) приложения для голосования. Переключатель флага компонента в нижней части экрана устанавливает файл cookie. Этот файл cookie используется `voting-app` виртуальной службой для направления пользователей в новую версию `2.0`.

![Версия 1.0 приложения AKS Voting — флаг компонента НЕ задан.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Версия 2.0 приложения AKS Voting — флаг компонента ЗАДАН.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Количество голосов различается между версиями приложения. Это различие подчеркивает, что вы используете два разных внутренних средства для хранения данных.

## <a name="finalize-the-rollout"></a>Финализация выпуска

После успешного тестирования выпуска ранний обновите виртуальную службу `voting-app`, чтобы направить весь трафик в `2.0` версии компонента `voting-app`. После этого все пользователи увидят версию `2.0` приложения, независимо от того, установлен ли флаг компонента.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-04.png)

Обновите все правила назначения, чтобы удалить версии компонентов, которые больше не нужны в активном состоянии. Затем обновите все виртуальные службы, убрав ссылки на эти версии.

Так как трафик больше не направляется ни на одну из старых версий компонентов, все эти развернутые компоненты теперь можно безопасно удалить.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-05.png)

Вы успешно выпустили новую версию приложения для голосования AKS Voting.

## <a name="clean-up"></a>Очистка 

Вы можете удалить приложение для голосования AKS, которое мы использовали в этом сценарии, из кластера AKS, удалив пространство имен `voting` следующим образом:

```azurecli
kubectl delete namespace voting
```

В следующем примере выходных данных показано, что все компоненты приложения AKS голосование были удалены из кластера AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Дальнейшие действия

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
[istio-install]: ./servicemesh-istio-install.md
