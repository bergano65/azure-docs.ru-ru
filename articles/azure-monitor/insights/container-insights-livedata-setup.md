---
title: Настройка azure Monitor для контейнеров Live Data (предварительный просмотр) Документы Майкрософт
description: В этой статье описывается, как настроить представление в реальном времени журналов контейнеров (stdout/stderr) и событий без использования kubectl с Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275377"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Как настроить функцию Live Data (предварительный просмотр)

Для просмотра Live Data (предварительного просмотра) с помощью Azure Monitor для контейнеров из кластеров Azure Kubernetes Service (AKS) необходимо настроить аутентификацию для предоставления разрешения на доступ к данным Kubernetes. Эта конфигурация безопасности позволяет в режиме реального времени получать доступ к вашим данным через API Kubernetes непосредственно на портале Azure.

Эта функция поддерживает следующие методы управления доступом к журналам, событиям и метрикам:

- AKS без включенной авторизации Kubernetes RBAC;
- AKS со включенной авторизацией Kubernetes RBAC;
    - AKS настроен с кластерным ** [связывающим кластером,контролирующим](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS включен с Azure Active Directory (AD) SAML на основе одноразового знака на

Эти инструкции требуют как административного доступа к кластеру Kubernetes, так и настройки для использования Active Directory (AD) для проверки подлинности пользователей, административного доступа к Azure AD.  

В этой статье объясняется, как настроить аутентификацию для управления доступом к функции Live Data (предварительный просмотр) из кластера:

- Контроль доступа на основе ролей (RBAC) позволил кластеру AKS
- Активный каталог Azure интегрировал кластер AKS. 

>[!NOTE]
>Кластеры AKS включены как [частные кластеры](https://azure.microsoft.com/updates/aks-private-cluster/) не поддерживаются с помощью этой функции. Эта функция опирается на прямой доступ к API Kubernetes через прокси-сервер из вашего браузера. Включение сетевой безопасности для блокировки API Kubernetes из этого прокси будет блокировать этот трафик. 

>[!NOTE]
>Эта функция доступна во всех регионах Azure, включая Azure China. В настоящее время он недоступен в Azure правительства США.

## <a name="authentication-model"></a>Модель проверки подлинности

Функции Live Data (предварительный просмотр) используют API Kubernetes, идентичный инструменту `kubectl` командной строки. Конечные точки Kubernetes API используют сертификат, подписанный самостоятельно, который ваш браузер не сможет проверить. Эта функция использует внутренний прокси для проверки сертификата с помощью службы AKS, обеспечивая доверие трафика.

Портал Azure предлагает проверить учетные данные входа в кластер Active Directory Azure и перенаправить вас на настройку регистрации клиентов во время создания кластера (и перенастроенную в этой статье). Такое поведение похоже на процесс `kubectl`проверки подлинности, требуемый . 

>[!NOTE]
>Авторизация кластера управляется Kubernetes и моделью безопасности, с которой он настроен. Пользователям, обращающимся к этой функции, требуется разрешение на загрузку `az aks get-credentials -n {your cluster name} -g {your resource group}`конфигурации Kubernetes *(kubeconfig),* аналогичной запуску. Этот файл конфигурации содержит токен авторизации и проверки подлинности для **функциональной роли пользователя кластера Azure Kubernetes**в случае кластеров Azure RBAC с поддержкой AzBAC и AKS без включения rBAC. Он содержит информацию о Azure AD и регистрационных данных клиентов, когда AKS включен с Azure Active Directory (AD) SAML на основе одного знака.

>[!IMPORTANT]
>Пользователям этой функции требуется [роль пользователя кластера Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) в кластере, чтобы загрузить `kubeconfig` и использовать эту функцию. Пользователям **не** требуется доступ к кластеру для использования этой функции. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>Использование кластераMonitoringUser с кластерами с поддержкой RBAC

Чтобы устранить необходимость применения дополнительных изменений конфигурации, чтобы позволить пользователю Kubernetes роль связывания **кластераПользователь** ский доступ к функции Live Data (предварительный просмотр) после [включения rBAC](#configure-kubernetes-rbac-authorization) авторизации, AKS добавил новый кластер Kubernetes роль связывания называется **clusterMonitoringUser**. Этот привязка роли кластера имеет все необходимые разрешения вне коробки для доступа к API Kubernetes и конечные точки для использования функции Live Data (предварительный просмотр).

Для того, чтобы использовать функцию Live Data (предварительный просмотр) с этим новым пользователем, необходимо быть участником роли [участника](../../role-based-access-control/built-in-roles.md#contributor) на ресурсе кластера AKS. Azure Monitor для контейнеров, когда включен, настроен для проверки подлинности с помощью этого пользователя по умолчанию. Если привязка ролей clusterMonitoringUser не существует в кластере, **кластерный user** используется для проверки подлинности.

AKS выпустила эту новую роль привязки в январе 2020 года, так что кластеры, созданные до января 2020 года не имеют его. Если у вас есть кластер, созданный до января 2020 года, новый **кластерMonitoringUser** может быть добавлен в существующий кластер, выполняя операцию PUT на кластере или выполняя любую другую операцию на кластере tha выполняет операцию PUT на кластере, например, обновляя кластерную версию.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Кластер Kubernetes без включенного RBAC

Если для кластера Kubernetes не настроена авторизация Kubernetes RBAC или он не интегрирован с единым входом Azure AD, вам не нужно выполнять следующие действия. Это связано с тем, что у вас есть административные разрешения по умолчанию в конфигурации, не относяской к RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Настройка авторизации Kubernetes RBAC

При включании авторизации Kubernetes RBAC используются два пользователя: **clusterUser** и **clusterAdmin** для доступа к API Kubernetes. Это похоже на `az aks get-credentials -n {cluster_name} -g {rg_name}` запуск без административного варианта. Это означает, что **кластеру** должен быть предоставлен доступ к конечным точкам aPI Kubernetes.

Ниже приведены шаги по настройке привязки роли кластера на основе этого YAML-файла с шаблоном конфигурации.

1. Скопируйте и вставьте YAML-файл, а затем сохраните его как LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Чтобы обновить конфигурацию, запустите `kubectl apply -f LogReaderRBAC.yaml`следующую команду: .

>[!NOTE] 
> Если предыдущая версия `LogReaderRBAC.yaml` файла была применена к кластеру, обновите ее, скопив и вставьте новый код, показанный на шаге 1 выше, а затем запустите команду, показанную на шаг 2, чтобы применить ее к кластеру.

## <a name="configure-ad-integrated-authentication"></a>Настройка ат-интегрированной аутентификации 

Кластер AKS, настроенный на использование Active Directory (AD) Azure для проверки подлинности пользователей, использует учетные данные входа пользователя, обращающегося к этой функции. В этой конфигурации можно войти в кластер AKS, используя маркер проверки подлинности Azure AD.

Регистрация клиентов Azure AD должна быть перенастроена, чтобы позволить порталу Azure перенаправлять страницы авторизации в качестве надежного URL-адреса. Пользователи Azure AD получают доступ непосредственно к тем же конечным точкам Kubernetes API через **ClusterRoles** и **ClusterRoleBindings.** 

Для получения дополнительной информации о расширенной настройке безопасности в Kubernetes, просмотрите [документацию Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>При создании нового кластера с поддержкой RBAC см. [Интегрированный активный каталог Azure с службой Azure Kubernetes](../../aks/azure-ad-integration.md) и выполните последующие действия по настройке аутентификации Azure AD. Во время шагов по созданию клиентского приложения в заметке в этом разделе освещаются два перенаправления URL-адресов, которые необходимо создать для Azure Monitor для контейнеров, соответствующих тем, которые указаны в шаге 3 ниже.

### <a name="client-registration-reconfiguration"></a>Перенастройка регистрации клиентов

1. Найдите регистрацию клиента для кластера Kubernetes в Azure AD в рамках **Azure Active Directory > регистрации приложений** на портале Azure.

2. Выберите **аутентификацию** из левого стекла. 

3. Добавьте два перенаправления URL-адресов в этот список в виде типов **web-приложений.** Первое базовое значение `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL должно быть, `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`а второе базовое значение URL должно быть .

    >[!NOTE]
    >Если вы используете эту функцию в Azure China, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` первое базовое значение `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`URL должно быть, а второе базовое значение URL должно быть . 
    
4. После регистрации ренапрямых URL-адресов под **неявной субсидией**выберите **токены Access** и **id-kens,** а затем сохраните изменения.

>[!NOTE]
>Настройка аутентификации с помощью Active-каталога Azure для однозначных знаков может быть выполнена только при первоначальном развертывании нового кластера AKS. Настроить единый вход для кластера AKS, который уже развернут, невозможно.
  
>[!IMPORTANT]
>Если вы перенастроили Azure AD для проверки подлинности пользователя с помощью обновленного URI, очистите кэш браузера, чтобы обеспечить загрузку и применение обновленного маркера аутентификации.

## <a name="grant-permission"></a>Предоставление разрешения

Каждая учетная запись Azure AD должна получить разрешение на соответствующие AIS в Kubernetes для доступа к функции Live Data (предварительный просмотр). Шаги по предоставлению учетной записи Active Directory Azure аналогичны шагам, описанным в разделе [аутентификации Kubernetes RBAC.](#configure-kubernetes-rbac-authorization) Прежде чем применять шаблон конфигурации yaml к кластеру, **замените clusterUser** под **ClusterRoleBinding** на нужного пользователя. 

>[!IMPORTANT]
>Если пользователь, для которого вы предоставляете привязку RBAC, находится в том же арендаторе Azure AD, назначайте разрешения на основе пользователяPrincipalName. Если пользователь находится в другом арендаторе Azure AD, запрос и использование свойства objectId.

Для дополнительной помощи в настройке кластера AKS **ClusterRoleBinding** [см.](../../aks/azure-ad-integration-cli.md#create-rbac-binding)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть настройка аутентификации, вы можете просматривать [метрики,](container-insights-livedata-metrics.md) [развертывания,](container-insights-livedata-deployments.md) [события и журналы](container-insights-livedata-overview.md) в режиме реального времени из вашего кластера.
