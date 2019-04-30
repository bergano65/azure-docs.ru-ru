---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Сведения о создании кластеров Службы Azure Kubernetes (AKS) с поддержкой Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: db92526bd02ba55be5df7ce6999e3099e72b8fa5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116784"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>rbИнтеграция Azure Active Directory со службой Azure Kubernetes

Службу Azure Kubernetes (AKS) можно настроить на использование Azure Active Directory (AD) для проверки подлинности пользователей. В этой конфигурации можно войдите в кластер AKS с помощью маркер проверки подлинности Azure Active Directory. Кроме того Администраторы кластера могут настроить управление доступом на основе ролей Kubernetes (RBAC), на основе членства в группе удостоверений или каталог пользователя.

В этой статье показано, как развернуть необходимые компоненты для AKS и Azure AD, а затем о развертывании кластера Azure с поддержкой AD и создать базовый роль RBAC в кластере AKS.

Действительны следующие ограничения.

- Azure Active Directory можно включить только при создании нового кластера с поддержкой RBAC. Вы не можете включить эту службу в существующем кластере AKS.
- *Гостевые* пользователи в Azure AD, например, при использовании федеративного входа из другого каталога, не поддерживаются.

## <a name="authentication-details"></a>Подробные сведения о проверке подлинности

Кластеры AKS проходят аутентификацию Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Дополнительные сведения об OpenID Connect см. в [Документации OpenID Connect][open-id-connect].

Внутри кластера Kubernetes проверка подлинности на основе маркера веб-перехватчика используется для проверки маркеров проверки подлинности. Настройка такой проверка подлинности и ее управление являются частью кластера AKS. Дополнительные сведения о проверке подлинности на основе маркера веб-перехватчика см. в [Документации по аутентификации веб-перехватчика][kubernetes-webhook].

> [!NOTE]
> При настройке Azure AD для проверки подлинности AKS настраиваются два приложения Azure AD. Эту операцию должен выполнять администратор клиента Azure.

## <a name="create-server-application"></a>Создание серверного приложения

Первое приложение Azure AD используется для получения членства в группе Azure AD.

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

   Присвойте имя приложению, в качестве типа приложения выберите **Веб-приложение или API**, а для параметра **URL-адрес для входа** введите любое значение в формате URI. После завершения работы выберите **Создать**.

   ![Создание регистрации в Azure AD](media/aad-integration/app-registration.png)

2. Выберите **Манифест** и в качестве значения `groupMembershipClaims` выберите `"All"`.

   Сохраните изменения.

   ![Обновление членства в группе — установка значения All](media/aad-integration/edit-manifest.png)

