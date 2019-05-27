---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Узнайте, как использовать Azure CLI для создания и кластер службы Kubernetes с поддержкой Azure Active Directory Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: d80ad5abecc968a9fe3c82d62ddd8577856a3c54
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835184"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Интеграция Azure Active Directory со службой Azure Kubernetes с помощью Azure CLI

Службу Azure Kubernetes (AKS) можно настроить на использование Azure Active Directory (AD) для проверки подлинности пользователей. В этой конфигурации вы можете войти в кластер AKS с помощью маркера проверки подлинности Azure AD. Операторы кластера можно также настроить на основе участия пользователя удостоверения или directory группе управления Kubernetes доступа на основе ролей (RBAC).

В этой статье показано, как создать необходимые компоненты Azure AD, затем развертывание Azure AD с поддержкой кластера и создать базовый роль RBAC в кластере AKS. Вы также можете [выполните следующие действия, с помощью портала Azure][azure-ad-portal].

Полный пример сценария, используемом в этой статье, см. в разделе [примеры Azure CLI - AKS интеграции с Azure AD][complete-script].

Действительны следующие ограничения.

- Azure Active Directory можно включить только при создании нового кластера с поддержкой RBAC. Вы не можете включить эту службу в существующем кластере AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

Для согласованности и выполните команды в этой статье создайте переменную для желаемого имени кластера AKS. В следующем примере используется имя *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Обзор проверки подлинности Azure AD

Кластеры AKS проходят аутентификацию Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Дополнительные сведения об OpenID Connect см. в [Документации OpenID Connect][open-id-connect].

Внутри кластера Kubernetes проверка подлинности на основе маркера веб-перехватчика используется для проверки маркеров проверки подлинности. Настройка такой проверка подлинности и ее управление являются частью кластера AKS. Дополнительные сведения о проверке подлинности на основе маркера веб-перехватчика см. в [Документации по аутентификации веб-перехватчика][kubernetes-webhook].

> [!NOTE]
> При настройке Azure AD для проверки подлинности AKS настраиваются два приложения Azure AD. Эту операцию должен выполнять администратор клиента Azure.

## <a name="create-azure-ad-server-component"></a>Создание компонента сервера Azure AD

Чтобы интегрировать с AKS, создайте и используйте приложение Azure AD, который выступает в качестве конечной точки для идентификации запросов. Первое приложение Azure AD, что нужно получает членства в группе Azure AD для пользователя.

