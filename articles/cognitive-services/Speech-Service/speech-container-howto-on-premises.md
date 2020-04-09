---
title: Используйте контейнеры для речевого обслуживания с Kubernetes и Helm
titleSuffix: Azure Cognitive Services
description: Используя Kubernetes и Helm для определения изображений контейнеров между речами и текстом к речи, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes на месте.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3c183f6d0e2d80ed497654448a726a1562bd046c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874364"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Используйте контейнеры для речевого обслуживания с Kubernetes и Helm

Одним из вариантов управления контейнерами для речевой речи является использование Kubernetes и Helm. Используя Kubernetes и Helm для определения изображений контейнеров между речами и текстом к речи, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes на месте. Наконец, мы рассмотрим, как протестировать развернутые службы и различные параметры конфигурации. Для получения дополнительной информации о запуске контейнеров Docker без согласования Kubernetes [см.](speech-container-howto.md)

## <a name="prerequisites"></a>Предварительные требования

Следующие предпосылки перед использованием речевых контейнеров на месте:

| Обязательно | Назначение |
|----------|---------|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][free-azure-account], прежде чем начинать работу. |
| Доступ к реестру контейнеров | Для того, чтобы Kubernetes вытащил докер изображения в кластер, он будет нуждаться в доступе к реестру контейнеров. |
| Кубернетес CLI | [Kubernetes CLI][kubernetes-cli] необходим для управления общими учетных данными из реестра контейнеров. Kubernetes также необходимо перед Helm, который является менеджером пакетов Kubernetes. |
| Интерфейс командной строки Helm | Установите [Helm CLI][helm-install], который используется для установки диаграммы руля (определение контейнерного пакета). |
|Речевой ресурс |Для использования контейнеров необходимо следующее:<br><br>Ресурс _Speech_ Azure для получения связанного ключа выставления счетов и конечных точек выставления счетов URI. Оба значения доступны на страницах **портала** Azure и ключей и необходимы для запуска контейнера.<br><br>**(API_KEY)**: ключ ресурса<br><br>**«ENDPOINT_URI»:** примером uri:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Рекомендуемая конфигурация компьютера-хозяина

В качестве ссылки на [данные компьютера контейнера службы speech.][speech-container-host-computer] Эта *диаграмма руля* автоматически вычисляет требования к процессору и памяти на основе количества декодированных (одновременных запросов), которые указывает пользователь. Кроме того, он будет корректироваться в зависимости от того, настраиваются ли оптимизации для аудио/текстовых ввода как. `enabled` График руля по умолчанию, два параллельных запроса и отключение оптимизации.

| Служба | Процессор / Контейнер | Память / Контейнер |
|--|--|--|
| **Преобразование речи в текст** | один декодер требует как минимум 1150 милликоров. Если `optimizedForAudioFile` включено, то требуется 1,950 милликоров. (по умолчанию: два декодера) | Требуется: 2 ГБ<br>Ограничено: 4 ГБ |
| **Преобразование текста в речь** | один одновременный запрос требует как минимум 500 милликоров. Если `optimizeForTurboMode` включено, то требуется 1000 милликоров. (по умолчанию: два одновременных запроса) | Требуется: 1 ГБ<br> Ограничено: 2 ГБ |

## <a name="connect-to-the-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Ожидается, что компьютер-хозяин будет иметь доступный кластер Kubernetes. Ознакомьтесь с этим учебником по [развертыванию кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) для получения концептуального понимания того, как развернуть кластер Kubernetes на компьютер-хост.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Обмен учетными данными Docker с кластером Kubernetes

Чтобы позволить кластеру `docker pull` Kubernetes настроить изображение (ы) из реестра `containerpreview.azurecr.io` контейнеров, необходимо перевести учетные данные докера в кластер. Выполните [`kubectl create`][kubectl-create] команду ниже, чтобы создать *секрет докер-реестра* на основе учетных данных, предоставленных из контейнерного реестра доступа предпосылки.

Из выбора интерфейса командной строки запустите следующую команду. Не забудьте `<username>`заменить `<password>` `<email-address>` , , и с реестром контейнера полномочия.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже `containerpreview.azurecr.io` есть доступ к реестру контейнеров, можно создать секрет Kubernetes, используя вместо этого общий флаг. Рассмотрим следующую команду, которая выполняется против конфигурации Docker JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Следующий выход печатается на консоли, когда секрет был успешно создан.