3. Вернитесь в приложение Azure AD, выберите **Параметры** > **Ключи**.

   Добавьте описание ключа, выберите срок истечения действия, а затем нажмите кнопку **Сохранить**. Запишите значение ключа. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Server application secret`.

   ![Получение закрытого ключа приложения](media/aad-integration/application-key.png)

4. Вернитесь в приложение Azure AD, выберите **Параметры** > **Необходимые разрешения** > **Добавить** > **Выбор API** > **Microsoft Graph** > **Выбрать**.

   ![Выбор API Graph](media/aad-integration/graph-api.png)

5. В разделе **Разрешения приложений** установите флажок рядом с параметром **Чтение данных каталога**.

   ![Установка разрешений Graph приложения](media/aad-integration/read-directory.png)

6. В разделе **Делегированные разрешения** установите флажки рядом с параметрами **Вход и чтение профиля пользователя** и **Чтение данных каталога**. Сохраните обновления после изменения.

   ![Установка разрешений Graph приложения](media/aad-integration/delegated-permissions.png)

   Нажмите кнопку **Готово**.

7. Выберите *Microsoft Graph* из списка API, а затем щелкните **Предоставление разрешений**. Этот шаг завершится ошибкой, если текущая учетная запись не является администратором клиента.

   ![Установка разрешений Graph приложения](media/aad-integration/grant-permissions.png)

   После успешного предоставления разрешения, на портале отображается следующее уведомление:

   ![Уведомление об успешном предоставлении разрешений](media/aad-integration/permissions-granted.png)

8. Вернитесь в приложение и запишите **идентификатор приложения**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Server application ID`.

   ![Получение идентификатора приложения](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Создание клиентского приложения

Второе приложение Azure AD используется при входе в систему с помощью интерфейса командной строки Kubernetes (kubectl).

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

   Присвойте имя приложению, в качестве типа приложения выберите **Машинный код**, а для параметра **URI перенаправления** введите любое значение в формате URI. После завершения работы выберите **Создать**.

   ![Создание регистрации в AAD](media/aad-integration/app-registration-client.png)

2. В приложении Azure AD выберите **Параметры** > **Необходимые разрешения** > **Добавить** > **Выбор API** и выполните поиск имени серверного приложения, созданного на последнем шаге в этом руководстве.

   ![Настройка разрешений для приложения](media/aad-integration/select-api.png)

3. Установите флажок рядом с приложением и щелкните **Выбрать**.

   ![Выбор конечной точки приложения сервера AAD в AKS](media/aad-integration/select-server-app.png)

   Щелкните **Готово**.

4. Выберите свой API сервера из списка, а затем щелкните **Предоставить разрешения**:

   ![Предоставление разрешений](media/aad-integration/grant-permissions-client.png)

5. Вернитесь в приложение AD и запишите **идентификатор приложения**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Client application ID`.

   ![Получение идентификатора приложения](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Получение идентификатора клиента

И наконец, получите идентификатор клиента Azure. Это значение также используется при развертывании кластера AKS.

На портале Azure выберите **Azure Active Directory** > **Свойства** и запишите значение **идентификатора каталога**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Tenant ID`.

![Получение идентификатора клиента Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Развертывание кластера

Чтобы создать группу ресурсов для кластера AKS, используйте команду [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Разверните кластер, выполнив команду [az aks create][az-aks-create]. Замените значения в примере команды ниже значениями, собранными при создании приложений Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Создание привязки RBAC

Прежде чем использовать учетную запись Azure Active Directory с кластером AKS, необходимо создать привязку роли или привязку роли кластера. *Роли* определяют разрешения для предоставления, а *привязки* применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Во-первых, используйте [az aks get-credentials] [ az-aks-get-credentials] с `--admin` аргумент для входа в кластер с административным доступом.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Создайте ClusterRoleBinding для учетной записи Azure AD, что вы хотите предоставить доступ к кластеру AKS. Следующий пример предоставляет полный доступ к учетной записи для всех пространств имен в кластере.

- Если пользователь является привязка RBAC для того же клиента Azure AD, то разрешения на основе имени субъекта-пользователя (UPN). Перейти к шагу для создания манифеста для ClusterRuleBinding YAML.

- Если пользователь находится в другой каталог Azure AD клиента, запрашивать и использовать *objectId* свойство вместо этого. При необходимости получить *objectId* требуемого пользователя учетной записи с помощью [show пользователя ad az] [ az-ad-user-show] команды. Укажите имя участника-пользователя (UPN) учетной записи, требуется:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Создайте файл, например *rbac-aad-user.yaml*, и вставьте в него приведенное ниже содержимое. В последней строке, замените *userPrincipalName_or_objectId* с Идентификатором имени участника-пользователя или объекта, в зависимости от того, если он в одном клиенте Azure AD или нет.

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

Примените привязку с помощью команды [kubectl apply][kubectl-apply], как показано в следующем примере:

```console
kubectl apply -f rbac-aad-user.yaml
```

Привязку роли также можно создать для всех членов группы Azure AD. Группы Azure AD указываются с помощью идентификатора объекта группы, как показано в приведенном ниже примере. Создайте файл, например *rbac-aad-group.yaml*, и вставьте в него приведенное ниже содержимое. Обновите идентификатор объекта группы с помощью одного из имен, которое используется в клиенте Azure AD:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Примените привязку с помощью команды [kubectl apply][kubectl-apply], как показано в следующем примере:

```console
kubectl apply -f rbac-aad-group.yaml
```

Дополнительные сведения о защите кластера Kubernetes с помощью ролей RBAC см. в документации об [использовании авторизации на основе ролей RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Доступ к кластеру с помощью Azure AD

Далее извлеките контекст для пользователя, не являющегося администратором, выполнив команду [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

После выполнения команды kubectl появится запрос на проверку подлинности в Azure. Выполните инструкции на экране.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

После завершения действий маркер проверки подлинности кэшируется. Кроме того, являются только reprompted для входа при истечении срока действия токена или повторно создать файл конфигурации Kubernetes.

Если после успешного входа появится сообщение об ошибке авторизации, убедитесь,
1. что пользователь, от имени которого был выполнен вход, не является гостем в экземпляре Azure AD (это часто происходит при использовании федеративного входа из другого каталога).
2. Пользователь не может быть членом более чем 200 групп.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы использовать Azure AD — пользователи и группы для управления доступом к ресурсам кластера, см. в разделе [управления доступом к ресурсам кластера, с помощью управления доступом и удостоверениями Azure AD в AKS][azure-ad-rbac].

Дополнительные сведения о защите кластеров Kubernetes см. в разделе [параметры доступа и удостоверений для AKS)][rbac-authorization].

Советы и рекомендации для управления удостоверениями и ресурсов, см. в разделе [советы и рекомендации для проверки подлинности и авторизации в AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
