---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Создание кластеров Azure Kubernetes Service (AKS) с поддержкой Azure Active Directory
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596595"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>rbИнтеграция Azure Active Directory со службой Azure Kubernetes

Службу Kubernetes Azure (AKS) можно настроить для использования проверки подлинности пользователей Azure Active Directory (Azure AD). В этой конфигурации вы можете войти в кластер AKS с помощью токена проверки подлинности Azure AD.

Администраторы кластера могут настроить Kubernetes управления доступом на основе ролей (RBAC) на основе удостоверения пользователя или членства в группе каталогов.

В статье описывается выполнение следующих задач:

- Разверните необходимые компоненты для AKS и Azure AD.
- Развертывание кластера с поддержкой Azure AD.
- Создайте базовую роль RBAC в кластере AKS с помощью портал Azure.

Эти действия также можно выполнить с помощью [Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD можно включить только при создании нового кластера с поддержкой RBAC. Вы не можете включить эту службу в существующем кластере AKS.

## <a name="authentication-details"></a>Подробные сведения о проверке подлинности

Аутентификация Azure AD предоставляется для кластеров AKS, которые имеют OpenID Connect Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0.

Дополнительные сведения о OpenID Connect Connect см. [в разделе Авторизация доступа к веб-приложениям с помощью OpenID Connect Connect и Azure AD][open-id-connect].

В кластере Kubernetes проверка подлинности маркеров веб-перехватчика используется для маркеров проверки подлинности. Настройка такой проверка подлинности и ее управление являются частью кластера AKS.

Дополнительные сведения о проверке подлинности маркеров веб-перехватчика см. в разделе [Проверка подлинности маркеров веб-перехватчика][kubernetes-webhook] в документации по Kubernetes.

Чтобы обеспечить аутентификацию Azure AD для кластера AKS, создаются два приложения Azure AD. Первое приложение — это серверный компонент, обеспечивающий проверку подлинности пользователя. Второе приложение — это клиентский компонент, который используется при запросе CLI для проверки подлинности. Это клиентское приложение использует серверное приложение для фактической проверки подлинности учетных данных, предоставленных клиентом.

> [!NOTE]
> При настройке проверки подлинности Azure AD для AKS настраиваются два приложения Azure AD. Действия по делегированию разрешений для каждого приложения должны выполняться администратором клиента Azure.

## <a name="create-the-server-application"></a>Создание серверного приложения

Первое приложение Azure AD применяется для получения членства в группе Azure AD пользователя. Чтобы создать это приложение в портал Azure, сделайте следующее:

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.

    а. Присвойте приложению имя, например *аксазуреадсервер*.

    б. Для **поддерживаемых типов учетных записей**выберите **учетные записи только в этом каталоге Организации**.
    
    в. Выберите **Интернет** для типа URI перенаправления, а затем введите любое значение в формате URI, например *https://aksazureadserver* .

    . По завершении выберите **Регистрация** .

2. Выберите **Манифест**и измените значение **groupMembershipClaims:** **ALL**. Завершив работу с обновлениями, нажмите кнопку **сохранить**.

    ![Обновление членства в группе — установка значения All](media/aad-integration/edit-manifest.png)

3. В левой области приложения Azure AD выберите **сертификаты & секреты**.

    а. Выберите **+ новый секрет клиента**.

    б. Добавьте описание ключа, например *AKS Azure AD Server*. Выберите срок действия, а затем нажмите кнопку **Добавить**.

    в. Обратите внимание на значение ключа, которое отображается только в данный момент. При развертывании кластера AKS с поддержкой Azure AD это значение называется секретом серверного приложения.

4. В левой области приложения Azure AD выберите **разрешения API**и щелкните **+ Добавить разрешение**.

    а. В разделе **API-интерфейсы Майкрософт**выберите **Microsoft Graph**.

    б. Выберите **делегированные разрешения**и установите флажок для **каталога > каталог. чтение. все (чтение данных каталога)** .

    в. Если по умолчанию делегированное разрешение **пользователя > пользователя. Read (вход и чтение профиля пользователя)** не существует, установите флажок рядом с ним.

    . Выберите **разрешения приложения**и установите флажок для **каталога > Directory. Read. All (чтение данных каталога)** .

    ![Установка разрешений графа](media/aad-integration/graph-permissions.png)

    д) Выберите **Добавить разрешения** , чтобы сохранить обновления.

    е) В разделе **предоставление согласия**выберите **предоставить согласие администратора**. Эта кнопка будет недоступна. текущая используемая учетная запись не указана в качестве администратора клиента.

    При успешном предоставлении разрешений на портале отображается следующее уведомление:

   ![Уведомление об успешном предоставлении разрешений](media/aad-integration/permissions-granted.png)

