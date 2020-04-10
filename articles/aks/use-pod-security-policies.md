---
title: Используйте политики безопасности pod в службе Безопасности Azure Kubernetes (AKS)
description: Узнайте, как контролировать прием стручков с помощью PodSecurityPolicy в azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998357"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Предварительный просмотр - Безопасность кластера с помощью политик безопасности стручка в службе Azure Kubernetes (AKS)

Чтобы повысить безопасность кластера AKS, можно ограничить запланированные стручки. Стручки, запрашивающие ресурсы, которые вы не разрешаете, не могут работать в кластере AKS. Этот доступ определяется с помощью политик безопасности pod. В этой статье показано, как использовать политики безопасности стручка для ограничения развертывания стручков в AKS.

> [!IMPORTANT]
> Функции предварительного просмотра AKS являются отказом от самообслуживания. Предварительные просмотры предоставляются "как есть" и "по мере возможности" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Анонсы AKS частично покрываются поддержкой клиентов на основе наилучших усилий. Таким образом, эти функции не предназначены для использования в производстве. Для получения дополнительной информации, пожалуйста, смотрите следующие статьи поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам нужна версия Azure CLI 2.0.61 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования политик безопасности pod вам нужна версия расширения *CLI aks-preview* 0.4.1 или выше. Установите расширение *aks-preview* Azure CLI с помощью команды [расширения az,][az-extension-add] а затем проверьте наличие доступных обновлений с помощью команды [обновления расширения аз:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Регистрация поставщика функций политики безопасности pod

Для создания или обновления кластера AKS для использования политик безопасности pod сначала включите флаг функции в подписке. Чтобы зарегистрировать флаг функции *PodSecurityPolicyPreview,* используйте команду [регистра функций az,][az-feature-register] как показано в следующем примере:

> [!CAUTION]
> При регистрации функции по подписке вы не можете в настоящее время отменить регистрацию этой функции. После включения некоторых функций предварительного просмотра по умолчанию могут использоваться для всех кластеров AKS, а затем созданных в подписке. Не включайте функции предварительного просмотра в производственных подписках. Используйте отдельную подписку для тестирования функций предварительного просмотра и сбора отзывов.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Обзор политик безопасности стручка

В кластере Kubernetes контроллер приема используется для перехвата запросов на сервер API при создании ресурса. Контроллер приема может *затем проверить* запрос ресурса на набор правил или *мутировать* ресурс для изменения параметров развертывания.

*PodSecurityPolicy* является контроллером приема, который проверяет спецификацию стручка, отвечающего определенным требованиям. Эти требования могут ограничивать использование привилегированных контейнеров, доступ к определенным типам хранилища или пользователю или группе контейнера. При попытке развертывания ресурса, в котором спецификации стручка не отвечают требованиям, изложенным в политике безопасности стручка, запрос отклоняется. Эта способность контролировать то, что стручки могут быть запланированы в кластере AKS предотвращает некоторые возможные уязвимости безопасности или эскалации привилегий.

При вменить политику безопасности pod в кластере AKS применяются некоторые политики по умолчанию. Эти политики по умолчанию предоставляют нестандартный опыт для определения того, какие стручки могут быть запланированы. Однако пользователи кластеров могут столкнуться с проблемами, когда будут развернуты стручки, пока вы не определите свои собственные политики. Рекомендуемый подход заключается в:

* Создание кластера AKS
* Определите свои собственные политики безопасности стручка
* Включить функцию политики безопасности стручка

Чтобы показать, как политики по умолчанию ограничивают развертывание стручков, в этой статье мы сначала включаем функцию политики безопасности pod, а затем создаем пользовательскую политику.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Включить политику безопасности стручка в кластере AKS

Вы можете включить или отключить политику безопасности стручка с помощью команды [обновления az aks.][az-aks-update] Следующий пример позволяет политику безопасности стручка на названии *кластера myAKSCluster* в группе ресурсов под названием *myResourceGroup.*

> [!NOTE]
> Для использования в реальном мире не включайте политику безопасности стручка до тех пор, пока не определите свои собственные пользовательские политики. В этой статье вы можете включить политику безопасности pod в качестве первого шага, чтобы увидеть, как политики по умолчанию ограничивают развертывание стручков.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Политики AKS по умолчанию

При включании политики безопасности pod AKS создает одну политику по умолчанию, названную *привилегированной.* Не издавайте и не удаляйте политику по умолчанию. Вместо этого создайте свои собственные политики, определяющие параметры, которые вы хотите контролировать. Давайте сначала рассмотрим, какие политики по умолчанию влияют на развертывание стручков.

Для просмотра доступных политик используйте команду [kubectl,][kubectl-get] как показано в следующем примере

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Привилегированные* политики безопасности стручка применяются к любому подлинному пользователю в кластере AKS. Это назначение контролируется ClusterRoles и ClusterRoleBindings. Используйте [команду kubectl получить команду rolebindings][kubectl-get] и поиск *по умолчанию:privileged:* связывание в пространстве имен *kube-системы:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Как показано на следующем сжатом выходе, *psp:restricted* ClusterRole назначается любой *системе:аутентифицированных* пользователей. Эта способность обеспечивает базовый уровень ограничений без определения собственных политик.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Важно понять, как эти политики по умолчанию взаимодействуют с запросами пользователей для планирования стручков, прежде чем вы начнете создавать свои собственные политики безопасности стручка. В следующих нескольких разделах давайте запланивне несколько стручков, чтобы увидеть эти политики по умолчанию в действии.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Создание тестового пользователя в кластере AKS

По умолчанию при использовании команды [az aks get-credentials][az-aks-get-credentials] учетные данные *админа* `kubectl` для кластера AKS добавляются в конфигурацию. Пользователь-админ обходит правила обеспечения безопасности pod. Если вы используете интеграцию Active Directory Azure для кластеров AKS, можно войти в систему с учетными данными пользователя, не являющаяся аудиенцией, чтобы увидеть соблюдение политик в действии. В этой статье давайте создадим учетную запись пользователя теста в кластере AKS, который можно использовать.

Создайте образец пространства имен под названием *psp-aks* для тестовых ресурсов, используя [команду kubectl, создавая][kubectl-create] команду пространства имен. Затем создайте учетную запись службы под названием *nonadmin-user,* используя команду [kubectl, создайте команду serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Затем создайте RoleBinding для *пользователя nonadmin* для выполнения основных действий в пространстве имен с помощью [kubectl создать команду rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Создание имено-именных команд для пользователя admin и не-админ

Чтобы подчеркнуть разницу между обычным пользователем `kubectl` админ при использовании и пользователем, не являющийся адемином, созданным в предыдущих шагах, создайте два псевдонима командной строки:

* Псевдоним **kubectl-admin** предназначен для обычного пользователя админ, и прикован к пространстве имени *psp-aks.*
* Псевдоним **kubectl-nonadminuser** предназначен для *пользователя nonadmin,* созданного на предыдущем этапе, и прикован к пространстве имен *psp-aks.*

Создайте эти два псевдонима, как показано в следующих командах:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Проверьте создание привилегированном стручку

Давайте сначала проверим, что происходит, когда вы `privileged: true`запланировали стручок с контекстом безопасности. Этот контекст безопасности обостряет привилегии стручка. В предыдущем разделе, в ходе которого были показаны политики безопасности AKS по умолчанию, *ограниченная* политика должна отклонить этот запрос.

Создайте файл `nginx-privileged.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте стручок с помощью команды [kubectl применить][kubectl-apply] и указать имя вашего манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Стручок не запланирован, как показано на следующем выходе примера:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Стручок не достигает стадии планирования, поэтому нет ресурсов для удаления, прежде чем двигаться дальше.

## <a name="test-creation-of-an-unprivileged-pod"></a>Тест-творение непривилегированном стручку

В предыдущем примере спецификация стручка просила привилегированную эскалацию. Этот запрос отклоняется политикой безопасности *с ограниченной* по умолчанию, поэтому стручок не может быть запланирован. Давайте попробуем теперь запустить тот же модуль NGINX без запроса на эскалацию привилегий.

Создайте файл `nginx-unprivileged.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте стручок с помощью команды [kubectl применить][kubectl-apply] и указать имя вашего манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Стручок не запланирован, как показано на следующем выходе примера:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Стручок не достигает стадии планирования, поэтому нет ресурсов для удаления, прежде чем двигаться дальше.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Тест создания стручка с определенным контекстом пользователя

В предыдущем примере изображение контейнера автоматически пыталось использовать root для связывания NGINX с портом 80. Эта просьба была отклонена политикой безопасности *с ограниченной* по умолчанию, поэтому стручок не может начаться. Давайте попробуем теперь запустить тот же ngINX стручок с определенным контекстом пользователя, `runAsUser: 2000`например.

Создайте файл `nginx-unprivileged-nonroot.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте стручок с помощью команды [kubectl применить][kubectl-apply] и указать имя вашего манифеста YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Стручок не запланирован, как показано на следующем выходе примера:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Стручок не достигает стадии планирования, поэтому нет ресурсов для удаления, прежде чем двигаться дальше.

## <a name="create-a-custom-pod-security-policy"></a>Создание пользовательской политики безопасности стручка

Теперь, когда вы увидели поведение политик безопасности стручка по умолчанию, давайте предоставим возможность для *пользователя nonadmin* успешно планировать стручки.

Давайте создадим политику для отклонения стручков, запрашиваемых привилегированным доступом. Другие опции, такие как *runAsUser* или *разрешенные тома,* явно не ограничены. Этот тип политики отклоняет запрос на привилегированный доступ, но в противном случае позволяет кластеру запускать запрошенные стручки.

Создайте файл `psp-deny-privileged.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте политику, используя команду [kubectl apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Для просмотра доступных политик используйте команду [kubectl,][kubectl-get] как показано в следующем примере. Сравните политику *psp-deny-privileged* с *ограниченной* по умолчанию политикой, которая была применена в предыдущих примерах для создания стручка. Только использование *PRIV* эскалации отрицается вашей политикой. Нет никаких ограничений для пользователя или группы для политики *psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Разрешить учетной записи пользователя использовать пользовательскую политику безопасности стручка

На предыдущем этапе была создана политика безопасности для отклонения стручков, запрашиваемых привилегированным доступом. Чтобы разрешить использовать политику, создается *роль* или *clusterRole.* Затем вы связываете одну из этих ролей с помощью *RoleBinding* или *ClusterRoleBinding.*

В этом примере создайте clusterRole, который позволяет *использовать* политику *psp-deny-privileged,* созданную на предыдущем этапе. Создайте файл `psp-deny-privileged-clusterrole.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте ClusterRole, используя команду [kubectl apply,][kubectl-apply] и укажите название манифеста YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Теперь создайте ClusterRoleBinding для использования ClusterRole, созданного на предыдущем этапе. Создайте файл `psp-deny-privileged-clusterrolebinding.yaml` с именем и вставьте следующий манифест YAML:

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

Создайте ClusterRoleBinding с помощью команды [kubectl применить][kubectl-apply] и указать имя вашего манифеста YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> На первом этапе этой статьи функция политики безопасности pod была включена в кластер AKS. Рекомендуемая практика состояла в том, чтобы включить функцию политики безопасности pod только после того, как вы определили свои собственные политики. Это этап, на котором можно включить функцию политики безопасности стручка. Определена одна или несколько пользовательских политик, и учетные записи пользователей были связаны с этими политиками. Теперь можно безопасно включить функцию политики безопасности стручка и минимизировать проблемы, вызванные политиками по умолчанию.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Проверьте создание непривилегированном стручка снова

При применении пользовательской политики безопасности стручка и привязке к учетной записи пользователя для использования политики давайте попробуем снова создать непривилегированный модуль. Используйте `nginx-privileged.yaml` тот же манифест для создания стручка с помощью команды [kubectl применить:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Стручок успешно запланирован. Когда вы проверяете состояние стручка с помощью [kubectl получить стручки][kubectl-get] команды, стручок *Работает:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

В этом примере показано, как можно создать пользовательские политики безопасности для определения доступа к кластеру AKS для разных пользователей или групп. Политики AKS по умолчанию обеспечивают жесткий контроль над тем, какие стручки могут работать, поэтому создайте свои собственные пользовательские политики, чтобы затем правильно определить необходимые ограничения.

Удалите непривилегированный модуль NGINX с помощью команды [удаления kubectl][kubectl-delete] и укажите имя вашего манифеста YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы отключить политику безопасности pod, используйте команду [обновления az aks][az-aks-update] снова. Следующий пример отсутсвий политики безопасности стручка на *названии кластера myAKSCluster* в группе ресурсов под названием *myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Далее, удалите ClusterRole и ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Удалите политику безопасности с помощью команды [удаления kubectl][kubectl-delete] и укажите имя манифеста YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Наконец, удалите пространство имен *psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Следующие шаги

В этой статье показано, как создать политику безопасности стручка для предотвращения использования привилегированном доступе. Существует множество функций, которые может выполнять политика, например тип громкости или пользователь RunAs. Для получения дополнительной информации о [Kubernetes pod security policy reference docs][kubernetes-policy-reference]доступных опциях см.

Для получения дополнительной информации об ограничении сетевого трафика, см [Безопасный трафик между стручками с помощью сетевых политик в AKS][network-policies].

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
