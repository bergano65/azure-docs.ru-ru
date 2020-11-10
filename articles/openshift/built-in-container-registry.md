---
title: Настройка встроенного реестра контейнеров для Azure Red Hat OpenShift 4
description: Настройка встроенного реестра контейнеров для Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414801"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Настройка встроенного реестра контейнеров для Azure Red Hat OpenShift 4

В этой статье вы настроите встроенный реестр образов контейнера для кластера Azure Red Hat OpenShift (АТО) 4. Вы узнаете, как:

> [!div class="checklist"]
> * Настройка Azure AD
> * Настройка подключения OpenID Connect
> * Доступ к встроенному реестру образов контейнеров

## <a name="prerequisites"></a>Обязательные условия

* Создайте кластер, выполнив действия, описанные в разделе [Создание кластера Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Не забудьте создать кластер с `--pull-secret` аргументом для `az aro create` .  Это необходимо, если вы хотите настроить Azure AD для входа, как это необходимо в этой статье.
* Подключитесь к кластеру, выполнив действия, описанные в подразделе [Подключение к кластеру Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Обязательно выполните действия, описанные в разделе "Установка OpenShift CLI", так как мы будем использовать эту `oc` команду Далее в этой статье.
   * Запишите URL-адрес консоли кластера, который выглядит так `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` :. Значения для `<random>` и `<region>` будут использоваться далее в этой статье.
   * Запишите `kubeadmin` учетные данные. Они будут использоваться далее в этой статье.

## <a name="set-up-azure-active-directory"></a>Настройка Azure Active Directory

Azure Active Directory (Azure AD) реализует OpenID Connect Connect (OIDC). OIDC позволяет использовать Azure AD для входа в кластер АТО. Выполните следующие действия, чтобы настроить Azure AD.

1. Настройте клиент Azure AD, выполнив действия, описанные в разделе [Краткое руководство по настройке клиента](/azure/active-directory/develop/quickstart-create-new-tenant). Возможно, у вашей учетной записи Azure уже есть клиент. Если это так, можно пропустить создание одного из них, если у вас есть необходимые привилегии в клиенте для выполнения этих действий. Запишите **идентификатор клиента**. Это значение будет использоваться позже.
2. Создайте по крайней мере одного пользователя Azure AD, выполнив действия, описанные в разделе [Добавление или удаление пользователей с помощью Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Вы можете использовать эти или собственные учетные записи для тестирования приложения. Запишите адреса электронной почты и пароли этих учетных записей для входа.
3. Создайте регистрацию приложения в клиенте Azure AD, выполнив действия, описанные в разделе [Краткое руководство по регистрации приложения на платформе Microsoft Identity](/azure/active-directory/develop/quickstart-register-app). 
   1. Припомните Примечание в предварительных требованиях к значениям для `<random>` и `<region>` . Используйте эти значения для создания URI в соответствии со следующей формулой:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Если вы достигли шага, посвященного полю **URI перенаправления** , введите URI, приведенный на предыдущем шаге.
   1. Выберите **Зарегистрировать**.
   1. На странице **Обзор** для приложения Обратите внимание на значение, показанное для **идентификатора приложения (клиента)** , как показано здесь.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Страница обзора приложения Azure AD.":::

4. Создайте новый секрет клиента. 
   1. Во вновь созданной регистрации приложения выберите **сертификаты & секреты** в левой области навигации.
   1. Выберите **Создать секрет клиента**.
   1. Введите **Описание** и нажмите кнопку **Добавить**.
   1. Сохраняйте созданное **секретное значение клиента** . Это значение будет использоваться далее в этой статье.

### <a name="add-openid-connect-identity-provider"></a>Добавление поставщика удостоверений OpenID Connect Connect

АТО предоставляет встроенный реестр контейнеров.  Чтобы получить доступ к нему, настройте поставщик удостоверений (IDP) с помощью Azure AD OIDC, выполнив следующие действия.

1. Войдите в веб-консоль OpenShift в браузере как `kubeadmin` .  Это та же процедура, которая используется при выполнении действий, описанных в [руководстве по подключению к кластеру Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. В области навигации слева выберите **Администрирование**  >  **Параметры кластера**.
1. В середине страницы выберите **Глобальная конфигурация**.
1. Найдите элемент **OAuth** в столбце **ресурс конфигурации** таблицы и выберите его.
1. В разделе **поставщики удостоверений** щелкните **Добавить** и выберите **OpenID Connect подключить**.
1. Задайте **имя** как **OpenID Connect**.  Это значение уже может быть заполнено.
1. Задайте **идентификатор клиента** и **секрет клиента** в качестве значений, указанных на предыдущем шаге.
1. Выполните этот шаг, чтобы найти значение для **URL-адреса издателя**.
   1. Замените на тот **\<tenant-id>** , который был сохранен в разделе **Настройка Azure Active Directory** в URL-адресе `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Откройте URL-адрес в том же браузере, который использовался для взаимодействия с портал Azure.
   1. Скопируйте значение **Issuer** свойства в возвращенном тексте JSON и вставьте его в текстовое поле с меткой **URL-адрес издателя**.  Значение **издателя** будет выглядеть примерно так `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` :.
1. Перейдите в нижнюю часть страницы и нажмите кнопку **Добавить**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect Connect в OpenShift.":::
1. Выйдите из веб-консоли OpenShift, нажав кнопку **KUBE: Admin** в правом верхнем углу окна браузера и выбрав пункт **выход**.

### <a name="access-the-built-in-container-image-registry"></a>Доступ к встроенному реестру образов контейнеров

Следующие инструкции позволяют включить доступ к встроенному реестру.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Определите пользователя Azure AD в качестве администратора.

1. Войдите в веб-консоль OpenShift из браузера, используя учетные данные пользователя Azure AD.

   1. Используйте InPrivate, режиме инкогнито или другую эквивалентную функцию окна браузера для входа в консоль.
   1. После включения OIDC окно будет выглядеть иначе.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Окно входа с включенным OpenID Connect подключением.":::
   1. Выбор **OpenID Connect**

   > [!NOTE]
   > Запишите имя пользователя и пароль, которые вы используете для входа. Это имя пользователя и пароль будут работать как администратор для других действий в этой и других статьях.
1. Выполните вход с помощью интерфейса командной строки OpenShift, выполнив следующие действия.  Для обсуждения этот процесс называется `oc login` .
   1. В правой верхней части веб-консоли разверните контекстное меню пользователя, выполнившего вход, и выберите **команду Копировать имя входа**.
   1. При необходимости войдите в новое окно вкладки с тем же пользователем.
   1. Выберите **Показать маркер**.
   1. Скопируйте указанное ниже значение **Login с этим маркером** в буфер обмена и запустите его в оболочке, как показано ниже.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Запустите `oc whoami` в консоли и запишите выходные данные в виде **\<aad-user>** .  Это значение будет использоваться позже в этой статье.
1. Выйдите из веб-консоли OpenShift. Нажмите кнопку в правом верхнем углу окна браузера с меткой **\<aad-user>** и выберите **выход**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Предоставьте пользователю Azure AD необходимые роли для взаимодействия с реестром.

1. Войдите в веб-консоль OpenShift из браузера, используя `kubeadmin` учетные данные.
1. Войдите в OpenShift CLI с маркером для, выполнив `kubeadmin` действия, описанные `oc login` выше, но выполните их после входа в веб-консоль с помощью `kubeadmin` .
1. Выполните следующие команды, чтобы включить доступ к встроенному реестру для **AAD-User**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   Выходные данные должны выглядеть следующим образом.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   Выходные данные должны выглядеть следующим образом.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   Выходные данные должны выглядеть следующим образом.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   Выходные данные должны выглядеть следующим образом.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Получение URL-адреса реестра контейнеров

Используйте `oc get route` команду, как показано ниже, чтобы получить URL-адрес реестра контейнеров.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Обратите внимание на выходные данные консоли **URL-адреса реестра контейнеров**. Он будет использоваться в качестве полного имени реестра для данного руководства и последующих.

## <a name="next-steps"></a>Дальнейшие действия

Используйте встроенный реестр образов контейнеров, развернув приложение на OpenShift.  Для приложений Java следуйте инструкциям по [развертыванию приложения Java с помощью Open Liberty/WebSphere Liberty в кластере Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