```console
secret "mcr" created
```

Чтобы убедиться, что секрет был [`kubectl get`][kubectl-get] создан, выполнить с флагом. `secrets`

```console
kubectl get secrets
```

Выполнение `kubectl get secrets` отпечатков все настроенные секреты.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы румов для развертывания

Посетите [концентратор Microsoft Helm][ms-helm-hub] для всех общедоступных диаграмм руля, предлагаемых корпорацией Майкрософт. В концентраторе Microsoft Helm вы найдете **диаграмму речи когнитивных служб On-Premises.** **Функция «Когнитивные службы speech On-Premises»** — это `config-values.yaml` диаграмма, которая мы установим, но сначала мы должны создать файл с явными конфигурациями. Начнем с добавления репозитория Майкрософт в наш экземпляр Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Далее мы наберем значения диаграммы Helm. Копировать и вставить следующие YAML `config-values.yaml`в файл с именем . Для получения дополнительной информации о настройке **когнитивных услуг речи On-Premises Helm Chart**, см. [customize helm charts](#customize-helm-charts) Замените `# {ENDPOINT_URI}` `# {API_KEY}` и комментарии с вашими собственными значениями.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Если `billing` стоимость `apikey` и значения не предоставляются, срок действия услуг истекает через 15 минут. Аналогичным образом, проверка не будет, так как службы не будут доступны.

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (Хелм-чарт)

*Диаграмма Helm* содержит конфигурацию, изображение которого докера (ы) вытащить из `containerpreview.azurecr.io` реестра контейнеров.

> [Диаграмма Helm][helm-charts] представляет собой набор файлов, описывающие связанный набор ресурсов Kubernetes. Единая диаграмма может быть использована для развертывания чего-то простого, например, мемкачанных стручок, или что-то сложное, например, полный стек веб-приложений с серверами HTTP, базами данных, кэшами и так далее.

Предоставленные *диаграммы Helm* тянут докерные изображения службы речи, как текстовые, так и `containerpreview.azurecr.io` услуги от речи к тексту из реестра контейнеров.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установка диаграммы Хелма в кластере Кубернете

Для установки *диаграммы руля* нам [`helm install`][helm-install-cmd] нужно выполнить `<config-values.yaml>` команду, заменив соответствующий аргумент пути и файл имени. Диаграмма `microsoft/cognitive-services-speech-onpremise` Helm, на ссылку ниже, доступна на [Microsoft Helm Hub здесь][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Вот пример вывода, который можно ожидать от успешного выполнения установки:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Развертывание Kubernetes может занять несколько минут. Чтобы подтвердить, что как стручки, так и службы должным образом развернуты и доступны, выполните следующую команду:

```console
kubectl get all
```

Вы должны ожидать увидеть что-то похожее на следующий вывод:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Проверить развертывание helm с тестами Helm

Установленные диаграммы Helm определяют *тесты Helm,* которые служат удобством для проверки. Эти тесты проверяют готовность службы. Для проверки служб **речевого текста** и текста в **речи** мы выполняем команду [тестирования Хелм.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Эти тесты не будут завершены, если статус POD `Running` `AVAILABLE` не является или если развертывание не перечислено в столбце. Будьте терпеливы, как это может занять более десяти минут, чтобы завершить.

Эти тесты будут результаты различных статусов:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

В качестве альтернативы выполнения *тестов руля*можно собирать *внешние IP-адреса* и `kubectl get all` соответствующие порты из команды. Используя IP и порт, откройте веб-браузер и перейдите к `http://<external-ip>:<port>:/swagger/index.html` просмотру страницы aPI swagger (ы).

## <a name="customize-helm-charts"></a>Настройка графиков хелм

Диаграммы руля иерархичны. Будучи иерархическим позволяет диаграммы наследования, он также обслуживает концепцию специфичности, где настройки, которые являются более конкретными переопределить унаследованные правила.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Дальнейшие действия

Для получения более подробной информации об установке приложений с шлемом в Azure Kubernetes Service (AKS), [посетите здесь][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Контейнеры когнитивных услуг][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