5. В левой области приложения Azure AD выберите **предоставить API**, а затем выберите **+ Добавить область**.
    
    а. Введите **имя области**, **Отображаемое имя согласия администратора**, а затем **Описание согласия администратора** , например *аксазуреадсервер*.

    б. Убедитесь, что для параметра **состояние** установлено значение **включено**.

    ![Предоставление серверного приложения в качестве API-интерфейса для использования с другими службами](media/aad-integration/expose-api.png)

    в. Выберите **Добавить область**.

6. Вернитесь на страницу **обзора** приложения и запишите **идентификатор приложения (клиента)** . При развертывании кластера AKS с поддержкой Azure AD это значение называется ИДЕНТИФИКАТОРом серверного приложения.

    ![Получение идентификатора приложения](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Создание клиентского приложения

Второе приложение Azure AD используется при входе с помощью Kubernetes CLI (kubectl).

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.

    а. Присвойте приложению имя, например *аксазуреадклиент*.

    б. Для **поддерживаемых типов учетных записей**выберите **учетные записи только в этом каталоге Организации**.

    в. Выберите **Интернет** для типа URI перенаправления, а затем введите любое значение в формате URI, например *https://aksazureadclient* .

    >[!NOTE]
    >Если вы создаете новый кластер с поддержкой RBAC для поддержки Azure Monitor для контейнеров, добавьте следующие два URL-адреса перенаправления в этот список в качестве типов **веб-** приложений. Первое значение базового URL-адреса должно быть `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, а второе значение базового URL-адреса должно быть `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Если вы используете эту функцию в Azure для Китая, то первым базовым URL-адресом должно быть `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, а второе значение базового URL-адреса должно быть `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Дополнительные сведения см. в статье Настройка [функции "Интерактивные данные (Предварительная версия)"](../azure-monitor/insights/container-insights-livedata-setup.md) для Azure Monitor для контейнеров и инструкции по настройке проверки подлинности в разделе [Настройка встроенной проверки подлинности Active Directory](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) .

    . По завершении выберите **Регистрация** .

2. В левой области приложения Azure AD выберите **разрешения API**и щелкните **+ Добавить разрешение**.

    а. Выберите **Мои API**, а затем выберите приложение сервера Azure AD, созданное на предыдущем шаге, например *аксазуреадсервер*.

    б. Выберите **делегированные разрешения**, а затем установите флажок рядом с приложением сервера Azure AD.

    ![Настройка разрешений для приложения](media/aad-integration/select-api.png)

    в. Выберите **Добавить разрешения**.

    . В разделе **предоставление согласия**выберите **предоставить согласие администратора**. Эта кнопка недоступна, если текущая учетная запись не является администратором клиента. При предоставлении разрешений на портале отображается следующее уведомление:

    ![Уведомление об успешном предоставлении разрешений](media/aad-integration/permissions-granted.png)

3. В левой области приложения Azure AD выберите **Проверка подлинности**.

    - В разделе **Тип клиента по умолчанию**выберите **Да** , чтобы **считать клиент общедоступным клиентом**.

5. В левой области приложения Azure AD запишите идентификатор приложения. При развертывании кластера AKS с поддержкой Azure AD это значение называется ИДЕНТИФИКАТОРом клиентского приложения.

   ![Получение идентификатора приложения](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Получение идентификатора клиента

Затем получите идентификатор клиента Azure. Это значение используется при создании кластера AKS.

На портал Azure выберите **Azure Active Directory** > **Свойства** и запишите **идентификатор каталога**. При создании кластера AKS с поддержкой Azure AD это значение называется ИДЕНТИФИКАТОРом клиента.

![Получение идентификатора клиента Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Развертывание кластера AKS

Используйте команду [AZ Group Create][az-group-create] , чтобы создать группу ресурсов для кластера AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Для развертывания кластера AKS используйте команду [AZ AKS Create][az-aks-create] . Затем замените значения в приведенном ниже образце команды. Используйте значения, собранные при создании приложений Azure AD для идентификатора серверного приложения, секрета приложения, идентификатора клиентского приложения и идентификатора клиента.

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

Создание кластера AKS займет несколько минут.

## <a name="create-an-rbac-binding"></a>Создание привязки RBAC

> [!NOTE]
> Имя привязки роли кластера чувствительно к регистру.

Прежде чем использовать учетную запись Azure Active Directory с кластером AKS, необходимо создать привязку ролей или привязку ролей кластера. Роли определяют предоставляемые разрешения, а привязки применяют их к нужным пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. [в разделе Использование авторизации RBAC][rbac-authorization].

Сначала используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] с аргументом `--admin`, чтобы войти в кластер с правами администратора.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Затем создайте Клустерролебиндинг для учетной записи Azure AD, которой требуется предоставить доступ к кластеру AKS. В следующем примере учетной записи предоставляется полный доступ ко всем пространствам имен в кластере:

- Если пользователь, которому вы предоставляете привязку RBAC, находится в том же клиенте Azure AD, назначьте разрешения на основе имени участника-пользователя (UPN). Перейдите к шагу, чтобы создать манифест YAML для Клустерролебиндинг.

- Если пользователь находится в другом клиенте Azure AD, вместо него следует запрашивать и использовать свойство **ObjectID** . При необходимости получите идентификатор объекта требуемой учетной записи пользователя с помощью команды [AZ AD user демонстрация][az-ad-user-show] . Укажите имя участника-пользователя (UPN) требуемой учетной записи:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Создайте файл, например *RBAC-AAD-User. YAML*, и вставьте следующее содержимое. В последней строке замените **userPrincipalName_or_objectId** именем участника-пользователя или идентификатором объекта. Выбор зависит от того, является ли пользователь тем же клиентом Azure AD или нет.

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

Примените привязку с помощью команды [kubectl Apply][kubectl-apply] , как показано в следующем примере:

```console
kubectl apply -f rbac-aad-user.yaml
```

Привязку роли также можно создать для всех членов группы Azure AD. Группы Azure AD указываются с помощью идентификатора объекта группы, как показано в следующем примере.

Создайте файл, например *RBAC-AAD-Group. YAML*, и вставьте следующее содержимое. Обновите идентификатор объекта группы с помощью одного из имен, которое используется в клиенте Azure AD:

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

Примените привязку с помощью команды [kubectl Apply][kubectl-apply] , как показано в следующем примере:

```console
kubectl apply -f rbac-aad-group.yaml
```

Дополнительные сведения о защите кластера Kubernetes с RBAC см. в разделе [Использование авторизации RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Доступ к кластеру с помощью Azure AD

Вытяните контекст для пользователя без прав администратора с помощью команды [AZ AKS Get-Credential][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

После выполнения команды `kubectl` вам будет предложено выполнить аутентификацию с помощью Azure. Следуйте инструкциям на экране, чтобы завершить процесс, как показано в следующем примере:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

По завершении процесса маркер проверки подлинности кэшируется. При истечении срока действия маркера или повторном создании файла конфигурации Kubernetes выводится запрос на вход.

Если после успешного входа появится сообщение об ошибке авторизации, проверьте следующие критерии.

```console
error: You must be logged in to the server (Unauthorized)
```


- Вы определили соответствующий идентификатор объекта или имя участника-пользователя в зависимости от того, находится ли учетная запись пользователя в том же клиенте Azure AD или нет.
- Пользователь не является членом более чем 200 групп.
- Секрет, определенный в регистрации приложения для сервера, совпадает со значением, настроенным с помощью `--aad-server-app-secret`.

## <a name="next-steps"></a>Следующие шаги

Сведения об использовании пользователей и групп Azure AD для управления доступом к ресурсам кластера см. [в статье Управление доступом к ресурсам кластера с помощью управления доступом на основе ролей и удостоверений Azure AD в AKS][azure-ad-rbac].

Дополнительные сведения о защите кластеров Kubernetes см. в статье [параметры доступа и удостоверений для AKS][rbac-authorization].

Дополнительные сведения об управлении удостоверениями и ресурсами см. [в разделе рекомендации по проверке подлинности и авторизации в AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