Создание компонента приложения сервера с помощью [az ad app создайте] [ az-ad-app-create] команда, а затем с помощью утверждений членства в группе обновления [az ad app обновления] [ az-ad-app-update] команды. В следующем примере используется *aksname* переменная, определенная в [перед началом](#before-you-begin) , при этом создается переменная

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Теперь создайте субъект-службу для сервера приложений с помощью [создать az ad sp] [ az-ad-sp-create] команды. Этот субъект-служба используется для проверки подлинности в пределах платформы Azure. Затем получите службы субъекта секрета с помощью [сбросить учетные данные az ad sp] [ az-ad-sp-credential-reset] команды и присвоить переменной с именем *serverApplicationSecret* для использования в одном из следующие действия:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD необходимы разрешения для выполнения следующих действий:

* Прочитать данные каталога
* Вход и чтение профиля пользователя

Назначить эти разрешения, используя [добавьте az ad app разрешение] [ az-ad-app-permission-add] команды:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Наконец, предоставить разрешения, назначенные в предыдущем шаге для сервера приложений с помощью [az ad app разрешение] [ az-ad-app-permission-grant] команды. Этот шаг завершается ошибкой, если текущая учетная запись не является администратором клиента Необходимо также добавить разрешения для приложения Azure AD для запроса данных, в противном случае может потребоваться административное разрешение с помощью [az ad app разрешение — согласие администратора][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Создание компонента клиента Azure AD

Второе приложение Azure AD используется в том случае, когда пользователь входит в кластер AKS с помощью интерфейса командной строки Kubernetes (`kubectl`). Это клиентское приложение принимает запрос на аутентификацию от пользователя и проверяет их учетные данные и разрешения. Создание приложения Azure AD для клиента компонент с помощью [az ad app создайте] [ az-ad-app-create] команды:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Создайте субъект-службу для клиентское приложение, использующее [создать az ad sp] [ az-ad-sp-create] команды:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Получить идентификатор oAuth2 для серверного приложения на разрешение потока проверки подлинности между двумя компонентами приложения с помощью [az ad app show] [ az-ad-app-show] команды. Этот идентификатор oAuth2 используется на следующем шаге.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Добавление разрешений для клиентского приложения и серверные компоненты приложения для использования oAuth2 по протоколу потока с помощью [добавьте az ad app разрешение] [ az-ad-app-permission-add] команды. Предоставьте разрешения для клиентского приложения для обмена данными с сервера приложений с помощью [az ad app разрешение] [ az-ad-app-permission-grant] команды:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Развертывание кластера

С помощью создания два приложения Azure AD Теперь создайте самого кластера AKS. Сначала создайте группу ресурсов с помощью [Создание группы az] [ az-group-create] команды. В следующем примере создается группа ресурсов в *EastUS* региона:

Создайте группу ресурсов для кластера:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Получение идентификатора клиента из своей подписки Azure с помощью [az учетной записи, отображают] [ az-account-show] команды. Затем создайте кластер AKS с помощью [создать az aks] [ az-aks-create] команды. Команду, чтобы создать кластер AKS предоставляет сервер и клиент, идентификаторы приложений, секрет субъекта-сервера приложения службы и идентификатор клиента:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Наконец, получить кластера с помощью учетных данных администратора [az aks get-credentials] [ az-aks-get-credentials] команды. В одном из следующих действий, вы получаете стандартным *пользователя* учетных данных для проверки подлинности Azure AD см. в разделе кластера потока в действии.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Создание привязки RBAC

Прежде чем использовать учетную запись Azure Active Directory с кластером AKS, необходимо создать привязку роли или привязку роли кластера. *Роли* определяют разрешения для предоставления, а *привязки* применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Получить имя участника-пользователя (UPN), для пользователь в настоящее время выполнил вход с помощью [az ad войдете в систему пользователя show] [ az-ad-signed-in-user-show] команды. Эта учетная запись пользователя включена для интеграции с Azure AD на следующем шаге.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Если вы предоставляете привязки RBAC для пользователя в одном клиенте Azure AD, назначать разрешения на основе *userPrincipalName*. Если пользователь находится в другой каталог Azure AD клиента, запрашивать и использовать *objectId* свойство вместо этого.

Создание yaml-ФАЙЛ манифеста с именем `basic-azure-ad-binding.yaml` и вставьте следующее содержимое. В последней строке, замените *userPrincipalName_or_objectId* с выходными данными идентификатор имени участника-пользователя или объекта из предыдущей команды:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Создается при помощи ClusterRoleBinding [применить kubectl] [ kubectl-apply] команду и укажите имя файла yaml-ФАЙЛ манифеста:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Доступ к кластеру с помощью Azure AD

Теперь давайте протестируем интеграции проверки подлинности Azure AD в кластере AKS. Задайте `kubectl` контекста конфигурации для использования учетных данных для обычных пользователей. Этот контекст передает все запросы проверки подлинности через Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Теперь с помощью [kubectl get pods] [ kubectl-get] команду, чтобы просматривать модулей для всех пространств имен:

```console
kubectl get pods --all-namespaces
```

Появляется знак в строке для проверки подлинности, используя учетные данные Azure AD в веб-браузере. После вы успешно прошли проверку подлинности, `kubectl` команда отображает POD, содержащихся в кластере AKS, как показано в следующем примере выходных данных:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Полученный маркер проверки подлинности для `kubectl` кэшируется. Кроме того, являются только reprompted для входа при истечении срока действия токена или файле конфигурации Kubernetes создается заново.

Если вы видите сообщение об ошибке авторизации, после успешного входа в браузере, как показано в следующем примере выходных данных, проверьте следующие возможные причины:

```console
error: You must be logged in to the server (Unauthorized)
```

* Соответствующий идентификатор объекта или имя участника-пользователя, зависимости, является ли учетная запись пользователя в одном клиенте Azure AD или не определен.
* Пользователь не может быть членом более чем 200 групп.
* Секрет, определенные в регистрации приложения для сервера совпадает со значением, настроенные с помощью `--aad-server-app-secret`

## <a name="next-steps"></a>Дальнейшие действия

Полный скрипт, который содержит команды, показанные в этой статье, см. в разделе [сценарий интеграции Azure AD в AKS примеры репозитория][complete-script].

Чтобы использовать Azure AD — пользователи и группы для управления доступом к ресурсам кластера, см. в разделе [управления доступом к ресурсам кластера, с помощью управления доступом и удостоверениями Azure AD в AKS][azure-ad-rbac].

Дополнительные сведения о защите кластеров Kubernetes см. в разделе [параметры доступа и удостоверений для AKS)][rbac-authorization].

Советы и рекомендации для управления удостоверениями и ресурсов, см. в разделе [советы и рекомендации для проверки подлинности и авторизации в AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
