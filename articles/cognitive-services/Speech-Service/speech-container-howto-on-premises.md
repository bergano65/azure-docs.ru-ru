---
title: Использовать Kubernetes на предприятии
titleSuffix: Azure Cognitive Services
description: Использование Helm и Kubernetes для определения образы контейнеров речи в текст и преобразования текста в речь, мы создадим пакет Kubernetes. Этот пакет будет развернут кластер Kubernetes на предприятии.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786252"
---
# <a name="use-kubernetes-on-premises"></a>Использовать Kubernetes на предприятии

Использование Helm и Kubernetes для определения образы контейнеров речи в текст и преобразования текста в речь, мы создадим пакет Kubernetes. Этот пакет будет развернут кластер Kubernetes на предприятии. Наконец мы рассмотрим, как проверить развернутых служб и различные параметры конфигурации.

## <a name="prerequisites"></a>Предварительные требования

Перед использованием речи контейнеры в локальной, необходимо выполнить следующие условия:

|Обязательно для заполнения|Цель|
|--|--|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][free-azure-account], прежде чем начинать работу. |
| Доступ к реестру контейнеров | Чтобы Kubernetes получить образы docker в кластер ему понадобится доступ к реестру контейнеров. Вам нужно [запросить доступ к реестру контейнеров][speech-preview-access] первого. |
| Интерфейс командной строки Kubernetes | [Интерфейс командной строки Kubernetes][kubernetes-cli] является обязательным для управления общих учетных данных из реестра контейнеров. До Helm, который является диспетчер пакетов Kubernetes также требуется Kubernetes. |
| Helm CLI | Как часть [интерфейс командной строки Helm][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]. |
|Ресурс речи |Для использования контейнеров необходимо следующее:<br><br>Объект _речи_ ресурсов Azure, чтобы получить связанный ключ выставления счетов и выставления счетов URI конечной точки. Оба значения доступны на портале Azure **речи** страницы Обзор и ключи на них, необходимые для запуска контейнера.<br><br>**{API_KEY}** : ключ ресурса<br><br>**{ENDPOINT_URI}** : пример URI конечной точки: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Рекомендуемый узел Конфигурация компьютера

Ссылаться на [главным компьютером для контейнеров службы распознавания речи][speech-container-host-computer] сведения как ссылка. Это *чарта helm* автоматически вычисляет требования к ЦП и памяти, исходя из количества декодирует (одновременных запросов), заданный пользователем. Кроме того, он будет зависеть от ли оптимизации для аудио/текстовое поле, настраиваются как `enabled`. Значения по умолчанию диаграмма helm для двух параллельных запросов и отключение оптимизации.

| Service | ЦП / контейнера | Память / контейнера |
|--|--|--|
| **Преобразование речи в текст** | один декодер требуется как минимум 1,150 миллиардах. Если `optimizedForAudioFile` включен, то 1,950 миллиардах являются обязательными. (по умолчанию: два декодеров) | Обязательно: 2 ГБ<br>Ограничен:  4 ГБ |
| **Преобразование текста в речь** | один одновременных запросов требуется как минимум 500 миллиардах. Если `optimizeForTurboMode` включен, то 1000 миллиардах являются обязательными. (по умолчанию: два одновременных запросов) | Обязательно: 1 GB<br> Ограничен: 2 ГБ |

## <a name="connect-to-the-kubernetes-cluster"></a>Подключитесь к кластеру Kubernetes

Главный компьютер должны быть доступного кластера Kubernetes. Это руководство см. в [развертывание кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) концептуальной лучше понять, как развернуть кластер Kubernetes к главному компьютеру.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Совместное использование учетных данных Docker с кластером Kubernetes

Чтобы разрешить кластеру Kubernetes `docker pull` настроенные образы из `containerpreview.azurecr.io` реестр контейнеров, которые необходимо передать учетные данные docker в кластер. Выполнение [ `kubectl create` ][kubectl-create] следующую команду, чтобы создать *секрет docker-registry* на основе учетных данных, предоставляются из требования доступа реестра контейнеров.

