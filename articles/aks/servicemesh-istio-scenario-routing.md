---
title: Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в Службе Azure Kubernetes (AKS)
description: Сведения об использовании Istio для обеспечения интеллектуальной маршрутизации и развертывания ранних выпусков в кластере Службы Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273001"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в службе Azure Kubernetes (AKS)

Сетка [Istio][istio-github] — это решение с открытым исходным кодом, которое предоставляет набор ключевых функций для микрослужб в кластере Kubernetes, включая управление трафиком, удостоверения служб и защиту служб, а также применение политик и возможность мониторинга. См. дополнительные сведения об [Istio][istio-docs-concepts].

В этой статье показано, как использовать возможности управления трафиком в Istio. Пример приложения AKS для голосования позволит ознакомиться с интеллектуальной маршрутизацией и ранними выпусками.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание приложения
> * Обновление приложения
> * Ранний выпуск приложения
> * Финализация выпуска

## <a name="before-you-begin"></a>Перед началом

> [!NOTE]
> Этот сценарий был протестирован против `1.3.2`версии Istio.

Шаги, описанные в этой статье, предполагают, что вы `1.13` создали кластер AKS (Kubernetes `kubectl` и выше, с включенным RBAC) и установили связь с кластером. Вам также понадобится Istio, установленный в кластере.

Если вам нужна помощь с любым из этих элементов, то смотрите [быстрый запуск AKS][aks-quickstart] и [установите Istio в][istio-install] руководстве AKS.

## <a name="about-this-application-scenario"></a>Сведения об этом сценарии приложения

Образец AKS голосования приложение предоставляет два варианта голосования (**Кошки** или **собаки**) для пользователей. Предоставляется компонент хранения, который сохраняет число голосов за каждый вариант. Кроме того, есть компонент аналитики, предоставляющий подробные сведения о голосах, поданных за каждый вариант.

В этом сценарии приложения вы `1.0` начинаете с развертывания `1.0` версии приложения для голосования и версии компонента аналитики. Компонент аналитики обеспечивает простой подсчет количества голосов. Приложение для голосования и аналитический компонент взаимодействуют с версией `1.0` компонента хранения, которая поддерживается Redis.

Вы обновляете компонент `1.1`аналитики до версии, которая обеспечивает количество, а теперь итоги и проценты.

Подмножество пользователей тестирует версию `2.0` приложения через канарейку. В этой новой версии используется компонент хранения, поддерживаемый базой данных MySQL.

После того, как `2.0` вы уверены, что версия работает, как `2.0` ожидалось на подмножество пользователей, вы раскатать версию для всех ваших пользователей.

## <a name="deploy-the-application"></a>Развертывание приложения

Начнем с развертывания приложения в кластере службы Azure Kubernetes (AKS). Следующая диаграмма показывает, что работает к `1.0` концу этого раздела - версия всех компонентов с входящими запросами, обслуживаемыми через шлюз входа Istio:

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-01.png)

Необходимые для выполнения инструкций этой статьи артефакты доступны в репозитории [Azure-Samples/aks-voting-app][github-azure-sample] на GitHub. Вы можете скачать эти артефакты или клонировать репозиторий следующим образом:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Перейдите в следующую папку в скачанном или клонированном репозитории и выполните из этой папки все следующие шаги:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Во-первых, создайте пространство имен в кластере AKS `voting` для примера приложения для голосования AKS, названного следующим образом:

```console
kubectl create namespace voting
```

Задайте для пространства имен метку `istio-injection=enabled`. Эта метка указывает Istio автоматически внедрить прокси-серверы istio-proxy в качестве службы-расширения во все группы pod в этом пространстве имен.

```console
kubectl label namespace voting istio-injection=enabled
```

Теперь давайте создадим компоненты для приложения AKS Voting. Создайте эти `voting` компоненты в пространстве имен, созданном на предыдущем этапе.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Следующий пример вывода показывает создаваемые ресурсы:

```output
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

```console
kubectl get pods -n voting --show-labels
```

Следующий вывод приведен в следующем примере показывает, что есть три экземпляра `voting-app` стручка и один экземпляр как `voting-analytics` стручков, так и `voting-storage` стручков. Каждая группа pod содержит два контейнера. Один из этих контейнеров является компонентом, `istio-proxy`а другой :

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Чтобы увидеть информацию о капсуле, мы будем использовать команду [kubectl, описываем команду стручка][kubectl-describe] с селекторами `voting-analytics` меток, чтобы выбрать стручок. Мы отфильтруем выход, чтобы показать детали двух контейнеров, присутствующих в капсуле:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Подключиться к приложению для голосования не удастся до тех пор, пока не будут созданы [шлюз Istio][istio-reference-gateway] и [виртуальная служба][istio-reference-virtualservice]. Эти ресурсы Istio направляют трафик из шлюза Istio для входящего трафика по умолчанию в наше приложение.

> [!NOTE]
> **Шлюз** — это компонент на границе сетки служб, который получает входящий или исходящий трафик HTTP и TCP.
> 
> **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.

Используйте `kubectl apply` команду для развертывания шлюза и виртуального сервиса yaml. Не забудьте указать пространство имен, в которое развернуты эти ресурсы.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Следующий пример вывода показывает новый шлюз и виртуальную службу создаются:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Получите IP-адрес шлюза входящего трафика Istio с помощью следующей команды:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

В следующем примере выходных данных показан IP-адрес шлюза входящего трафика:

```output
20.188.211.19
```

Откройте браузер и вставьте в адресную строку IP-адрес. Отобразится пример приложения AKS для голосования.

![Приложение AKS для голосования, работающее в кластере AKS с поддержкой Istio.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Информация в нижней части экрана показывает, `1.0` `voting-app` что `1.0` приложение `voting-storage` использует версию и версию (Redis).

## <a name="update-the-application"></a>Обновление приложения

Давайте развернем новую версию компонента аналитики. Эта новая `1.1` версия отображает итоги и проценты в дополнение к подсчету для каждой категории.

Следующая диаграмма показывает, что будет работать в конце `1.1` этого `voting-analytics` раздела - только `voting-app` версия нашего компонента имеет трафик, направленный из компонента. Несмотря `1.0` на `voting-analytics` то, что версия нашего компонента продолжает работать и ссылается на службу, `voting-analytics` прокси Istio не позволяют трафику и из него.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-02.png)

Развернем версию `1.1` компонента. `voting-analytics` Создайте этот `voting` компонент в пространстве имен:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Следующий пример вывода показывает создаваемые ресурсы:

```output
deployment.apps/voting-analytics-1-1 created
```

Откройте снова пример приложения AKS для голосования в браузере, используя IP-адрес шлюза входящего трафика Istio, полученный на предыдущем шаге.

Браузер переключается между двумя представлениями, показанными ниже. Так как вы используете [службу][kubernetes-service] `voting-analytics` Kubernetes для компонента`app: voting-analytics`только с одним селектораметом метки ( ), Kubernetes использует поведение по умолчанию кругового между стручками, которые соответствуют этому селектору. В этом случае, это `1.0` `1.1` и `voting-analytics` версия, и ваши стручки.

![Версия 1.0 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Вы можете визуализировать переключение `voting-analytics` между двумя версиями компонента следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

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

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Ограничение трафика версией 1.1 приложения

Теперь давайте заблокируем `1.1` трафик `voting-analytics` только для `1.0` версии компонента и для версии компонента. `voting-storage` Затем следует определить правила маршрутизации для всех остальных компонентов.

> * **Виртуальная служба** определяет набор правил маршрутизации для одной или нескольких служб назначения.
> * **Правило назначения** определяет политики трафика и политики, связанные с определенной версией.
> * **Политика** определяет, какие методы проверки подлинности допустимы для рабочих нагрузок.

Используйте `kubectl apply` команду, чтобы заменить `voting-app` определение виртуальной службы на вашем и добавить [правила назначения][istio-reference-destinationrule] и [виртуальные услуги][istio-reference-virtualservice] для других компонентов. Вы добавите [Политику][istio-reference-policy] `voting` в пространство имен, чтобы гарантировать, что все связи между службами защищены с помощью взаимных TLS и сертификатов клиента.

* Политика установила, `peers.mtls.mode` `STRICT` что взаимный TLS обеспечивается между вашими службами в пространстве `voting` имен.
* Мы также `trafficPolicy.tls.mode` устанавливаем во `ISTIO_MUTUAL` всех наших правилах назначения. Istio предоставляет службы с надежными удостоверениями и обеспечивает защиту обмена данными между службами с помощью взаимной поддержки TLS и сертификатов клиентов, которыми Istio управляет прозрачным образом.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Следующий пример показывает новую политику, правила назначения и виртуальные службы, обновляемые/созданные:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Если вы снова откроете приложение AKS Voting в `1.1` браузере, `voting-analytics` `voting-app` компонент использует только новую версию компонента.

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Вы можете представить, что теперь вы `1.1` только `voting-analytics` направляетсяе к версии компонента следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio:

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

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Давайте теперь подтвердим, что Istio использует взаимные TLS для обеспечения связи между каждым из наших сервисов. Для этого мы будем использовать [authn tls-check][istioctl-authn-tls-check] команду на `istioctl` клиентском двоичном, который принимает следующую форму.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Этот набор команд предоставляет информацию о доступе к указанным службам, из всех стручков, которые находятся в пространстве имен и соответствуют набору меток:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Этот следующий вывод показывает, что взаимный TLS применяется для каждого из наших запросов выше. На выходе также показаны правила политики и назначения, которые обеспечивают соблюдение взаимных TLS:

```output
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

