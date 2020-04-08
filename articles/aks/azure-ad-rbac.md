---
title: Используйте Azure AD и RBAC для кластеров
titleSuffix: Azure Kubernetes Service
description: Узнайте, как использовать членство в группе Active Directory, чтобы ограничить доступ к кластерным ресурсам с помощью ролевой системы управления доступом (RBAC) в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803575"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Управление доступом к кластерным ресурсам с помощью элементанного управления доступом и идентификаторов активных каталогов Azure в службе Azure Kubernetes

Службу Azure Kubernetes (AKS) можно настроить на использование Azure Active Directory (AD) для проверки подлинности пользователей. В этой конфигурации вы влюбитесь в кластер AKS с помощью маркера проверки подлинности Azure AD. Можно также настроить управление доступом на основе ролей Kubernetes (RBAC) для ограничения доступа к кластерным ресурсам, основанным на идентификации пользователя или членстве в группе.

В этой статье показано, как использовать членство в группе Azure AD для управления доступом к пространствам имен и кластерным ресурсам с помощью Kubernetes RBAC в кластере AKS. Примеры групп и пользователей создаются в Azure AD, затем в кластере AKS создаются роли и переплеты ролей для предоставления соответствующих разрешений на создание и просмотр ресурсов.

## <a name="before-you-begin"></a>Подготовка к работе

В этой статье предполагается, что с интеграцией Azure AD включен существующий кластер AKS. Если вам нужен кластер AKS, [см.][azure-ad-aks-cli]

Вам нужна версия Azure CLI 2.0.61 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Создание демо-групп в Azure AD

В этой статье давайте создадим две роли пользователей, которые можно использовать для отображания того, как Kubernetes RBAC и Azure AD контролируют доступ к кластерным ресурсам. Используются следующие две роли примера:

* **Разработчик приложений**
    * Пользователь по имени *aksdev,* который является частью группы *appdev.*
* **Инженер по надежности сайта**
    * Пользователь по имени *akssre,* который является частью группы *opssre.*

В производственных средах можно использовать существующих пользователей и групп в рамках арендатора Azure AD.

Во-первых, получите идентификатор ресурсов кластера AKS с помощью команды [шоу az aks.][az-aks-show] Присвоить идентификатор ресурса переменной, названной *AKS_ID,* чтобы на него можно было ссылаться в дополнительных командах.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Создайте первую группу примеров в Azure AD для разработчиков приложений, использующих [команду создания группы рекламы az.][az-ad-group-create] Следующий пример создает группу под названием *appdev:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Создайте назначение ролей Azure для группы *appdev,* используя [команду создания назначения роли аз.][az-role-assignment-create] Это назначение позволяет любому члену группы использовать `kubectl` для взаимодействия с кластером AKS, предоставляя им роль пользователя *кластера обслуживания Azure Kubernetes.*

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Если вы получаете `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`ошибку, такую как, подождите несколько секунд, пока идентификатор `az role assignment create` группы Azure AD будет распространяться через каталог, попробуйте снова.

Создайте вторую группу примеров, эту для SREs, названную *opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Опять же, создайте назначение роли Azure, чтобы предоставить членам группы *роль пользователя кластера обслуживания Azure Kubernetes:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Создание демо-пользователей в Azure AD

С помощью двух примерных групп, созданных в Azure AD для наших разработчиков приложений и SREs, теперь позволяет создавать двух примеров пользователей. Чтобы протестировать интеграцию RBAC в конце статьи, вы восваш— в кластере AKS с этими учетными записями.

Создайте первую учетную запись пользователя в Azure AD с помощью [команды создания рекламы аз-][az-ad-user-create]