По выбору, выполните следующую команду в интерфейсе командной строки. Не забудьте заменить `<username>`, `<password>`, и `<email-address>` с учетными данными реестра контейнеров.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже есть доступ к `containerpreview.azurecr.io` реестр контейнеров, можно создать секрет Kubernetes, вместо этого использовать универсальный флаг. Рассмотрим следующую команду, выполняемую в JSON-ФАЙЛ конфигурации Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Следующий результат выводится в консоль после успешного создания секрета.

```console
secret "containerpreview" created
```

Чтобы убедиться, что был создан секрет, выполните [ `kubectl get` ][kubectl-get] с `secrets` флаг.

```console
kuberctl get secrets
```

Выполнение `kubectl get secrets` выводит все настроенные секреты.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы Helm для развертывания

Посетите [центра Microsoft Helm][ms-helm-hub] для всех общедоступных helm диаграмм, предлагаемых корпорацией Майкрософт. В центре Microsoft Helm, вы найдете **диаграммы в локальной речи Cognitive Services**. **Cognitive Services речи On-Premises** является диаграммы будет установлен, но необходимо сначала создать `config-values.yaml` файл с явные конфигурации. Начнем с добавления репозиторий Майкрософт в экземпляр Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Затем мы настроим наших значений диаграммы Helm. Скопируйте и вставьте следующий код YAML в файл с именем `config-values.yaml`. Дополнительные сведения о настройке **Чарта Helm речи локальной Cognitive Services**, см. в разделе [Настройка диаграмм helm](#customize-helm-charts). Замените `billing` и `apikey` значения собственными.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Если `billing` и `apikey` значения не указаны, службы будут действительны в течение 15 минут. Аналогичным образом проверка завершится ошибкой, так как службы не будут доступны.

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (диаграмма Helm)

*Чарта Helm* содержит конфигурацию, какие образы docker для извлечения из `containerpreview.azurecr.io` реестра контейнеров.

> Объект [чарта Helm][helm-charts] — это коллекция файлов, которые описывают связанным набором ресурсов Kubernetes. Одной диаграмме может использоваться для развертывания простой, как memcached pod, или что-нибудь сложного, такие как стек полных веб приложения с серверов HTTP, баз данных, кэшей и т. д.

Предоставленный *диаграмм Helm* получить образы docker службы распознавания речи, преобразования текста в речь и службы распознавания речи в текст из `containerpreview.azurecr.io` реестра контейнеров.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установить диаграмму Helm в кластере Kubernetes

Чтобы установить *чарта helm* нам нужно будет выполнить [ `helm install` ][helm-install-cmd] команду, заменив `<config-values.yaml>` аргументом соответствующий путь к файлу и имя. `microsoft/cognitive-services-speech-onpremise` Чарта Helm, упоминаемой ниже можно найти в [Helm центра Microsoft][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Ниже приведен пример выходных данных, которые могут ожидать от выполнения успешной установки.

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

Развертывание Kubernetes может занять через несколько минут. Чтобы убедиться, что модули и службы являются правильно развернуто и доступно, выполните следующую команду:

```console
kubectl get all
```

Следует ожидать увидеть примерно следующие выходные данные:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Проверка развертывания Helm с помощью Helm тестов

Определение установленных диаграмм Helm *команду Helm тесты*, используемые в качестве удобный способ для проверки. Эти тесты проверки готовности службы. Проверка двух **речи в текст** и **преобразования текста в речь** служб, будет выполняться [Helm теста][helm-test] команды.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Эти тесты будут завершаться неудачей, если не находится в состоянии POD `Running` или если развертывание не указана в списке `AVAILABLE` столбца. Сохраняйте Терпение, так как это может занять более десяти минут до завершения.

Эти тесты будут результаты различных состояния:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Вместо выполнения *helm тесты*, мог собирать данные *внешний IP-адрес* адреса и соответствующие порты из `kubectl get all` команды. С помощью IP-адрес и порт, откройте веб-браузер и перейдите к `http://<external-ip>:<port>:/swagger/index.html` представление страницы swagger API.

## <a name="customize-helm-charts"></a>Настройка диаграмм Helm

Чарты helm, являются иерархическими. Иерархические благосостояние допускает наследование диаграммы, она также предусматривает понятие, которое определяет, насколько подробны, где параметры, которые относятся более переопределять наследуемые правила.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об установке приложений с помощью Helm в Azure Kubernetes Service (AKS) [веб-странице][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Контейнеры Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
