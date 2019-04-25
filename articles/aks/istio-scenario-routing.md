---
title: Интеллектуальная маршрутизация и ранние выпуски с использованием Istio в Службе Azure Kubernetes (AKS)
description: Сведения об использовании Istio для обеспечения интеллектуальной маршрутизации и развертывания ранних выпусков в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/03/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465945"
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

В этой статье предполагается, что у вас есть кластер AKS (Kubernetes версии 1.10 и выше с поддержкой RBAC), к которому установлено подключение `kubectl`. Также в кластере потребуется установить Istio.

Если требуется помощь по любому из этих вопросов, см. [краткое руководство по AKS][aks-quickstart] и [руководство по установке Istio в AKS][istio-install].

## <a name="about-this-application-scenario"></a>Сведения об этом сценарии приложения

Пример приложения AKS для голосования предоставляет пользователям два варианта голосования (кошки или собаки). Предоставляется компонент хранения, который сохраняет число голосов за каждый вариант. Кроме того, есть компонент аналитики, предоставляющий подробные сведения о голосах, поданных за каждый вариант.

В этой статье описано, как развернуть версию *1.0* приложения для голосования и версию *1.0* компонента аналитики. Компонент аналитики обеспечивает простой подсчет количества голосов. Приложение для голосования и компонент аналитики взаимодействуют с версией *1.0* компонента хранения, поддержку которого обеспечивает Redis.

Выполняется обновление компонента аналитики до версии *1.1*, которая предоставляет счетчики, а также итоги и проценты.

Часть пользователей тестирует версию *2.0* приложения в виде раннего выпуска. В этой новой версии используется компонент хранения, поддерживаемый базой данных MySQL.

Убедившись в том, что версия *2.0* работает ожидаемым образом у группы пользователей, вы выпускаете версию *2.0* для всех пользователей.

## <a name="deploy-the-application"></a>Развертывание приложения

Начнем с развертывания приложения в кластере службы Azure Kubernetes (AKS). На следующей схеме показано, что выполняется в конце этого раздела — версия *1.0* всех компонентов с обслуживанием входящих запросов через шлюз входящего трафика Istio:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-01.png)

Необходимые для выполнения инструкций этой статьи артефакты доступны в репозитории [Azure-Samples/aks-voting-app][github-azure-sample] на GitHub. Вы можете скачать эти артефакты или клонировать репозиторий следующим образом:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Перейдите в следующую папку в скачанном или клонированном репозитории и выполните из этой папки все следующие шаги:

```console
cd scenarios/intelligent-routing-with-istio
```

Прежде всего создайте пространство имен в кластере AKS для примера приложения AKS для голосования с именем *voting* следующим образом:

```console
kubectl create namespace voting
```

Задайте для пространства имен метку `istio-injection=enabled`. Эта метка указывает Istio автоматически внедрить прокси-серверы istio-proxy в качестве службы-расширения во все группы pod в этом пространстве имен.

```console
kubectl label namespace voting istio-injection=enabled
```

Теперь давайте создадим компоненты для приложения AKS Voting. Эти компоненты следует создать в пространстве имен *voting*, созданном на предыдущем шаге.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

В следующем примере выходных данных показано, что ресурсы успешно созданы:

```
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
kubectl get pods -n voting
```

В следующем примере выходных данных показано, что существуют три экземпляра pod *voting-app* и по одному экземпляру pod *voting-analytics* и *voting-storage*. Каждая группа pod содержит два контейнера. Один из этих контейнеров представляет собой компонент, а другой — *istio-proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Чтобы просмотреть сведения о группе pod, воспользуйтесь командой [kubectl describe pod][kubectl-describe]. Замените имя группы pod ее именем в вашем кластере AKS из предыдущих выходных данных:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

