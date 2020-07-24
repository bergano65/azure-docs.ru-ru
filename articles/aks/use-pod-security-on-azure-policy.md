---
title: Защита модулей Pod с помощью политики Azure в службе Kubernetes Azure (AKS)
description: Сведения о защите модулей Pod с помощью политики Azure в службе Kubernetes Azure (AKS)
services: container-service
ms.topic: article
ms.date: 07/06/2020
author: jluk
ms.openlocfilehash: 8be0b05c260037bbe8afc92726d81668e1391d4a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050471"
---
# <a name="secure-pods-with-azure-policy-preview"></a>Защита модулей Pod с помощью политики Azure (Предварительная версия)

Чтобы повысить уровень безопасности кластера AKS, вы можете контролировать предоставляемые модули Pod и, если что-либо работает с политикой компании. Этот доступ определяется с помощью встроенных политик, предоставляемых [надстройкой политики Azure для AKS][kubernetes-policy-reference]. Обеспечивая дополнительный контроль над аспектами безопасности спецификации Pod, например привилегированными привилегиями, обеспечивает более строгое соответствие безопасности и видимость того, что развертывается в кластере. Если модуль Pod не соответствует условиям, указанным в политике, политика Azure может запретить запуск или пометить нарушение. В этой статье показано, как использовать политику Azure для ограничения развертывания модулей Pod в AKS.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Установка надстройки политики Azure для AKS