Следующий пример создает пользователя с именем отображения *AKS* `aksdev@contoso.com`Dev и главным образом имя пользователя (UPN) . Обновление UPN, чтобы включить проверенный домен для *contoso.com* вашего aure AD-арендатора `--password` (заменить contoso.com собственным доменом) и предоставить свой собственный защищенный учетный данный:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Теперь добавьте пользователя в группу *appdev,* созданную в предыдущем разделе с помощью [команды группы ad az:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Создайте вторую учетную запись пользователя. Следующий пример создает пользователя с именем отображения *AKS SRE* и основным именем пользователя (UPN) `akssre@contoso.com`. Опять же, обновите UPN, чтобы включить проверенный домен для вашего aure AD-арендатора (заменить *contoso.com* с вашим собственным доменом), и предоставить свой собственный безопасный `--password` учетный данный:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Создание кластерных ресурсов AKS для разработчиков приложений

Группы Azure AD и пользователи теперь созданы. Назначения ролей Azure были созданы для того, чтобы члены группы подключались к кластеру AKS в качестве обычного пользователя. Теперь давайте накончим кластер AKS, чтобы обеспечить этим различным группам доступ к определенным ресурсам.

Во-первых, получить учетные данные кластерного админа с помощью команды [az aks получить учетные данные.][az-aks-get-credentials] В одном из следующих разделов вы получаете регулярные учетные данные кластера *пользователей,* чтобы увидеть поток аутентификации Azure AD в действии.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Создайте пространство имен в кластере AKS с помощью [kubectl создать][kubectl-create] команду пространства имен. Следующий пример создает *разработчик*имени:

```console
kubectl create namespace dev
```

В Kubernetes *роли* определяют разрешения на предоставление, а *RoleBindings* применяют их к желаемым пользователям или группам. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Во-первых, создайте роль для пространства имен *разработчиков.* Эта роль предоставляет полные разрешения на пространство имен. В производственных средах можно указать больше детальных разрешений для разных пользователей или групп.

Создайте файл `role-dev-namespace.yaml` с именем и вставьте следующий манифест YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Создайте роль с помощью команды [kubectl применить][kubectl-apply] и указать имя файла вашего манифеста YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Затем получите идентификатор ресурсов для группы *appdev,* используя команду [шоу-шоу ad az.][az-ad-group-show] Эта группа устанавливается в качестве предмета RoleBinding на следующем этапе.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Создайте RoleBinding для группы *appdev,* чтобы использовать ранее созданную Роль для доступа к пространству имен. Создайте файл `rolebinding-dev-namespace.yaml` и вставьте в него следующий манифест YAML. На последней строке замените *groupObjectId* выходом идентификатора группового объекта из предыдущей команды:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Создайте RoleBinding с помощью команды [kubectl применить][kubectl-apply] и указать имя файла вашего манифеста YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Создание кластерных ресурсов AKS для SREs

Теперь повторите предыдущие шаги, чтобы создать пространство имен, роль и переплет для SREs.

Во-первых, создать пространство имен для *sre* с помощью [kubectl создать][kubectl-create] команду пространства имен:

```console
kubectl create namespace sre
```

Создайте файл `role-sre-namespace.yaml` с именем и вставьте следующий манифест YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Создайте роль с помощью команды [kubectl применить][kubectl-apply] и указать имя файла вашего манифеста YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Получите идентификатор ресурсов для *группы opssre,* используя команду [шоу-шоу группы az:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Создайте RoleBinding для *группы opssre,* чтобы использовать ранее созданную Роль для доступа к пространству имен. Создайте файл `rolebinding-sre-namespace.yaml` и вставьте в него следующий манифест YAML. На последней строке замените *groupObjectId* выходом идентификатора группового объекта из предыдущей команды:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Создайте RoleBinding с помощью команды [kubectl применить][kubectl-apply] и указать имя файла вашего манифеста YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Взаимодействие с кластерными ресурсами с помощью идентификаторов Azure AD

Теперь давайте проверим, что ожидаемые разрешения работают при создании и управлении ресурсами в кластере AKS. В этих примерах вы планируете и просматриваете стручки в назначенном пространстве имен пользователя. Затем вы пытаетесь запланировать и просмотреть стручки за пределами назначенного пространства имен.

Во-первых, сбросить контекст *kubeconfig* с помощью команды [az aks получить учетные данные.][az-aks-get-credentials] В предыдущем разделе вы устанавливаете контекст с помощью учетных данных кластерного админ-. Пользователь admin обходит знак Azure AD в подсказках. Без `--admin` параметра применяется пользовательский контекст, который требует проверки подлинности всех запросов с помощью Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Расписание базового стручка NGINX с использованием команды [kubectl run][kubectl-run] в пространстве имен *разработчиков:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Как знак в оперативном порядке, введите учетные данные для вашей учетной `appdev@contoso.com` записи, созданной в начале статьи. После успешного входного в водну можно `kubectl` коньк овеному знаку консея консилиуса для будущих команд. NGINX успешно график, как показано в следующем примере вывода:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Теперь используйте [команду kubectl получить стручки][kubectl-get] для просмотра стручки в пространстве имен *разработчиков.*

```console
kubectl get pods --namespace dev
```

Как показано в следующем выводе примера, стручок NGINX успешно *работает:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Создание и просмотр ресурсов кластера за пределами назначенного пространства имен

Теперь попробуйте просмотреть стручки за пределами пространства имен *разработчиков.* Используйте [kubectl получить стручки][kubectl-get] команды снова, на этот раз, чтобы увидеть: `--all-namespaces`

```console
kubectl get pods --all-namespaces
```

Членство в группе пользователя не имеет роли Kubernetes, которая позволяет это действие, как показано в следующем выводе примера:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Таким же образом, попробуйте запланировать стручок в другом пространстве имен, например, в пространстве *sre* namespace. Членство в группе пользователя не согласуется с ролью Kubernetes и RoleBinding для предоставления этих разрешений, как показано в следующем примере вывода:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Проверьте доступ SRE к ресурсам кластера AKS

Чтобы подтвердить, что членство в группе Azure AD и Kubernetes RBAC работают правильно между различными пользователями и группами, попробуйте предыдущие команды при входе в систему в качестве пользователя *opssre.*

Сбросить контекст *kubeconfig* с помощью команды [az aks get-credentials,][az-aks-get-credentials] которая очищает ранее кэшированный маркер аутентификации для пользователя *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Попробуйте запланировать и просмотреть стручки в назначенном пространстве имен *sre.* При попрошйе `opssre@contoso.com` воспринимайте свои собственные учетные данные, созданные в начале статьи:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Как показано в следующем выводе примера, можно успешно создавать и просматривать стручки:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Теперь попробуйте просмотреть или запланировать стручки за пределами назначенного пространства имен SRE:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Эти `kubectl` команды не удавлены, как показано на следующем выходе примера. Членство в группе пользователя и Kubernetes Role and RoleBindings не дают разрешений на создание или управление ресурсами в других областях имен:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье вы создали ресурсы в кластере AKS, а также пользователей и групп в Azure AD. Чтобы очистить все эти ресурсы, запустите следующие команды:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о том, как обеспечить безопасность кластеров Kubernetes, [см.][rbac-authorization]

Для наилучшей практики по [Best practices for authentication and authorization in AKS][operator-best-practices-identity]идентификации и управлению ресурсами см.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