Теперь давайте развернем `2.0` новую `voting-app` `voting-analytics`версию `voting-storage` , и компоненты. Новый `voting-storage` компонент использует MyS'L вместо `voting-app` Redis, и `voting-analytics` и компоненты `voting-storage` обновляются, чтобы позволить им использовать этот новый компонент.

Компонент `voting-app` теперь поддерживает функциональность флага функции функции. Флаг компонента позволяет протестировать возможность Istio осуществить ранний выпуск для группы пользователей.

Следующая диаграмма показывает, что вы будете работать в конце этого раздела.

* Версия `1.0` `voting-app` компонента, `1.1` версия `voting-analytics` компонента `1.0` и `voting-storage` версия компонента способны общаться друг с другом.
* Версия `2.0` `voting-app` компонента, `2.0` версия `voting-analytics` компонента `2.0` и `voting-storage` версия компонента способны общаться друг с другом.
* Версия `2.0` компонента `voting-app` доступна только пользователям, которые имеют определенный набор флагов функций. Управление этим изменением осуществляется с помощью флага компонента через файл cookie.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-03.png)

Прежде всего обновите правила назначения и виртуальные службы Istio для работы с новыми компонентами. Это гарантирует, что трафик не будет несоответствующим образом направляться к новым компонентам, и пользователи не получат доступ непредвиденным образом:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Следующий пример показывает правила назначения и виртуальные службы, обновляемые:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Далее давайте добавим объекты Kubernetes `2.0` для компонентов новой версии. Вы также `voting-storage` обновляете услугу, чтобы включить `3306` порт для MyS'L:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

В следующем примере выходных данных показано, что объекты Kubernetes успешно обновлены или созданы:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Подождите, пока `2.0` все стручки версии работают. Используйте [команду kubectl получить стручки][kubectl-get] с переключателем `-w` часов, чтобы `voting` следить за изменениями на всех стручках в пространстве имен:

```console
kubectl get pods --namespace voting -w
```

Теперь вы должны иметь возможность `1.0` переключаться между версией и версией (канарейка) `2.0` приложения для голосования. Переключатель флага компонента в нижней части экрана устанавливает файл cookie. Это файлcookieое `voting-app` печенье используется виртуальной `2.0`службой для направления пользователей в новую версию.

![Версия 1.0 приложения AKS Voting — флаг компонента НЕ задан.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Версия 2.0 приложения AKS Voting — флаг компонента ЗАДАН.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Количество голосов различается между версиями приложения. Это различие подчеркивает, что вы используете два разных внутренних средства для хранения данных.

## <a name="finalize-the-rollout"></a>Финализация выпуска

После успешного тестирования релиза canary, обновите виртуальную `voting-app` службу, чтобы направить весь трафик в версию `2.0` компонента. `voting-app` Затем все пользователи видят версию `2.0` приложения, независимо от того, установлен флаг функции или нет:

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-04.png)

Обновите все правила назначения, чтобы удалить версии компонентов, которые больше не нужны в активном состоянии. Затем обновите все виртуальные службы, убрав ссылки на эти версии.

Так как трафик больше не направляется ни на одну из старых версий компонентов, все эти развернутые компоненты теперь можно безопасно удалить.

![Компоненты и маршрутизация приложения AKS для голосования.](media/servicemesh/istio/scenario-routing-components-05.png)

Вы успешно выпустили новую версию приложения для голосования AKS Voting.

## <a name="clean-up"></a>Очистка 

Вы можете удалить приложение для голосования AKS, которое мы использовали `voting` в этом сценарии, из кластера AKS, удалив пространство имен следующим образом:

```console
kubectl delete namespace voting
```

Следующий вывод показывает, что все компоненты приложения для голосования AKS были удалены из кластера AKS.

```output
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
[istio-install]: ./servicemesh-istio-install.md
