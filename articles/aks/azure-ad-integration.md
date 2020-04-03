---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Как создать кластеры службы Azure Active Directory С поддержкой Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617596"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>rbИнтеграция Azure Active Directory со службой Azure Kubernetes

Служба Azure Kubernetes (AKS) может быть настроена на использование active Directory Azure (Azure AD) для проверки подлинности пользователей. В этой конфигурации можно войти в кластер AKS, используя маркер проверки подлинности Azure AD.

Администраторы кластеров могут настроить элемент управления доступом на основе ролей Kubernetes (RBAC) на основе идентификации пользователя или членства в группе каталогов.

В статье описывается выполнение следующих задач:

- Развертывание предпосылок для AKS и Azure AD.
- Развертывание кластера с поддержкой Azure AD.
- Создайте основную роль RBAC в кластере AKS с помощью портала Azure.

Вы также можете выполнить эти шаги с помощью [Azure CLI.][azure-ad-cli]

> [!NOTE]
> AD Azure может быть включен только при создании нового кластера с поддержкой RBAC. Вы не можете включить эту службу в существующем кластере AKS.

## <a name="authentication-details"></a>Подробные сведения о проверке подлинности

Аутентификация Azure AD предоставляется кластерам AKS с OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0.

Для получения дополнительной информации [Authorize access to web applications using OpenID Connect and Azure AD][open-id-connect]о OpenID Connect см.

Внутри кластера Kubernetes для проверки подлинности маркеров подлинности используется аутентификация веб-крючка. Настройка такой проверка подлинности и ее управление являются частью кластера AKS.

Для получения дополнительной информации об аутенкентации токенов Webhook можно узнать в разделе [«Проверка подлинности токенов Вебхука»][kubernetes-webhook] в документации Kubernetes.

Для обеспечения аутентификации Azure AD для кластера AKS создаются два приложения Azure AD. Первое приложение представляет собой компонент сервера, обеспечивающий аутентификацию пользователя. Второе приложение — это клиентский компонент, который используется при попросевом CLI для проверки подлинности. Это клиентское приложение использует серверное приложение для фактической проверки подлинности учетных данных, предоставленных клиентом.

> [!NOTE]
> При настройке Azure AD для проверки подлинности AKS настраиваются два приложения Azure AD. Шаги по делегированию разрешений для каждого приложения должны быть завершены администратором-арендатором Azure.

## <a name="create-the-server-application"></a>Создание серверного приложения

Первое приложение Azure AD применяется для получения членства в группе Azure AD пользователя. Чтобы создать это приложение на портале Azure:

1. Выберите **приложение Azure Active Directory** > **App регистрации** > **Новая регистрация**.

    а. Дайте приложению имя, например *AKSAzureADServer.*

    b. Для **типов поддерживаемых учетных записей**выберите **учетные записи только в этом каталоге организации.**
    
    c. Выберите **Web** для типа Redirect URI, а затем введите любое значение URI-формата, *https://aksazureadserver*например.

    d. Выберите **Регистрацию** по завершении.

2. Выберите **Манифест,** а затем отображайте **groupMembershipClaims:** значение как **все**. Когда вы закончите с обновлениями, выберите **Сохранить**.

    ![Обновление членства в группе — установка значения All](media/aad-integration/edit-manifest.png)

3. В левом стеле приложения Azure AD выберите **Сертификаты & секреты.**

    а. Выберите **- Новый секрет клиента**.

    b. Добавьте ключевое описание, например *сервер AKS Azure AD.* Выберите время истечения срока действия, а затем выберите **Добавить**.

    c. Обратите внимание на ключевое значение, которое отображается только в это время. При развертывании кластера AKS с поддержкой Azure AD это значение называется секретным серверным приложением.

4. В левом стеку приложения Azure AD выберите **разрешения API,** а затем выберите **и добавьте разрешение.**

    а. В **соответствии с AIS Майкрософт**выберите **график Microsoft.**

    b. Выберите **делегированные разрешения,** а затем выберите флажок рядом с **каталогом > directory.Read.All (Читайте данные каталога).**

    c. Если делегата по умолчанию для **пользователя > User.Read (зарегистрируйтесь и прочитайте профиль пользователя)** не существует, выберите флажок рядом с ним.

    d. Выберите **разрешения приложения,** а затем выберите флажок рядом с **каталогом > directory.Read.All (Читайте данные каталога).**

    ![Установка разрешений на график](media/aad-integration/graph-permissions.png)

    д) Выберите **разрешения добавить** для сохранения обновлений.

    е) При **согласии гранта,** выберите **согласие админа Гранта.** Эта кнопка не будет доступна, использоваваяся учетная запись не указана в качестве админа-клиента.

    При успешном предоставлении разрешений на портале отображается следующее уведомление:

   ![Уведомление об успешном предоставлении разрешений](media/aad-integration/permissions-granted.png)

5. В левом стеку приложения Azure AD выберите **Expose API,** а затем выберите **панель «Добавить область».**
    
    а. Введите **имя области,** **имя отображения согласия администратора,** а затем **описание согласия администратора,** такое как *AKSAzureADServer.*

    b. Убедитесь, что **состояние** настроено на **включено.**

    ![Разоблачить приложение сервера как API для использования с другими службами](media/aad-integration/expose-api.png)

    c. Выберите **область добавления.**

