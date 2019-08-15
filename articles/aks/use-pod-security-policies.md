---
title: Использование политик безопасности Pod в службе Kubernetes Azure (AKS)
description: Узнайте, как управлять допуском Pod с помощью Подсекуритиполици в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: 374e86409be08f1f9859b3e325dda57080b89dbf
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033997"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Предварительная версия — защита кластера с помощью политик безопасности Pod в службе Kubernetes Azure (AKS)

Чтобы повысить безопасность кластера AKS, можно ограничить набор модулей, которые можно запланировать. Модули, которые запрашивают неразрешенные ресурсы, не могут выполняться в кластере AKS. Этот доступ определяется с помощью политик безопасности Pod. В этой статье показано, как использовать политики безопасности Pod для ограничения развертывания модулей AKS.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Требуется Azure CLI версии 2.0.61 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования политик безопасности Pod требуется расширение CLI *AKS-Preview* версии 0.4.1 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Регистрация поставщика функций политики безопасности Pod

Чтобы создать или обновить кластер AKS для использования политик безопасности Pod, сначала включите в подписке флаг функции. Чтобы зарегистрировать флаг функции *подсекуритиполиципревиев* , используйте команду [AZ Feature Register][az-feature-register] , как показано в следующем примере:

> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать значения по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Проверить состояние регистрации можно с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Общие сведения о политиках безопасности Pod

В кластере Kubernetes контроллер допуска используется для перехвата запросов к серверу API при создании ресурса. Контроллер допуска может затем *проверить* запрос ресурса на соответствие набору правил или изменить его на изменение параметров развертывания.

*Подсекуритиполици* — это контроллер допуска, который проверяет спецификацию Pod на соответствие определенным требованиям. Эти требования могут ограничивать использование привилегированных контейнеров, доступ к определенным типам хранилища, а также пользователя или группу, от имени которых может работать контейнер. При попытке развернуть ресурс, в котором спецификации Pod не соответствуют требованиям, изложенным в политике безопасности Pod, запрос отклоняется. Эта возможность контролировать, какие модули управления доступом к данным могут быть запланированы в кластере AKS, предотвращая некоторые возможные уязвимости или эскалацию привилегий.

При включении политики безопасности Pod в кластере AKS применяются некоторые политики по умолчанию. Эти политики по умолчанию предоставляют готовый интерфейс для определения модулей, которые можно запланировать. Однако пользователи кластера могут столкнуться с проблемами при развертывании модулей Pod, пока не будут определены собственные политики. Рекомендуемый подход:

* Создание кластера AKS
* Определение собственных политик безопасности Pod
* Включение функции политики безопасности Pod

Чтобы продемонстрировать, как политики по умолчанию ограничивают развертывания Pod, в этой статье мы сначала включили функции политик безопасности Pod, а затем создадим настраиваемую политику.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Включение политики безопасности Pod в кластере AKS

Включить или отключить политику безопасности Pod можно с помощью команды [AZ AKS Update][az-aks-update] . В следующем примере включается политика безопасности Pod для имени кластера *myAKSCluster* в группе ресурсов с именем *myResourceGroup*.

> [!NOTE]
> Для реального использования не включайте политику безопасности Pod, пока не будут определены собственные пользовательские политики. В этой статье вы включите политику безопасности Pod в качестве первого шага, чтобы увидеть, как политики по умолчанию ограничивают развертывания Pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Политики AKS по умолчанию

При включении политики безопасности Pod AKS создает две политики по умолчанию с именами privileged и Restricted. Не изменяйте и не удаляйте эти политики по умолчанию. Вместо этого создайте собственные политики, определяющие параметры, которые требуется контролировать. Давайте сначала посмотрим, что эти политики по умолчанию влияют на развертывание Pod.

Чтобы просмотреть доступные политики, используйте команду [kubectl Get PSP][kubectl-get] , как показано в следующем примере. В рамках политики *ограничения* по умолчанию пользователю запрещено использовать *Priv* для укрупнения привилегированных модулей, и пользователь *муструнаснонрут*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Ограниченная* политика безопасности Pod применяется ко всем пользователям, прошедшим проверку подлинности в кластере AKS. Это назначение управляется Клустерролес и Клустерролебиндингс. Используйте команду [kubectl Get клустерролебиндингс][kubectl-get] и выполните поиск по умолчанию: Restricted *:* Binding:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Как показано в следующем сжатом выводе, параметр *PSP:* Restricted клустерроле назначается любой *системе: прошедшим проверку* подлинности пользователям. Эта возможность обеспечивает базовый уровень ограничений без определения собственных политик.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Важно понимать, как эти политики по умолчанию взаимодействуют с запросами пользователей на планирование модулей Pod, прежде чем приступать к созданию собственных политик безопасности. В следующих разделах мы планируем несколько модулей, чтобы увидеть эти политики по умолчанию в действии.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Создание тестового пользователя в кластере AKS