Контейнер *istio-proxy* автоматически был внедрен средствами Istio для управления входящим и исходящим сетевым трафиком компонентов, как показано в следующем примере выходных данных:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         dockerhub.azk8s.cn/istio/proxyv2:1.0.4
[...]
```

Подключиться к приложению для голосования не удастся до тех пор, пока не будут созданы [шлюз Istio][istio-reference-gateway] и [виртуальная служба][istio-reference-virtualservice]. Эти ресурсы Istio направляют трафик из шлюза Istio для входящего трафика по умолчанию в наше приложение.

> [!NOTE]
> *Шлюз* — это компонент на границе сетки служб, который получает входящий или исходящий трафик HTTP и TCP.
>
> *Виртуальная служба* определяет набор правил маршрутизации для одной или нескольких служб назначения.

С помощью клиентского двоичного файла `istioctl` выполните развертывание YAML шлюза и виртуальной службы. Как и в случае команды `kubectl apply`, не забудьте указать пространство имен, в котором развертываются эти ресурсы.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Получите IP-адрес шлюза входящего трафика Istio с помощью следующей команды:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

В следующем примере выходных данных показан IP-адрес шлюза входящего трафика:

```
52.187.250.239
```

Откройте браузер и вставьте в адресную строку IP-адрес. Отобразится пример приложения AKS для голосования.

![Приложение AKS для голосования, работающее в кластере AKS с поддержкой Istio.](media/istio/deploy-app-01.png)

Сведения в нижней части экрана показывают, что приложение использует версию *1.0* *voting-app* и версию *1.0* (Redis) в качестве средства хранения.

## <a name="update-the-application"></a>Обновление приложения

Давайте развернем новую версию компонента аналитики. Эта новая версия *1.1* отображает итоги и проценты в дополнение к подсчету голосов за каждую категорию.

На следующей схеме показано, что выполняется в конце этого раздела — трафик из компонента *voting-app* направляется только в версию *1.1* нашего компонента *voting-analytics*. Хотя версия *1.0* нашего компонента *voting-analytics* продолжает выполняться и на нее ссылается служба *voting-analytics*, прокси-серверы Istio запрещают трафик к ней и от нее.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-02.png)

Давайте развернем версию *1.1* компонента *voting-analytics*. Создайте этот компонент в пространстве имен *voting*:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Откройте снова пример приложения AKS для голосования в браузере, используя IP-адрес шлюза входящего трафика Istio, полученный на предыдущем шаге.

Браузер переключается между двумя представлениями, показанными ниже. Так как [служба Kubernetes][kubernetes-service] используется для компонента *voting-analytics* только с селектором одиночной метки (`app: voting-analytics`), Kubernetes применяет поведение по умолчанию — циклическое переключение между группами pod, соответствующими этому селектору. В нашем примере это обе версии *1.0* и *1.1* групп pod *voting-analytics*.

![Версия 1.0 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/deploy-app-01.png)

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Можно визуализировать переключение между двумя версиями компонента *voting-analytics* следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
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

Теперь давайте заблокируем трафик, чтобы он поступал только к версии *1.1* компонента *voting-analytics* и версии *1.0* компонента *voting-storage*. Затем следует определить правила маршрутизации для всех остальных компонентов.

> * *Виртуальная служба* определяет набор правил маршрутизации для одной или нескольких служб назначения.
> * *Правило назначения* определяет политики трафика и политики, связанные с определенной версией.
> * *Политика* определяет, какие методы проверки подлинности допустимы для рабочих нагрузок.

С помощью клиентского двоичного файла `istioctl` следует заменить определение виртуальной службы для *voting-app* и добавить [целевые правила][istio-reference-destinationrule] и [виртуальные службы][istio-reference-virtualservice] для остальных компонентов.

Также следует добавить [политику][istio-reference-policy] к пространству имен *voting*, чтобы обеспечить защиту всех передаваемых между службами данных с помощью взаимной поддржки TLS и сертификатов клиентов.

Так как есть существующее определение виртуальной службы для *voting-app*, которое заменяется, выполните команду `istioctl replace` следующим образом:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

В следующем примере выходных данных показано, что виртуальная служба Istio успешно обновлена:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Далее воспользуйтесь командой `istioctl create` для добавления новой политики, а также новых правил назначения и виртуальных служб для всех остальных компонентов.

* В политике для `peers.mtls.mode` устанавливается значение `STRICT`, принудительно применяющее взаимную поддержку TLS для обмена данными между службами в пределах пространства имен *voting*.
* Также следует задать для `trafficPolicy.tls.mode` значение `ISTIO_MUTUAL` во всех правилах назначения. Istio предоставляет службы с надежными удостоверениями и обеспечивает защиту обмена данными между службами с помощью взаимной поддержки TLS и сертификатов клиентов, которыми Istio управляет прозрачным образом.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

В следующем примере выходных данных показано, что новая политика, правила назначения и виртуальные службы успешно созданы:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Если снова открыть в браузере приложение AKS Voting, компонентом *voting-app* будет использоваться только новая версия *1.1* компонента *voting-analytics*.

![Версия 1.1 компонента аналитики, выполняющаяся в приложении AKS Voting.](media/istio/update-app-01.png)

Можно еще проще отобразить перенаправление к версии *1.1* компонента *voting-analytics* следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio.

Можно отобразить перенаправление только к версии *1.1* компонента *voting-analytics* следующим образом. Не забудьте указать IP-адрес вашего шлюза входящего трафика Istio:

```azurecli
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

