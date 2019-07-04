---
title: Использование политик безопасности pod в службе Azure Kubernetes (AKS)
description: Сведения об управлении приемной комиссии pod с помощью PodSecurityPolicy в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 9da722006651cfc9e9f2a175d5c330ba5df08123
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447068"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview — защита кластера с помощью политик безопасности pod в службе Azure Kubernetes (AKS)

Чтобы повысить безопасность кластера AKS, можно ограничить модулей, которые может быть запланировано. Модули, запрашивающих ресурсы, которые вы не разрешаете нельзя запускать в кластере AKS. Можно определить этот доступ, с помощью политик безопасности pod. В этой статье показано, как использовать политики безопасности pod ограниченного развертывания POD, содержащихся в AKS.

> [!IMPORTANT]
> Функции предварительной версии AKS: самообслуживания, согласиться. Они предназначены для сбора отзывов и ошибки нашего сообщества. В предварительной версии эти функции не предназначены для использования в рабочей среде. Функции в общедоступной предварительной версии подпадают под поддержки «оптимальных затрат». Помощь от групп разработчиков AKS Техническая поддержка доступна во время рабочих часов тихоокеанского часового пояса (по тихоокеанскому времени) только. Дополнительные сведения обратитесь в следующие справочные статьи:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, см. в этом кратком руководстве AKS [с помощью Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если требуется выполнить установку или обновление, см. в разделе [установить Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Чтобы использовать политики безопасности pod, необходимо сначала *предварительной версии aks* CLI версия расширения 0.4.1 или более поздней версии. Установка *предварительной версии aks* расширение Azure CLI с помощью [добавить расширения az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] команда::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Зарегистрировать поставщик функций политики безопасности pod

Чтобы создать или обновить кластер AKS с помощью политик безопасности pod, необходимо сначала включите флаг компонента по вашей подписке. Чтобы зарегистрировать *PodSecurityPolicyPreview* флаг, используйте [az функция register][az-feature-register] команды, как показано в следующем примере:

> [!CAUTION]
> При регистрации компонента для подписки, вы не можете сейчас отменить регистрацию этой функции. После включения некоторые возможности предварительной версии, можно использовать значения по умолчанию для всех кластеров AKS, затем создается в подписке. Не включайте функции предварительной версии на рабочие подписки. Используйте отдельную подписку для тестирования функции предварительной версии и собирайте отзывы.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Вы можете проверить состояние регистрации с помощью [список функций az][az-feature-list] команды:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновить регистрацию *Microsoft.ContainerService* поставщик ресурсов с помощью [az provider register][az-provider-register] команды:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Обзор политик безопасности pod

В кластере Kubernetes контроллеру допуском используется для перехвата запросов на сервер API, если ресурс не должен быть создан. Контроллер допуском можно затем *проверки* запрос ресурса на основе набора правил, или *изменяет* ресурсов, чтобы изменить параметры развертывания.

*PodSecurityPolicy* является контроллеру допуском проверки спецификации pod определенным требованиям. Эти требования могут ограничить использование привилегированных контейнеров, доступ к определенные типы хранилища, или пользователь или группа, которые контейнер может работать как. При попытке развернуть ресурс, где спецификации pod не соответствуют требованиям, приведенным в политике безопасности pod, запрос отклоняется. Эта возможность контроля модулей, которые может быть запланировано в AKS кластера предотвращает некоторые уязвимости безопасности или эскалации привилегий.

При включении политики безопасности pod в кластере AKS, применяются некоторые политики по умолчанию. Эти политики по умолчанию предоставляют интерфейс out of box, чтобы определить, что модулей можно запланировать. Тем не менее, кластера могут столкнуться пользователи проблемы при развертывании модулей POD, пока вы можете настраивать собственные политики. Рекомендуется использовать подход является:

* Создание кластера AKS
* Определение собственных политик безопасности pod
* Включение функции политики безопасности pod

Демонстрирует, как политики по умолчанию ограничить pod развертываний, в этой статье мы сначала включить функцию политики безопасности pod, а затем создайте настраиваемую политику.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Включить политику безопасности pod в кластере AKS

Можно включить или отключить политику безопасности pod с помощью [обновление az aks][az-aks-update] команды. В следующем примере включает pod политику безопасности на имя кластера *myAKSCluster* в группе ресурсов с именем *myResourceGroup*.

> [!NOTE]
> Для использования в реальных условиях не включить политику безопасности pod, пока не будет определено пользовательские политики. В этой статье вы включить политику безопасности pod на первом этапе, чтобы увидеть, как политики по умолчанию ограничить pod развертываний.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Политики по умолчанию для AKS

При включении политики безопасности pod AKS создает две политики по умолчанию с именем *привилегированных* и *ограниченных*. Не изменить или удалить эти политики по умолчанию. Вместо этого создайте собственные политики, которые определяют параметры, которые будут к элементу управления. Давайте первый взгляд на какие эти политики по умолчанию, их влияние на развертывание pod.

Чтобы просмотреть доступные политики, используйте [kubectl get psp][kubectl-get] команды, как показано в следующем примере. Как часть по умолчанию *ограниченных* политики, пользователю будет отказано *PRIV* для эскалации привилегированных pod и пользователь *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Ограниченных* pod безопасности политика применяется ко всем авторизованным пользователям в кластере AKS. Это назначение управляется ClusterRoles и ClusterRoleBindings. Используйте [kubectl get clusterrolebindings][kubectl-get] команды и выполните поиск *по умолчанию: ограниченных:* привязки:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Как показано в следующем сжатом примере выходных данных, *psp: ограниченных* ClusterRole является назначенных какой-либо *системы: проверку подлинности* пользователей. Эта возможность предоставляет базовый уровень ограничения не в собственных политиках определяемого.

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

Важно понимать, как взаимодействуют эти политики по умолчанию с запросы пользователей, чтобы запланировать модулей, прежде чем создавать собственный модуль политики безопасности. В следующих разделах давайте запланировать некоторых модулей и просмотреть эти политики по умолчанию в действии.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Создание тестового пользователя в кластере AKS

По умолчанию, при использовании [az aks get-credentials][az-aks-get-credentials] команды, *администратора* учетных данных в кластере AKS и добавлен к вашей `kubectl` конфигурации. Учетную запись администратора обход применяемых политик безопасности pod. При использовании интеграции Azure Active Directory для кластеров AKS удалось войти с учетными данными пользователя без прав администратора для принудительного применения политики в действии см. в разделе. В этой статье давайте создадим тестовую учетную запись пользователя в кластере AKS, который можно использовать.

Создание пространства имен пример с именем *psp aks* для ресурсов теста с помощью [kubectl создание пространства имен][kubectl-create] команды. Затем создайте учетную запись службы с именем *nonadmin пользователя* с помощью [kubectl создать учетная запись службы][kubectl-create] команды:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Создайте RoleBinding для *nonadmin пользователя* выполнять базовые действия в пространство имен при помощи [kubectl создать rolebinding][kubectl-create] команды:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Создание псевдонимов команд для пользователя admin и без прав администратора

Чтобы выделить разницу между регулярных администратора при использовании `kubectl` и пользователь без прав администратора, созданный на предыдущих шагах, создайте два псевдонима командной строки:

* **Kubectl-admin** псевдоним для регулярного администратора и ограничивается *psp aks* пространства имен.
* **Kubectl nonadminuser** псевдоним — для *nonadmin пользователя* создан на предыдущем шаге, а ограничена *psp aks* пространства имен.

Создайте эти два псевдонима, как показано в следующих командах:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Проверка создания привилегированных pod

Давайте сначала проверьте, что происходит во время запланированной pod с контекстом безопасности `privileged: true`. Этот контекст безопасности повышает привилегии pod. В предыдущем разделе, в котором pod AKS по умолчанию политики безопасности *ограниченных* политики следует Отклонить этот запрос.

Создайте файл с именем `nginx-privileged.yaml` и вставьте приведенный ниже YAML манифест:

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

Создайте pod с помощью [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Модуль не удается запланировать, как показано в следующем примере выходных данных:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Модуль не достигает стадии планирования, поэтому нет ресурсов, которые можно удалить, прежде чем продолжить работу.

## <a name="test-creation-of-an-unprivileged-pod"></a>Создание теста непривилегированной pod

В предыдущем примере в спецификации pod запрошенные привилегированных эскалации. Этот запрос запрещен по умолчанию *ограниченных* pod политику безопасности, поэтому не удается запланировать pod. Давайте попробуем теперь работает этот же модуля NGINX без запроса, эскалации привилегий.

Создайте файл с именем `nginx-unprivileged.yaml` и вставьте приведенный ниже YAML манифест:

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

Создайте pod с помощью [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Планировщик Kubernetes принимает запрос pod. Тем не менее если взглянуть на состояние pod с помощью `kubectl get pods`, ошибка:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Используйте [pod описания kubectl][kubectl-describe] команду, чтобы просмотреть события для pod. Следующем сокращенном примере показано, что контейнерам и образам необходимы разрешения корневой, несмотря на то, что мы не запрашивали их:

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

Несмотря на то, что мы не запрашивали любой привилегированный доступ, необходимо создать привязку для порта образ контейнера для NGINX *80*. Чтобы привязать порты *1024* и ниже, *корневой* пользователь необходим. При попытке запустить, pod *ограниченных* политика безопасности pod отклоняет этот запрос.

В этом примере показано, что по умолчанию политика безопасности pod, созданные AKS вступают в силу и ограничить действия, которые пользователь может выполнять. Важно понимать поведение этих политик по умолчанию, как можно не ожидать основные модуля NGINX запрещается.

Перед переходом к следующему шагу, удалите этот тест pod с помощью [kubectl удалить pod][kubectl-delete] команды:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Создание теста pod в контексте определенного пользователя

В предыдущем примере образ контейнера автоматически предпринята попытка использовать корневой для привязки NGINX на порт 80. Этот запрос был отклонен по умолчанию *ограниченных* модулю политики безопасности, поэтому не удается запустить модуль. Давайте попробуем теперь работает этот же модуля NGINX в контексте определенного пользователя, такие как `runAsUser: 2000`.

Создайте файл с именем `nginx-unprivileged-nonroot.yaml` и вставьте приведенный ниже YAML манифест:

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

Создайте pod с помощью [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Планировщик Kubernetes принимает запрос pod. Тем не менее если взглянуть на состояние pod с помощью `kubectl get pods`, есть другие ошибки, чем в предыдущем примере:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Используйте [pod описания kubectl][kubectl-describe] команду, чтобы просмотреть события для pod. В следующем сокращенном примере показано события pod:

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

События указывают, что контейнер был создан и запущен. Не сразу очевидно, для которой модуль находится в состоянии сбоя. Давайте взглянем на журналы модуля pod с помощью [kubectl журналы][kubectl-logs] команды:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

В следующем примере выходных журнала дает значение, указывающее, внутри самой конфигурации NGINX, возникает ошибка разрешений при попытке запуска службы. Эта ошибка возникает снова, которым требуется выполнить привязку к порту 80. Несмотря на то, что в спецификации pod определенные учетную запись обычного пользователя, этой учетной записи пользователя недостаточно на уровне операционной системы для службы NGINX для запуска и привязать к порту ограниченным доступом.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Опять же очень важно понимать поведение по умолчанию политика безопасности pod. Эта ошибка была немного труднее отследить, и опять же, может оказаться неожиданным основные модуля NGINX запрещается.

Перед переходом к следующему шагу, удалите этот тест pod с помощью [kubectl удалить pod][kubectl-delete] команды:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Создайте политику безопасности пользовательских pod

Теперь, когда вы увидели поведение по умолчанию политика безопасности pod, давайте позволяют *nonadmin пользователя* успешно запланировать модулей POD.

Давайте создадим политику, чтобы отклонить модулей, которые запрашивать привилегированный доступ. Другие параметры, такие как *runAsUser* или разрешено *тома*, не явным образом ограничено только. Этот тип политики отклоняет запрос для привилегированного доступа, а также в противном случае позволяет запуска модулей POD Запрошенная кластера.

Создайте файл с именем `psp-deny-privileged.yaml` и вставьте приведенный ниже YAML манифест:

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

Создайте политику с использованием [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Чтобы просмотреть доступные политики, используйте [kubectl get psp][kubectl-get] команды, как показано в следующем примере. Сравнение *запретить привилегий psp* политики со значением по умолчанию *ограниченных* политики, которое было применено в предыдущих примерах для создания pod. Использование только *PRIV* эскалации запрещен политикой. Нет ограничений на пользователя или группы для *запретить привилегий psp* политики.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Разрешить использование учетной записи пользователя для использования пользовательского модуля политики безопасности

На предыдущем шаге вы создали политику безопасности pod для отклонения модулей этот запрос привилегированный доступ. Чтобы разрешить политику, которую можно использовать, необходимо создать *роли* или *ClusterRole*. Затем можно связать одно из этих ролей с помощью *RoleBinding* или *ClusterRoleBinding*.

Например, создание ClusterRole, которая позволяет *использовать* *запретить привилегий psp* политику, созданную на предыдущем шаге. Создайте файл с именем `psp-deny-privileged-clusterrole.yaml` и вставьте приведенный ниже YAML манифест:

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

Создается при помощи ClusterRole [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Теперь создайте ClusterRoleBinding использовать ClusterRole, созданные на предыдущем шаге. Создайте файл с именем `psp-deny-privileged-clusterrolebinding.yaml` и вставьте приведенный ниже YAML манифест:

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

Создать с помощью ClusterRoleBinding [kubectl применить][kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> На первом шаге этой статьи средство политики безопасности pod была включена в кластере AKS. Рекомендуемая практика было включить средство политики безопасности pod только после определения собственных политик. Это этап, где вы включить средство политики безопасности pod. Были определены один или несколько пользовательских политик, а учетные записи пользователей, которые были связаны с помощью этих политик. Теперь вы можете безопасно политики безопасности pod компонентов и свести к минимуму проблемы, вызванные политик по умолчанию.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Проверка создания непривилегированной pod

Применить политику безопасности пользовательских pod и привязку для учетной записи пользователя для использования политики давайте попробуем создать непривилегированной pod снова. Используйте тот же `nginx-privileged.yaml` манифеста, чтобы создать pod с помощью [kubectl применить][kubectl-apply] команды:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Модуль успешно запланировано. При возврате состояние pod с помощью [kubectl get pods][kubectl-get] команды pod будет *под управлением*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

В этом примере показано, как можно создать пользовательский модуль политики безопасности для определения доступа к кластеру AKS для разных пользователей или групп. По умолчанию, AKS политики предоставляют строгим контролем на какие модулей POD можно запустить, поэтому создать собственные пользовательские политики, чтобы правильно определить ограничения, что нужно.

Удалите pod непривилегированный NGINX с помощью [kubectl удалить][kubectl-delete] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы отключить политику безопасности pod, используйте [обновление az aks][az-aks-update] еще раз выполните команду. В следующем примере отключает pod политику безопасности на имя кластера *myAKSCluster* в группе ресурсов с именем *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Затем удалите ClusterRole и ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Удаление политики сети с помощью [kubectl удалить][kubectl-delete] команду и укажите имя yaml-ФАЙЛ манифеста:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Наконец, удалите *psp aks* пространство имен:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как создать политику безопасности pod для предотвращения использования привилегированного доступа. Существует множество возможностей, которые можно применить политики, такие как тип для тома или запуска от имени пользователя. Дополнительные сведения о доступных параметрах см. в разделе [Kubernetes pod справочные документы политики безопасности][kubernetes-policy-reference].

Дополнительные сведения об ограничении сетевого трафика pod см. в разделе [защиты трафика между модулей с помощью политик сети в AKS][network-policies].

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