По умолчанию при использовании команды [AZ AKS Get-Credential][az-aks-get-credentials] учетные данные *администратора* кластера AKS и `kubectl` добавляются в конфигурацию. Пользователь с правами администратора обходит принудительное применение политик безопасности Pod. При использовании интеграции Azure Active Directory для кластеров AKS можно выполнить вход с использованием учетных данных пользователя без прав администратора, чтобы увидеть применение политик в действии. В этой статье мы создадим тестовую учетную запись пользователя в кластере AKS, который можно использовать.

Создайте пример пространства имен с именем *PSP-AKS* для тестовых ресурсов с помощью команды [kubectl Create Namespace][kubectl-create] . Затем создайте учетную запись службы с именем *"неадминистративный пользователь* " с помощью команды [kubectl Create учетная запись службы][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Затем создайте Ролебиндинг для *пользователя* , который не является администратором, чтобы выполнять базовые действия в пространстве имен с помощью команды [kubectl Create ролебиндинг][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Создание псевдонимов для администратора и пользователя без прав администратора

Чтобы выделить разницу между обычным пользователем администратора при использовании `kubectl` и пользователь, не являющийся администратором, созданный на предыдущих шагах, создайте два псевдонима командной строки:

* Псевдоним **kubectl-Admin** предназначен для обычного пользователя администратора и ограничивается пространством имен *PSP-AKS* .
* Псевдоним **kubectl-нонадминусер** предназначен для *пользователя* , который не является администратором, созданным на предыдущем шаге, и ограничивается пространством имен *PSP-AKS* .

Создайте эти два псевдонима, как показано в следующих командах:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Тестирование создания привилегированного Pod

Давайте сначала проверим, что происходит при планировании Pod с помощью контекста `privileged: true`безопасности. Этот контекст безопасности повышает привилегии Pod. В предыдущем разделе, в котором были показаны политики безопасности AKS Pod по умолчанию, политика *ограничения* должна отклонить этот запрос.

Создайте файл с именем `nginx-privileged.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Не удалось запланировать модуль Pod, как показано в следующем примере выходных данных:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Модуль не достигает этапа планирования, поэтому нет ресурсов для удаления перед тем, как вы перейдете.

## <a name="test-creation-of-an-unprivileged-pod"></a>Тестирование создания непривилегированного Pod

В предыдущем примере спецификация Pod запросила привилегированное укрупнение. Этот запрос отклонен политикой безопасности Pod, заданной по умолчанию, поэтому не удалось запланировать модуль Pod. Давайте попробуем запустить этот же модуль NGINX без запроса на эскалацию привилегий.

Создайте файл с именем `nginx-unprivileged.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Планировщик Kubernetes принимает запрос Pod. Однако если взглянуть на состояние Pod с помощью `kubectl get pods`, возникает ошибка:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Используйте команду [kubectl для описания Pod][kubectl-describe] , чтобы просмотреть события для Pod. В следующем сокращенном примере показано, что контейнер и образ должны иметь разрешения root, даже если мы не запрашивали их:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Несмотря на то, что мы не запрашивали привилегированный доступ, образ контейнера для NGINX должен создать привязку для порта *80*. Для привязки портов *1024* и ниже требуется *корневой* пользователь. При попытке запуска Pod политика безопасности Pod запрещает этот запрос.

В этом примере показано, что действуют политики безопасности Pod по умолчанию, созданные AKS, и ограничивает действия, которые может выполнять пользователь. Важно понимать поведение этих политик по умолчанию, так как вы, возможно, не планируете отклонять базовый модуль NGINX.

Прежде чем перейти к следующему шагу, удалите этот тест Pod с помощью команды [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Тестирование создания Pod с конкретным контекстом пользователя

В предыдущем примере образ контейнера автоматически пытался использовать root для привязки NGINX к порту 80. Этот запрос был отклонен политикой безопасности Pod с ограничением по умолчанию, поэтому не удается запустить модуль Pod. Давайте попробуем запустить тот же модуль NGINX с конкретным контекстом пользователя, например `runAsUser: 2000`.

Создайте файл с именем `nginx-unprivileged-nonroot.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Создайте модуль Pod с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Планировщик Kubernetes принимает запрос Pod. Однако если взглянуть на состояние Pod с помощью `kubectl get pods`, то возникает другая ошибка, чем в предыдущем примере:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Используйте команду [kubectl для описания Pod][kubectl-describe] , чтобы просмотреть события для Pod. В следующем сжатом примере показаны события Pod:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

События указывают на то, что контейнер был создан и запущен. Ничего очевидно от того, почему модуль Pod находится в неисправном состоянии. Рассмотрим журналы Pod с помощью команды [kubectl logs][kubectl-logs] :

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Следующий пример выходных данных журнала дает указание о том, что в самой конфигурации NGINX есть ошибка разрешений при попытке запуска службы. Эта ошибка снова вызвана тем, что необходимо выполнить привязку к порту 80. Несмотря на то, что спецификация Pod определила обычную учетную запись пользователя, эта учетная запись пользователя недостаточно на уровне операционной системы для запуска службы NGINX и привязки к ограниченному порту.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Опять же, важно понимать поведение политик безопасности Pod по умолчанию. Эта ошибка немного сложнее проконтролировать, и, возможно, вы не ожидали, что базовый модуль NGINX не будет отклонен.

Прежде чем перейти к следующему шагу, удалите этот тест Pod с помощью команды [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Создание пользовательской политики безопасности Pod

Теперь, когда вы узнали о поведении политик безопасности Pod по умолчанию, давайте разберем, что *пользователь* , не являющийся администратором, может успешно запланировать множество модулей.

Давайте создадим политику для отклонения модулей Pod, запрашивающих привилегированный доступ. Другие параметры, такие как выполняющиеся или разрешенные *тома*, явно не ограничиваются. Этот тип политики запрещает запрос привилегированного доступа, но в противном случае позволяет кластеру запускать запрошенные модули Pod.

Создайте файл с именем `psp-deny-privileged.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Создайте политику с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Чтобы просмотреть доступные политики, используйте команду [kubectl Get PSP][kubectl-get] , как показано в следующем примере. Сравните политику *PSP-Deny-Privileges* с политикой *ограничения* по умолчанию, которая была применена в предыдущих примерах для создания Pod. Только использование эскалации *Priv* запрещено политикой. Для политики *PSP-Deny-Privileges* не существует ограничений на пользователя или группу.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Разрешить учетной записи пользователя использовать пользовательскую политику безопасности Pod

На предыдущем шаге вы создали политику безопасности Pod для отклонения модулей, запрашивающих привилегированный доступ. Чтобы разрешить использование политики, создайте *роль* или *клустерроле*. Затем вы связываете одну из этих ролей с помощью *ролебиндинг* или *клустерролебиндинг*.

В этом примере создайте Клустерроле, который позволит *использовать* политику *PSP-Deny-privileged* , созданную на предыдущем шаге. Создайте файл с именем `psp-deny-privileged-clusterrole.yaml` и вставьте следующий манифест YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Создайте Клустерроле с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Теперь создайте Клустерролебиндинг для использования Клустерроле, созданного на предыдущем шаге. Создайте файл с именем `psp-deny-privileged-clusterrolebinding.yaml` и вставьте следующий манифест YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Создайте Клустерролебиндинг с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> На первом шаге этой статьи функция политики безопасности Pod была включена в кластере AKS. Рекомендуется включить функцию политики безопасности Pod только после определения собственных политик. На этом этапе можно включить функцию политики безопасности Pod. Была определена одна или несколько пользовательских политик, а учетные записи пользователей были связаны с этими политиками. Теперь вы можете безопасно применять политику безопасности Pod и сокращать проблемы, вызванные политиками по умолчанию.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Повторное тестирование создания непривилегированного модуля Pod

Применяя пользовательскую политику безопасности Pod и привязку учетной записи пользователя для использования политики, давайте попробуем снова создать непривилегированный модуль. Используйте тот же `nginx-privileged.yaml` манифест для создания Pod с помощью команды [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Модуль POD успешно запланирован. При проверке состояния Pod с помощью команды [kubectl Get][kubectl-get] Pod *выполняется*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

В этом примере показано, как создать пользовательские политики безопасности Pod для определения доступа к кластеру AKS для разных пользователей или групп. Политики AKS по умолчанию предоставляют ограниченный контроль над тем, какие модули Pod могут выполняться, поэтому создайте собственные пользовательские политики, чтобы правильно определить необходимые ограничения.

Удалите непривилегированный модуль NGINX с помощью команды [kubectl Delete][kubectl-delete] и укажите имя манифеста YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы отключить политику безопасности Pod, выполните команду [AZ AKS Update][az-aks-update] еще раз. В следующем примере выполняется отключение политики безопасности Pod для имени кластера *myAKSCluster* в группе ресурсов с именем *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Затем удалите Клустерроле и Клустерролебиндинг:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Удалите политику сети с помощью команды [kubectl Delete][kubectl-delete] и укажите имя манифеста YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Наконец, удалите пространство имен *PSP-AKS* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Следующие шаги

В этой статье показано, как создать политику безопасности Pod, чтобы предотвратить использование привилегированного доступа. Существует множество функций, которые может применять политика, например тип тома или пользователя запуска от имени. Дополнительные сведения о доступных параметрах см. в справочнике по [политикам безопасности Kubernetes Pod][kubernetes-policy-reference].

Дополнительные сведения об ограничении сетевого трафика Pod см. [в статье Защита трафика между модулями данных с помощью сетевых политик в AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
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