В следующем примере выходных данных показана требуемая часть возвращаемого веб-сайта:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Убедитесь, что Istio использует взаимную поддержку TLS для защиты обмена данными между каждой из наших служб. Следующие команды проверяют параметры TLS для каждой из служб *voting-app*:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

В следующем примере выходных данных показано, что взаимная поддержка TLS принудительно применяется для каждой из служб с помощью политики и целевых правил:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Ранний выпуск приложения

Теперь давайте развернем новую версию *2.0* компонентов *voting-app*, *voting-analytics* и *voting-storage*. В новом компоненте *voting-storage* используется MySQL вместо Redis, а компоненты *voting-app* и *voting-analytics* обновляются, что позволяет им использовать новый компонент *voting-storage*.

Теперь компонент *voting-app* поддерживает использование флага компонента. Флаг компонента позволяет протестировать возможность Istio осуществить ранний выпуск для группы пользователей.

На следующей схеме показано, что выполняется в конце этого раздела.

* Версия *1.0* компонента *voting-app*, версия *1.1* компонента *voting-analytics* и версия *1.0* компонента *voting-storage* способны обмениваться данными друг с другом.
* Версия *2.0* компонента *voting-app*, версия *2.0* компонента *voting-analytics* и версия *2.0* компонента *voting-storage* способны обмениваться данными друг с другом.
* Версия *2.0* компонента *voting-app* доступна только пользователям, для которых установлен определенный флаг компонента. Управление этим изменением осуществляется с помощью флага компонента через файл cookie.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-03.png)

Прежде всего обновите правила назначения и виртуальные службы Istio для работы с новыми компонентами. Это гарантирует, что трафик не будет несоответствующим образом направляться к новым компонентам, и пользователи не получат доступ непредвиденным образом:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

В следующем примере выходных данных показано, что целевые правила и виртуальные службы успешно обновлены:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Далее добавим объекты Kubernetes для компонентов новой версии *2.0*. Следует также обновить службу *voting-storage*, чтобы включить в нее порт *3306* для MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

В следующем примере выходных данных показано, что объекты Kubernetes успешно обновлены или созданы:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Подождите, пока не будут выполняться все группы pod версии *2.0*. Воспользуйтесь командой [kubectl get pods][kubectl-get] для просмотра всех групп pod в пространстве имен *voting*:

```azurecli
kubectl get pods --namespace voting
```

Теперь у вас должна быть возможность переключаться между версией *1.0* и версией *2.0* (ранний выпуск) приложения для голосования. Переключатель флага компонента в нижней части экрана устанавливает файл cookie. Этот файл cookie используется виртуальной службой *voting-app* для направления пользователей к новой версии *2.0*.

![Версия 1.0 приложения AKS Voting — флаг компонента НЕ задан.](media/istio/canary-release-01.png)

![Версия 2.0 приложения AKS Voting — флаг компонента ЗАДАН.](media/istio/canary-release-02.png)

Количество голосов различается между версиями приложения. Это различие подчеркивает, что вы используете два разных внутренних средства для хранения данных.

## <a name="finalize-the-rollout"></a>Финализация выпуска

После успешного тестирования раннего выпуска, обновите виртуальную службу *voting-app*, чтобы направить весь трафик к версии *2.0* компонента *voting-app*. После этого всем пользователям будет отображаться версия *2.0* приложения, независимо от того, задан ли флаг компонента:

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-04.png)

Обновите все правила назначения, чтобы удалить версии компонентов, которые больше не нужны в активном состоянии. Затем обновите все виртуальные службы, убрав ссылки на эти версии.

Так как трафик больше не направляется ни на одну из старых версий компонентов, все эти развернутые компоненты теперь можно безопасно удалить.

![Компоненты и маршрутизация приложения AKS для голосования.](media/istio/components-and-routing-05.png)

Вы успешно выпустили новую версию приложения для голосования AKS Voting.

## <a name="next-steps"></a>Дальнейшие действия

Вы можете исследовать дополнительные сценарии, используя [пример приложения Istio Bookinfo][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md