Для защиты модулей AKS с помощью политики Azure необходимо установить надстройку политики Azure для AKS в кластере AKS. Выполните следующие [действия, чтобы установить надстройку политики Azure](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

В этом документе предполагается, что вы развернули следующие ссылки в пошаговом руководстве.

* Зарегистрированные `Microsoft.ContainerService` `Microsoft.PolicyInsights` поставщики ресурсов и с помощью`az provider register`
* `AKS-AzurePolicyAutoApprove`Флаг предварительной версии зарегистрирован с помощью`az feature register`
* Azure CLI установлен с `aks-preview` расширением 0.4.53 или более поздней версии
* Кластер AKS на поддерживаемой версии 1,15 или более поздней, с надстройкой политики Azure

## <a name="overview-of-securing-pods-with-azure-policy-for-aks-preview"></a>Общие сведения о защите модулей Pod с помощью политики Azure для AKS (Предварительная версия)

>[!NOTE]
> В этом документе подробно описано, как использовать политику Azure для защиты модулей Pod, которая является преемником [функции политики безопасности модуля Kubernetes в предварительной версии](use-pod-security-policies.md).
> **Политика безопасности Pod (Предварительная версия) и надстройка политики Azure для AKS нельзя включить одновременно.**
> 
> Если вы устанавливаете надстройку политики Azure в кластер с включенной политикой безопасности Pod, [выполните следующие действия, чтобы отключить политику безопасности Pod](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

В кластере AKS контроллер допуска используется для перехвата запросов к серверу API при создании и обновлении ресурса. Контроллер допуска может *проверить* запрос ресурса на соответствие набору правил, следует ли создавать его.

Ранее [Политика безопасности Pod Feature (Предварительная версия)](use-pod-security-policies.md) была включена в проекте Kubernetes, чтобы ограничить возможности развертывания модулей. Эта функция больше не находится в активной разработке из проекта Kubernetes.

С помощью надстройки "политика Azure" кластер AKS может использовать встроенные политики Azure, которые защищают модули Pod и другие ресурсы Kubernetes, аналогичные политикам безопасности модулей. Надстройка политики Azure для AKS устанавливает управляемый экземпляр [привратника](https://github.com/open-policy-agent/gatekeeper), контроллер допуска проверки. Политика Azure для Kubernetes основана на агенте открытой политики с открытым исходным кодом, который основывается на [языке политики Рего](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

В этом документе подробно описано, как использовать политику Azure для защиты модулей Pod в кластере AKS и как выполнить миграцию из политик безопасности модуля (Предварительная версия).

## <a name="limitations"></a>Ограничения

* На этапе предварительной версии ограничение 200 Pod с 20 политиками Azure для политик Kubernetes может выполняться в одном кластере.
* [Некоторые пространства имен System](#namespace-exclusion) , содержащие управляемые модули AKS, исключаются из оценки политики.
* Windows Pods [не поддерживает контексты безопасности](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods), поэтому многие политики Azure применимы только к ОС Linux, например, запрещают привилегии root, которые не могут быть расширены в Windows Pod.
* Невозможно одновременно включить политику безопасности Pod и надстройку политики Azure для AKS. Если вы устанавливаете надстройку политики Azure в кластер с включенной политикой безопасности Pod, отключите политику безопасности Pod, [выполнив следующие инструкции](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Политики Azure для защиты модулей Kubernetes

После установки надстройки политики Azure никакие политики по умолчанию не применяются.

Существует одиннадцать (11) встроенных политик Azure и две (2) встроенные инициативы, которые специально защищают модули Pod в кластере AKS.
Каждая политика может быть настроена с помощью действия. Ниже приведен полный список [политик AKS и их поддерживаемых эффектов][policy-samples]. Узнайте больше о [влиянии политики Azure](../governance/policy/concepts/effects.md).

Политики Azure можно применять на уровне группы управления, подписки или группы ресурсов. При назначении политики на уровне группы ресурсов убедитесь, что выбрана группа ресурсов целевого кластера AKS в области действия политики. Каждый кластер в назначенной области с установленной надстройкой политики Azure находится в области действия политики.

Если вы используете [политику безопасности Pod (Предварительная версия)](use-pod-security-policies.md), Узнайте, как [Перейти к политике Azure и о других различиях в поведении](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Встроенные инициативы политики

Инициатива в политике Azure — это набор определений политик, адаптированных к достижению одноименной цели. Использование инициатив может упростить управление и назначение политик в кластерах AKS. Инициатива существует как один объект, Узнайте больше о [инициативах политики Azure](../governance/policy/overview.md#initiative-definition).

Политика Azure для Kubernetes предлагает две встроенные инициативы, которые защищают модули Pod, [базовые показатели](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) и [ограничены](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Обе встроенные инициативы построены на основе определений, используемых в [политике безопасности Pod из Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Управление политикой безопасности Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Ссылка на определение политики Azure| [Базовая инициатива](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Ограниченная инициатива](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Запретить запуск привилегированных контейнеров|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Да | Да
|Запретить совместное использование пространств имен узла|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Да | Да
|Ограничение использования сети и портов узла|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Да | Да
|Ограничение использования файловой системы узла|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Да | Да
|Ограничение возможностей Linux до [набора по умолчанию](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Да | Да
|Ограничение использования определенных типов томов|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Да — разрешенные типы томов: `configMap` ,, `emptyDir` `projected` , `downwardAPI` ,`persistentVolumeClaim`|
|Укрупнение привилегий до корня|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Да |
|Ограничение идентификаторов пользователей и групп контейнера|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Да|
|Ограничить выделение Фсграуп, владеющего томами Pod|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Да — допустимые правила: `runAsUser: mustRunAsNonRoot` , `supplementalGroup: mustRunAs 1:65536` , `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Требуется профиль секкомп|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Да, Алловедпрофилес * `docker/default` или`runtime/default` |

\*DOCKER или Default не рекомендуется использовать в Kubernetes, начиная с версии 1.11

### <a name="additional-optional-policies"></a>Дополнительные необязательные политики

Существуют дополнительные политики Azure, которые можно применять отдельно, за исключением применения инициативы. Рассмотрите возможность добавления этих политик в дополнение к инициативам, если ваши требования не выполняются встроенными инициативами.

|[Управление политикой безопасности Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Ссылка на определение политики Azure| Применение в дополнение к инициативе базовых показателей | Применение в дополнение к ограниченной инициативе |
|---|---|---|---|
|Определение профиля AppArmor, используемого контейнерами|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Необязательно | Необязательно |
|Разрешить подключения, которые не предназначены только для чтения|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Необязательно | Необязательно |
|Ограничить конкретные драйверы Флексволуме|[Общедоступное облако](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Необязательно — используйте, если требуется ограничить только драйверы Флексволуме, но другие не задаются с помощью параметра "ограничить использование определенных типов томов". | Неприменимо. Ограниченная инициатива включает "ограничение использования определенных типов томов", которая запрещает все драйверы Флексволуме. |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Неподдерживаемые встроенные политики для управляемых кластеров AKS

> [!NOTE]
> Следующие 3 политики **не поддерживаются в AKS** из-за настройки аспектов, управляемых и ЗАЩИЩЕНных AKS в качестве управляемой службы. Эти политики созданы специально для подключенных к службе "дуги" кластеров Azure с неуправляемыми плоскостями управления.

|[Управление политикой безопасности Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Определение пользовательского контекста SELinux для контейнера|
|Ограничение профиля сисктл, используемого контейнерами|
|Типы подключения процедур по умолчанию определяются для сокращения уязвимой зоны.|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Исключение пространства имен

> [!WARNING]
> Для поддержания работоспособности кластера в пространствах имен администраторов, таких как KUBE-System, необходимо выполнить удаление необходимого пространства имен из списка исключенных пространств имен по умолчанию из-за требуемого системного Pod.

AKS требует, чтобы системные модули запускались в кластере для обеспечения важных служб, таких как разрешение DNS. Политики, ограничивающие функциональность Pod, могут повлиять на стабильность системной Pod. Таким образом, следующие пространства имен **исключаются из оценки политики во время запросов на допускы во время создания, обновления и аудита политики**. Это приведет к тому, что новые развертывания для этих пространств имен будут исключены из политик Azure.

1. KUBE — система
1. Привратник — система
1. Azure — дуга
1. AKS — Periscope

Дополнительные пользовательские пространства имен можно исключить из оценки во время создания, обновления и аудита. Это следует использовать при наличии специализированных модулей Pod, которые выполняются в санкционированном пространстве имен и хотят избежать срабатывания нарушений аудита.

## <a name="apply-the-baseline-initiative"></a>Применение базовой инициативы

> [!TIP]
> Все политики по умолчанию имеют результат аудита. В любое время в политике Azure эффекты можно обновлять и отклонять.

Чтобы применить базовую инициативу, можно назначить с помощью портал Azure.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Перейдите по [ссылке на портал Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , чтобы ознакомиться с инициативой по базовому плану безопасности Pod
1. Задайте **область действия** уровня подписки или только группу ресурсов с AKS кластерами с включенной надстройкой политики Azure.
1. Выберите страницу **Параметры** и обновите **результат действия** с `audit` на, `deny` чтобы запретить новые развертывания, нарушающие базовую инициативу.
1. Добавление дополнительных пространств имен для исключения из оценки во время создания, обновления и аудита [. Некоторые пространства имен принудительно исключаются из оценки политики.](#namespace-exclusion) 
 ![ обновить результат](media/use-pod-security-on-azure-policy/update-effect.png)
1. Выберите **проверить и создать** , чтобы отправить политики.

Подтвердите, что политики применяются к кластеру, выполнив `kubectl get constrainttemplates` .

> [!NOTE]
> Синхронизация политик может занять до [20 минут](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) в каждом кластере.

Результат должен выглядеть следующим образом:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Проверка отклонения привилегированного Pod

Давайте сначала проверим, что происходит при планировании Pod с помощью контекста безопасности `privileged: true` . Этот контекст безопасности повышает привилегии Pod. Концепция базовых модулей запрещает привилегированные модули, поэтому запрос отклоняется, что приведет к отклонению развертывания.

Создайте файл с именем `nginx-privileged.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
      securityContext:
        privileged: true
```

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Как ожидалось, модуль не может быть запланирован, как показано в следующем примере выходных данных:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Модуль не достигает этапа планирования, поэтому нет ресурсов для удаления перед тем, как вы перейдете.

## <a name="test-creation-of-an-unprivileged-pod"></a>Тестирование создания непривилегированного Pod

В предыдущем примере образ контейнера автоматически пытался использовать root для привязки NGINX к порту 80. Этот запрос был отклонен инициативой базовой политики, поэтому невозможно запустить Pod. Давайте попробуем запустить этот же модуль NGINX без привилегированного доступа.

Создайте файл с именем `nginx-unprivileged.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Модуль POD успешно запланирован. При проверке состояния Pod с помощью команды [kubectl Get][kubectl-get] Pod *выполняется*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

В этом примере показано, как базовая инициатива влияет только на развертывания, которые нарушают политики в коллекции. Разрешенные развертывания продолжают функционировать.

Удалите непривилегированный модуль NGINX с помощью команды [kubectl Delete][kubectl-delete] и укажите имя манифеста YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Отключите политики и надстройку политики Azure

Чтобы удалить базовую инициативу, выполните следующие действия.

1. Перейдите в область политики на портал Azure
1. Выбор **назначений** в левой области
1. Нажмите кнопку "..." кнопка рядом с базовым профилем
1. Выберите "удалить назначение".

![Удалить назначение](media/use-pod-security-on-azure-policy/delete-assignment.png)

Чтобы отключить надстройку политики Azure, используйте команду [AZ AKS Disable-надстроек][az-aks-disable-addons] .

```azure-cli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Узнайте, как удалить [надстройку политики Azure из портал Azure](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Миграция из политики безопасности Kubernetes Pod в политику Azure

Чтобы выполнить миграцию из политики безопасности Pod, необходимо выполнить следующие действия в кластере.

1. [Отключение политики безопасности Pod](use-pod-security-policies.md#clean-up-resources) в кластере
1. Включение [надстройки политики Azure][kubernetes-policy-reference]
1. Включение требуемых политик Azure из [доступных встроенных политик][policy-samples]

Ниже приведена сводка изменений в поведении политики безопасности Pod и политики Azure.

|Сценарий| Политика безопасности Pod | Политика Azure |
|---|---|---|
|Установка|Включить компонент политики безопасности Pod |Включить надстройку политики Azure
|Развертывание политик| Развертывание ресурса политики безопасности Pod| Назначьте политики Azure области подписки или группы ресурсов. Для приложений ресурсов Kubernetes требуется надстройка политики Azure.
| Политики по умолчанию | Если политика безопасности Pod включена в AKS, применяются привилегированные и неограниченные политики по умолчанию. | Политики по умолчанию не применяются при включении надстройки политики Azure. Необходимо явно включить политики в политике Azure.
| Кто может создавать и назначать политики | Администратор кластера создает ресурс политики безопасности Pod | Пользователи должны иметь минимальную роль "владелец" или "участник политики ресурсов" в группе ресурсов кластера AKS. С помощью API пользователи могут назначать политики в области ресурсов кластера AKS. У пользователя должно быть как минимум разрешение "владелец" или "участник политики ресурсов" в ресурсе кластера AKS. — На портале Azure политики можно назначать на уровне группы управления, подписки или группы ресурсов.
| Политики авторизации| Пользователям и учетным записям служб требуются явные разрешения на использование политик безопасности Pod. | Для авторизации политик дополнительное назначение не требуется. После назначения политик в Azure все пользователи кластера могут использовать эти политики.
| Применимость политик | Пользователь с правами администратора обходит принудительное применение политик безопасности Pod. | Все пользователи (администратор & без прав администратора) видят одни и те же политики. Специальные регистры на основе пользователей не предусмотрены. Приложение политики можно исключить на уровне пространства имен.
| Область политики | Политики безопасности Pod не являются пространствами имен | Шаблоны ограничений, используемые политикой Azure, не являются пространствами имен.
| Действие "запретить/аудит/изменения" | Политики безопасности Pod поддерживают только запрещенные действия. Изменение может выполняться со значениями по умолчанию для запросов на создание. Проверку можно выполнить во время запросов на обновление.| Политика Azure поддерживает как аудит, так & запрещать действия. Изменения пока не поддерживаются, но были запланированы.
| Соответствие политике безопасности Pod | Не существует сведений о соответствии модулей Pod, существовавших до включения политики безопасности модуля. Несоответствующие Pod, созданные после включения политик безопасности Pod, отклоняются. | Несоответствующие модули, существовавшие до применения политик Azure, будут отображаться в нарушениях политики. Несоответствующие модули Pod, созданные после включения политик Azure, отклоняются, если для политик задано действие Deny.
| Просмотр политик в кластере | `kubectl get psp` | `kubectl get constrainttemplate`— Возвращаются все политики.
| Политика безопасности Pod "Стандартный — привилегированный" | Ресурс политики безопасности Pod, созданный по умолчанию, создается при включении этой функции. | Привилегированный режим не подразумевает ограничений, поэтому он эквивалентен отсутствию назначения политики Azure.
| [Политика безопасности Pod "Стандартный" — "базовый"/"по умолчанию"](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Пользователь устанавливает базовый ресурс политики безопасности Pod. | Политика Azure предоставляет [встроенную базовую инициативу](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , которая сопоставляется с базовой политикой безопасности Pod.
| [Политика безопасности Pod — ограничена по стандарту](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Пользователь устанавливает ресурс с ограничением политики безопасности Pod. | Политика Azure предоставляет [встроенную ограниченную инициативу](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) , которая сопоставляется с ограниченной политикой безопасности Pod.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как применить политику Azure, которая запрещает развертывание привилегированных модулей Pod, чтобы предотвратить использование привилегированного доступа. Существует множество политик, которые можно применять, например, для ограничения использования томов. Дополнительные сведения о доступных параметрах см. в [справочнике по политикам Azure для Kubernetes][kubernetes-policy-reference].

Дополнительные сведения об ограничении сетевого трафика Pod см. [в статье Защита трафика между модулями данных с помощью сетевых политик в AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: policy-samples.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete