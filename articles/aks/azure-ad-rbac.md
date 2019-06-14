---
title: Ресурсы кластера управления RBAC и Azure AD в службе Azure Kubernetes
description: Узнайте, как использовать членство в группе Azure Active Directory для ограничения доступа к ресурсам кластера, с помощью управления доступом на основе ролей (RBAC) в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467550"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Управление доступом к ресурсам кластера, используя управление доступом на основе ролей и удостоверения Azure Active Directory в службе Azure Kubernetes

Службу Azure Kubernetes (AKS) можно настроить на использование Azure Active Directory (AD) для проверки подлинности пользователей. В этой конфигурации вы войти в кластер AKS с помощью маркера проверки подлинности Azure AD. Вы также можете настроить Kubernetes управления доступом на основе ролей (RBAC) для ограничения доступа к ресурсам кластера на основе членства пользователя удостоверения или группы.

В этой статье показано, как использовать членство в группе Azure AD для контроля доступа к пространствам имен и с помощью Kubernetes RBAC в кластере AKS ресурсы кластера. Например, группы и пользователи создаются в Azure AD, а затем в кластере AKS, чтобы предоставить соответствующие разрешения для создания и просмотра ресурсов создаются роли и RoleBindings.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть существующий кластер AKS включен с интеграцией Azure AD. Если вам нужен кластер AKS, см. в разделе [интегрировать Azure Active Directory с AKS][azure-ad-aks-cli].

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Создание групп демонстрации в Azure AD

В этой статье давайте создадим две роли пользователей, которые могут использоваться для отображения, как Kubernetes RBAC и Azure AD управляют доступом к ресурсам кластера. Используются следующие два примера роли:

* **Разработчик приложения**
    * Пользователь с именем *aksdev* , является частью *appdev* группы.
* **Инженер по надежности сайта**
    * Пользователь с именем *akssre* , является частью *opssre* группы.

В производственной среде можно использовать существующих пользователей и групп в клиенте Azure AD.

Сначала получите идентификатор ресурса кластера AKS с помощью [az aks show] [ az-aks-show] команды. Назначьте идентификатор ресурса с именем переменной *AKS_ID* таким образом, чтобы его можно ссылаться в дополнительные команды.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Создание первой группы пример в Azure AD для разработчиков приложений, использующих [создать группы Active Directory az] [ az-ad-group-create] команды. В следующем примере создается группа с именем *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Теперь создайте назначения ролей Azure для *appdev* группы с использованием [Создание назначений ролей az] [ az-role-assignment-create] команды. Это назначение позволяет использовать любой член группы `kubectl` для взаимодействия с кластером AKS, предоставив этому пользователю *роли пользователя кластера для службы Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Если появляется сообщение об ошибке `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, подождите несколько секунд, идентификатор объекта группы Azure AD для распространения через каталог, а затем повторите `az role assignment create` еще раз выполните команду.

Создайте группу второй пример, предназначенная для SREs с именем *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Опять же, Создание назначений ролей Azure, чтобы предоставить членам группы *роли пользователя кластера для службы Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Создать Демонстрация пользователей в Azure AD

С двумя группами пример, созданные в Azure AD для разработчиков приложений из наших SREs теперь давайте создадим два примера пользователя. Проверка интеграции RBAC в конце статьи, вы вход в кластер AKS с помощью этих учетных записей.

Создание первой учетной записи пользователя в Azure AD с помощью [создать пользователей ad az] [ az-ad-user-create] команды.

В следующем примере создается пользователь с отображаемым именем *разработки AKS* и имя участника-пользователя (UPN) из `aksdev@contoso.com`. Обновить имя участника-пользователя для включения проверенного домена для вашего клиента Azure AD (Замените *contoso.com* с именем вашего домена) и предоставляют свои собственные безопасные `--password` учетных данных:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Теперь добавьте пользователя *appdev* группу, созданную в предыдущем разделе, с помощью [добавить члена группы ad az] [ az-ad-group-member-add] команды:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Создайте вторую учетную запись пользователя. В следующем примере создается пользователь с отображаемым именем *AKS SRE* и имя участника-пользователя (UPN) из `akssre@contoso.com`. Опять же, обновите имя участника-пользователя для включения проверенного домена для вашего клиента Azure AD (Замените *contoso.com* с именем вашего домена) и предоставляют свои собственные безопасные `--password` учетных данных:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Создать ресурсы кластера AKS для разработчиков приложений

Группы Azure AD и пользователи будут созданы. Назначения ролей Azure были созданы для членов группы для подключения к кластеру AKS как для обычного пользователя. Теперь давайте настроим кластера AKS, чтобы разрешить этим разным группам доступ к определенным ресурсам.

Сначала необходимо получить учетные данные администратора с помощью кластера [az aks get-credentials] [ az-aks-get-credentials] команды. В одном из следующих разделов, вы получаете стандартным *пользователя* учетных данных для проверки подлинности Azure AD см. в разделе кластера потока в действии.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Создание пространства имен в кластере AKS с помощью [kubectl создание пространства имен] [ kubectl-create] команды. В следующем примере создается имя пространства имен *разработки*:

```console
kubectl create namespace dev
```

В Kubernetes *ролей* определить разрешения, чтобы предоставить, и *RoleBindings* применить их к необходимых пользователей или групп. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Во-первых, создайте роль для *разработки* пространства имен. Эта роль предоставляет полный набор разрешений на пространство имен. В производственной среде можно указать более детальные разрешения для разных пользователей или групп.

Создайте файл с именем `role-dev-namespace.yaml` и вставьте приведенный ниже YAML манифест:

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

Создание роли с помощью [применить kubectl] [ kubectl-apply] команду и укажите имя файла yaml-ФАЙЛ манифеста:

```console
kubectl apply -f role-dev-namespace.yaml
```

Затем получите идентификатор ресурса для *appdev* группы с использованием [Показать группы ad az] [ az-ad-group-show] команды. Эта группа будет задан предметом RoleBinding на следующем шаге.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Теперь создайте RoleBinding для *appdev* группы для использования ранее созданной роли доступ к пространству имен. Создайте файл `rolebinding-dev-namespace.yaml` и вставьте в него следующий манифест YAML. В последней строке, замените *groupObjectId* с выходными данными идентификатор объекта группы из предыдущей команды:

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

Создается при помощи RoleBinding [применить kubectl] [ kubectl-apply] команду и укажите имя файла yaml-ФАЙЛ манифеста:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Создать ресурсы кластера AKS SREs

Теперь повторите предыдущие шаги, чтобы создать пространство имен, роли и RoleBinding для SREs.

Во-первых, создайте пространство имен для *sre* с помощью [kubectl создание пространства имен] [ kubectl-create] команды:

```console
kubectl create namespace sre
```

Создайте файл с именем `role-sre-namespace.yaml` и вставьте приведенный ниже YAML манифест:

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

Создание роли с помощью [применить kubectl] [ kubectl-apply] команду и укажите имя файла yaml-ФАЙЛ манифеста:

```console
kubectl apply -f role-sre-namespace.yaml
```

Получить идентификатор ресурса для *opssre* группы с использованием [Показать группы ad az] [ az-ad-group-show] команды:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Создание RoleBinding для *opssre* группы для использования ранее созданной роли доступ к пространству имен. Создайте файл `rolebinding-sre-namespace.yaml` и вставьте в него следующий манифест YAML. В последней строке, замените *groupObjectId* с выходными данными идентификатор объекта группы из предыдущей команды:

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

Создается при помощи RoleBinding [применить kubectl] [ kubectl-apply] команду и укажите имя файла yaml-ФАЙЛ манифеста:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Взаимодействовать с ресурсами кластера, с помощью удостоверений Azure AD

Теперь давайте протестируем ожидаемые разрешения работают при создании и управлении ресурсами в кластере AKS. В этих примерах можно запланировать и просмотреть POD, содержащихся в пространстве имен назначенного пользователя. Затем предпринимается попытка расписание и представление модулей вне назначенного пространства имен.

Во-первых, сброс *kubeconfig* контекста с помощью [az aks get-credentials] [ az-aks-get-credentials] команды. В предыдущем разделе задается контекст, используя учетные данные администратора кластера. Учетную запись администратора обходит входа Azure AD в запросах. Без `--admin` применяется параметр, контекст пользователя, которой требуются все запросы должны проходить проверку подлинности с помощью Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Основные pod NGINX с помощью расписания [запустите kubectl] [ kubectl-run] в команду *разработки* пространство имен:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Как и знак в строке, введите учетные данные для своего собственного `appdev@contoso.com` учетной записи, созданной в начале статьи. После успешного входа, токен учетной записи кэшируются для будущего `kubectl` команды. NGINX успешно запланировать, как показано в следующем примере выходных данных:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Теперь с помощью [kubectl get pods] [ kubectl-get] команду, чтобы просмотреть POD, содержащихся в *разработки* пространства имен.

```console
kubectl get pods --namespace dev
```

Как показано в следующем примере выходных данных модуля NGINX — успешно *под управлением*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Создание и просмотр ресурсов кластера вне назначенного пространства имен

Теперь при попытке просмотра модулей за пределами *разработки* пространства имен. Используйте [kubectl get pods] [ kubectl-get] команду еще раз, чтобы см. в разделе `--all-namespaces` следующим образом:

```console
kubectl get pods --all-namespaces
```

Членство в группе пользователей не имеет роль Kubernetes, которая позволяет это действие, как показано в следующем примере выходных данных:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Таким же образом, попытайтесь запланировать pod в другое пространство имен, такие как *sre* пространства имен. Членство в группе пользователей не согласуются со роли Kubernetes и RoleBinding предоставлять эти разрешения, как показано в следующем примере выходных данных:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Проверка доступа SRE и ресурсы кластера AKS

Чтобы убедиться, что наши членство в группе Azure AD и Kubernetes RBAC работают между разными пользователями и группами, повторите приведенные выше команды, когда в систему как *opssre* пользователя.

Сброс *kubeconfig* контекста с помощью [az aks get-credentials] [ az-aks-get-credentials] команду, которая очищает токена проверки подлинности ранее кэшированных *aksdev*  пользователя:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Попробуйте расписание и представление модулей в назначенном *sre* пространства имен. Когда появится запрос, войдите, используя собственные `opssre@contoso.com` учетных данных, созданных в начале статьи:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Как показано в следующем примере выходных данных, можно успешно создавать и просматривать модулей POD:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Теперь попробуйте просматривать и планировать модулей вне назначенного пространства имен в редакторе правил Синхронизации:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Эти `kubectl` команды завершиться ошибкой, как показано в следующем примере выходных данных. Членство в группе пользователя и роль Kubernetes и RoleBindings не предоставить разрешения на создание или диспетчер ресурсов в других пространствах имен:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье вы создали ресурсы в кластере AKS и пользователи и группы в Azure AD. Чтобы очистить все ресурсы, выполните следующие команды:

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

Дополнительные сведения о защите кластеров Kubernetes см. в разделе [параметры доступа и удостоверений для AKS)][rbac-authorization].

Советы и рекомендации для управления удостоверениями и ресурсов, см. в разделе [советы и рекомендации для проверки подлинности и авторизации в AKS][operator-best-practices-identity].

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