6. Вернитесь на страницу **обзора** приложения и обратите внимание на **идентификатор приложения (клиента).** При развертывании кластера AKS с поддержкой Azure AD это значение называется идентификатором приложения сервера.

    ![Получение идентификатора приложения](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Создание клиентского приложения

Второе приложение Azure AD используется при входе в kubernetes CLI (kubectl).

1. Выберите **приложение Azure Active Directory** > **App регистрации** > **Новая регистрация**.

    а. Дайте приложению имя, *например, AKSAzureADClient.*

    b. Для **типов поддерживаемых учетных записей**выберите **учетные записи только в этом каталоге организации.**

    c. Выберите **Web** для типа Redirect URI, а затем введите любое значение URI-формата, такое как. *https://aksazureadclient*

    >[!NOTE]
    >Если вы создаете новый кластер с поддержкой RBAC для поддержки Azure Monitor для контейнеров, добавьте следующие два дополнительных URL-адреса перенаправления в этот список в виде типов **web-приложений.** Первое базовое значение `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL должно быть, а второе базовое значение URL должно быть`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Если вы используете эту функцию в Azure China, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` первое базовое значение URL должно быть, а второе базовое значение URL должно быть`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Для получения дополнительной информации смотрите [как настроить функцию Live Data (предварительный просмотр)](../azure-monitor/insights/container-insights-livedata-setup.md) для контейнеров Azure Monitor и шаги по настройке аутентификации в разделе [интегрированной аутентификации Для настройки AD.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Выберите **Регистрацию** по завершении.

2. В левом стеку приложения Azure AD выберите **разрешения API,** а затем выберите **и добавьте разрешение.**

    а. Выберите **мои AA,** а затем выберите приложение сервера Azure AD, созданное на предыдущем этапе, например *AKSAzureADServer.*

    b. Выберите **делегированные разрешения,** а затем выберите флажок рядом с приложением Azure AD сервера.

    ![Настройка разрешений для приложения](media/aad-integration/select-api.png)

    c. Выберите **Добавить разрешения**.

    d. При **согласии гранта,** выберите **согласие админа Гранта.** Эта кнопка недоступна, если текущая учетная запись не является админ-клиентом-арендатором. При выдаче разрешений на портале отображается следующее уведомление:

    ![Уведомление об успешном предоставлении разрешений](media/aad-integration/permissions-granted.png)

3. В левом стеле приложения Azure AD выберите **аутентификацию.**

    - Под **типом клиента по умолчанию**выберите **«Да»** для **обращения с клиентом в качестве публичного клиента.**

5. В левом стеле приложения Azure AD обратите внимание на идентификатор приложения. При развертывании кластера AKS с поддержкой Azure AD это значение называется идентификатором клиентского приложения.

   ![Получение идентификатора приложения](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Получение идентификатора клиента

Затем получите идентификатор арендатора Azure. Это значение используется при создании кластера AKS.

На портале Azure выберите > **свойства** **активных каталогов Azure**и обратите внимание на **идентификатор каталога.** При создании кластера AKS с поддержкой Azure AD это значение называется идентификатором клиента.

![Получение идентификатора клиента Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Развертывание кластера AKS

Чтобы создать группу ресурсов для кластера AKS, используйте команду [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Используйте команду [создания az aks][az-aks-create] для развертывания кластера AKS. Затем замените значения в следующей команде образца. Используйте значения, собранные при создании приложений Azure AD для идентификатора приложения сервера, секрета приложения, идентификатора клиентского приложения и идентификатора клиента.

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

Создание кластера AKS занимает несколько минут.

## <a name="create-an-rbac-binding"></a>Создание привязки RBAC

> [!NOTE]
> Имя связывания роли кластера является чувствительным к случаю.

Перед использованием учетной записи Active Directory Azure с кластером AKS необходимо создать связывающую роль или кластерную привязку ролей. Роли определяют разрешения для предоставления, а привязки применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [Использовании авторизации RBAC][rbac-authorization].

Во-первых, используйте команду [az aks get-credentials][az-aks-get-credentials] с аргументом, `--admin` чтобы войти в кластер с доступом к админ.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Затем создайте clusterRoleBinding для учетной записи Azure AD, которую вы хотите предоставить доступ к кластеру AKS. Следующий пример дает учетную запись полный доступ ко всем пространствам имен в кластере:

- Если пользователь, для которого вы предоставляете привязку RBAC, находится в том же арендаторе Azure AD, назначайте разрешения на основе главного имени пользователя (UPN). Перейдите к шагу, чтобы создать манифест YAML для ClusterRoleBinding.

- Если пользователь находится в другом арендаторе Azure AD, запросите свойство **objectId** и вместо него используйте его. При необходимости получите objectId требуемой учетной записи пользователя с помощью команды [шоу-шоу пользователя az.][az-ad-user-show] Укажите основное имя пользователя (UPN) требуемой учетной записи:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Создайте файл, например *rbac-aad-user.yaml,* а затем вставьте следующее содержимое. На последней строке замените **userPrincipalName_or_objectId** идентификатором UPN или объекта. Выбор зависит от того, является ли пользователь тем же арендатором Azure AD или нет.

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

Примените привязку, используя команду [kubectl,][kubectl-apply] как показано в следующем примере:

```console
kubectl apply -f rbac-aad-user.yaml
```

Привязку роли также можно создать для всех членов группы Azure AD. Группы Azure AD определяются с помощью идентификатора группового объекта, как показано в следующем примере.

Создайте файл, например *rbac-aad-group.yaml,* а затем вставьте следующее содержимое. Обновите идентификатор объекта группы с помощью одного из имен, которое используется в клиенте Azure AD:

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

Примените привязку, используя команду [kubectl,][kubectl-apply] как показано в следующем примере:

```console
kubectl apply -f rbac-aad-group.yaml
```

Дополнительные сведения о защите кластера Kubernetes с помощью ролей RBAC см. в документации об [использовании авторизации на основе ролей RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Доступ к кластеру с Azure AD

Вытяните контекст для пользователя, не являщегося аудиационом, используя команду [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

После запуска `kubectl` команды вам будет предложено проверить подлинность с помощью Azure. Следуйте инструкциям на экране, чтобы закончить процесс, как показано в следующем примере:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Когда процесс завершен, токен проверки подлинности кэшируется. Вам предлагается войти снова, когда срок действия маркера истекает, или воссоздан файл конфигурации Kubernetes.

Если вы видите сообщение об ошибке авторизации после успешного вступления в систему, проверьте следующие критерии:

```console
error: You must be logged in to the server (Unauthorized)
```


- Вы определили соответствующий идентификатор объекта или UPN, в зависимости от того, находится ли учетная запись пользователя в том же арендаторе Azure AD или нет.
- Пользователь не является членом более чем 200 групп.
- Секрет, определенный в регистрации приложения для сервера, соответствует значению, настроенному с помощью `--aad-server-app-secret`.

## <a name="next-steps"></a>Следующие шаги

Чтобы использовать пользователей и группы Azure AD для управления доступом к кластерным ресурсам, см. [Контроль доступа к кластерным ресурсам с помощью элемента управления доступом на основе ролей и идентификационных данных Azure AD в AKS.][azure-ad-rbac]

Для получения дополнительной информации о том, как обеспечить безопасность кластеров Kubernetes, [см.][rbac-authorization]

Чтобы узнать больше о идентификационных данных и управлении ресурсами, [см.][operator-best-practices-identity]

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
