---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Узнайте, как использовать Azure CLI для создания и кластера службы Azure Active Directory С поддержкой Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253056"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Интеграция active-каталога Azure с сервисом Azure Kubernetes с помощью Azure CLI

Службу Azure Kubernetes (AKS) можно настроить на использование Azure Active Directory (AD) для проверки подлинности пользователей. В этой конфигурации можно войти в кластер AKS с помощью маркера проверки подлинности Azure AD. Операторы кластеров также могут настроить управление доступом на основе ролей Kubernetes (RBAC) на основе идентификации пользователя или членства в группе каталогов.

В этой статье показано, как создать необходимые компоненты Azure AD, а затем развернуть кластер с поддержкой Azure AD и создать основную роль RBAC в кластере AKS. Вы также можете [выполнить эти шаги с помощью портала Azure][azure-ad-portal].

Для полного образца сценария, [Azure CLI samples - AKS integration with Azure AD][complete-script]используемого в этой статье, см.

Действительны следующие ограничения.

- Azure Active Directory можно включить только при создании нового кластера с поддержкой RBAC. Вы не можете включить эту службу в существующем кластере AKS.

## <a name="before-you-begin"></a>Перед началом

Вам нужна версия Azure CLI 2.0.61 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][install-azure-cli]

Перейдите [https://shell.azure.com](https://shell.azure.com) к открытию Cloud Shell в браузере.

Для согласованности и для выполнения команд в этой статье создайте переменную для желаемого имени кластера AKS. В следующем примере используется название *myakscluster:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Обзор аутентификации Azure AD

Кластеры AKS проходят аутентификацию Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Для получения дополнительной информации о [Open ID connect documentation][open-id-connect]OpenID Connect см.

Внутри кластера Kubernetes проверка подлинности на основе маркера веб-перехватчика используется для проверки маркеров проверки подлинности. Настройка такой проверка подлинности и ее управление являются частью кластера AKS. Дополнительные сведения о проверке подлинности на основе маркера веб-перехватчика см. в [Документации по аутентификации веб-перехватчика][kubernetes-webhook].

> [!NOTE]
> При настройке Azure AD для аутентификации AKS настраиваются два приложения Azure AD. Эту операцию должен выполнять администратор клиента Azure.

## <a name="create-azure-ad-server-component"></a>Создание компонента AD-сервера Azure

Чтобы интегрироваться с AKS, необходимо создать и использовать приложение Azure AD, которое действует как конечная точка для запросов идентификации. Первое приложение Azure AD, которое вам нужно, получает членство в группе Azure AD для пользователя.

Создайте компонент приложения сервера с помощью команды создания [рекламного приложения АЗ,][az-ad-app-create] а затем обновите требования членства в группе с помощью команды [обновления рекламного приложения аз.][az-ad-app-update] В следующем примере используется переменная *aksname,* определенная в разделе [«Прежде чем начать»,](#before-you-begin) и создается переменная

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Теперь создайте принцип службы для приложения сервера, используя команду [az ad sp.][az-ad-sp-create] Этот принцип службы используется для проверки подлинности в платформе Azure. Затем получите главный секрет службы с помощью команды [сбросить учетные данные az ad sp][az-ad-sp-credential-reset] и присвоите переменной названному *серверуApplicationSecret* для использования в одном из следующих шагов:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

AD Azure aD требуется разрешение для выполнения следующих действий:

* Прочитать данные каталога
* Вход в систему и чтение профиля пользователя.

Назначайте эти разрешения с помощью [разрешения приложения АЗ::][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Наконец, предоставите разрешения, назначенные на предыдущем этапе для серверного приложения, используя команду [разрешения на рекламное приложение аз.][az-ad-app-permission-grant] Этот шаг завершается неудачей, если текущий счет не является админ-клиентом-арендатором. Также необходимо добавить разрешения для приложения Azure AD для запроса информации, которая в противном случае может потребовать административного согласия с помощью [разрешения ad-приложения аз:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Создание компонента aD AD Azure

Второе приложение Azure AD используется при входе пользователя в кластер AKS с`kubectl`Kubernetes CLI ( ). Это клиентское приложение принимает запрос на аутентификацию от пользователя и проверяет его учетные данные и разрешения. Создайте приложение Azure AD для клиентского компонента с помощью команды [ad az:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Создайте принцип службы для клиентского приложения, используя команду [az ad sp:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Получите идентификатор oAuth2 для серверного приложения, чтобы обеспечить поток аутентификации между двумя компонентами приложения с помощью команды [шоу-приложения az.][az-ad-app-show] Этот идентификатор oAuth2 используется на следующем этапе.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Добавьте разрешения для компонентов клиентского приложения и серверного приложения для использования коммуникационного потока oAuth2 с помощью [команды добавления разрешения на рекламное приложение аз.][az-ad-app-permission-add] Затем предоставите разрешение клиенту на общение с серверным приложением с помощью команды [выдачи разрешения][az-ad-app-permission-grant] на рекламное приложение АЗ:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Развертывание кластера

Теперь, когда созданы два приложения Azure AD, создайте сам кластер AKS. Во-первых, создайте группу ресурсов, используя [группу az, создавая][az-group-create] команду. Следующий пример создает группу ресурсов в регионе *Восточного США:*

Создайте группу ресурсов для кластера:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Получите идентификатор клиента подписки Azure с помощью команды [шоу-аккаунта az.][az-account-show] Затем создайте кластер AKS с помощью [az aks, создайте][az-aks-create] команду. Команда для создания кластера AKS предоставляет идентификаторы сервера и клиентского приложения, главный секрет службы приложений сервера и идентификатор клиента:

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

Наконец, получите учетные данные кластерного админа, используя команду [az aks get-credentials.][az-aks-get-credentials] В одном из следующих шагов вы получаете регулярные учетные данные кластера *пользователей,* чтобы увидеть поток аутентификации Azure AD в действии.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Создание привязки RBAC

Прежде чем использовать учетную запись Azure Active Directory с кластером AKS, необходимо создать привязку роли или привязку роли кластера. *Роли* определяют разрешения для предоставления, а *привязки* применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Получите главное имя пользователя (UPN) для пользователя, в настоящее время вошел в систему с помощью команды [шоу-шоу аз-рекламы.][az-ad-signed-in-user-show] Эта учетная запись пользователя включена для интеграции Azure AD на следующем этапе.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Если пользователь, для которого вы предоставляете привязку RBAC, находится в том же арендаторе Azure AD, назначайте разрешения на основе *пользователяPrincipalName*. Если пользователь находится в другом арендаторе Azure AD, запросите свойство *objectId* и вместо него используйте его.

Создайте манифест YAML, названный `basic-azure-ad-binding.yaml` и вставьте следующее содержимое. На последней строке замените *userPrincipalName_or_objectId* выходом UPN или идентификатора объекта из предыдущей команды:

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

Создайте ClusterRoleBinding с помощью команды [kubectl применить][kubectl-apply] и указать имя файла вашего манифеста YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Доступ к кластеру с помощью Azure AD

Теперь давайте проверим интеграцию аутентификации Azure AD для кластера AKS. Установите `kubectl` контекст конфигурации для использования регулярных учетных данных пользователей. Этот контекст передает все запросы аутентификации обратно через Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Теперь используйте [команду kubectl, чтобы][kubectl-get] просматривать стручки во всех пространствах имен:

```console
kubectl get pods --all-namespaces
```

Вы получаете знак оперативной проверки подлинности с помощью учетных данных Azure AD с помощью веб-браузера. После успешной аутентификации `kubectl` команда отображает стручки в кластере AKS, как показано на следующем выходе примера:

```console
kubectl get pods --all-namespaces
```

```output
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

Полученный для маркер `kubectl` аутентификации кэшируется. Вы только повторно войти в систему, когда маркер истек или конфигурационный файл Kubernetes воссоздан.

Если вы видите сообщение об ошибке авторизации после успешного вхложусь в веб-браузер, как в следующем выводе примера, проверьте следующие возможные проблемы:

```output
error: You must be logged in to the server (Unauthorized)
```

* Вы определили соответствующий идентификатор объекта или UPN, в зависимости от того, находится ли учетная запись пользователя в том же арендаторе Azure AD или нет.
* Пользователь не может быть членом более чем 200 групп.
* Секрет, определенный в регистрации приложения для сервера, соответствует значению, настроенной с помощью`--aad-server-app-secret`

## <a name="next-steps"></a>Дальнейшие действия

Для полного скрипта, содержащего команды, указанные в этой статье, см. [сценарий интеграции Azure AD в репо образцов AKS.][complete-script]

Чтобы использовать пользователей и группы Azure AD для управления доступом к кластерным ресурсам, см. [Контроль доступа к кластерным ресурсам с помощью элемента управления доступом на основе ролей и идентификационных данных Azure AD в AKS.][azure-ad-rbac]

Для получения дополнительной информации о том, как обеспечить безопасность кластеров Kubernetes, [см.][rbac-authorization]

Для наилучшей практики по [Best practices for authentication and authorization in AKS][operator-best-practices-identity]идентификации и управлению ресурсами см.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
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
