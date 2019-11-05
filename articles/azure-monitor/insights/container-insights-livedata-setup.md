---
title: Azure Monitor установки для динамических данных контейнеров (Предварительная версия) | Документация Майкрософт
description: В этой статье описывается, как настроить представление журналов контейнеров (stdout/stderr) и событий в режиме реального времени без использования kubectl с Azure Monitor для контейнеров.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2019
ms.author: magoedte
ms.openlocfilehash: 596c5ad378d471c6c98616a48f44e96c365ee0bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514372"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Настройка функции "Интерактивные данные (Предварительная версия)"

Чтобы просмотреть данные (Предварительная версия) с Azure Monitor для контейнеров из кластеров Azure Kubernetes Service (AKS), необходимо настроить проверку подлинности, чтобы предоставить разрешение на доступ к данным Kubernetes. Эта конфигурация безопасности обеспечивает доступ к данным в режиме реального времени через API Kubernetes непосредственно в портал Azure.

Эта функция поддерживает три различных метода управления доступом к журналам, событиям и метрикам.

- AKS без включенной авторизации Kubernetes RBAC;
- AKS со включенной авторизацией Kubernetes RBAC;
- AKS с поддержкой единого входа Azure Active Directory (AD) на основе SAML

Для этих инструкций требуется административный доступ к кластеру Kubernetes, а при настройке на использование Azure Active Directory (AD) для проверки подлинности пользователей — административный доступ к Azure AD.  

В этой статье объясняется, как настроить проверку подлинности для управления доступом к функции "Интерактивные данные (Предварительная версия)" из кластера.

- Кластер AKS с включенным управлением доступом на основе ролей (RBAC)
- Azure Active Directory интегрированный кластер AKS. 

>[!NOTE]
>Кластеры AKS, включенные в качестве [частных кластеров](https://azure.microsoft.com/updates/aks-private-cluster/) , не поддерживаются этой функцией. Эта функция зависит от прямого доступа к API Kubernetes через прокси-сервер из браузера. Включение сетевой безопасности для блокировки интерфейса API Kubernetes от этого прокси-сервера приведет к блокировке этого трафика. 

>[!NOTE]
>Эта функция доступна во всех регионах Azure, включая Azure для Китая. Сейчас она недоступна в Azure для государственных организаций США.

## <a name="authentication-model"></a>Модель проверки подлинности

Функции интерактивных данных (Предварительная версия) используют API Kubernetes, идентичный средству командной строки `kubectl`. Конечные точки API Kubernetes используют самозаверяющий сертификат, который браузер не сможет проверить. Эта функция использует внутренний прокси-сервер для проверки сертификата со службой AKS, гарантируя, что трафик является доверенным.

Портал Azure предложит проверить учетные данные входа для Azure Active Directory кластера и перенаправить вас на настройку регистрации клиента во время создания кластера (и повторно настроить в этой статье). Такое поведение аналогично процессу проверки подлинности, требуемому для `kubectl`. 

>[!NOTE]
>Авторизация в кластере управляется Kubernetes и моделью безопасности, с помощью которой она настроена. Пользователям, обращающимся к этой функции, требуется разрешение на скачивание конфигурации Kubernetes (*kubeconfig*), аналогично выполнению `az aks get-credentials -n {your cluster name} -g {your resource group}`. Этот файл конфигурации содержит маркер авторизации и проверки подлинности для **роли пользователя кластера Azure Kubernetes Service**в случае кластеров с поддержкой RBAC и AKS без разрешения RBAC. Он содержит сведения об Azure AD и сведения о регистрации клиента, когда AKS включается с помощью единого входа на основе SAML Azure Active Directory (AD).

>[!IMPORTANT]
>Пользователям этих функций требуется [роль пользователя кластера Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) в кластере, чтобы скачать `kubeconfig` и использовать эту функцию. Пользователям **не** требуется доступ участника к кластеру для использования этой функции. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Кластер Kubernetes без включенного RBAC

Если для кластера Kubernetes не настроена авторизация Kubernetes RBAC или он не интегрирован с единым входом Azure AD, вам не нужно выполнять следующие действия. Это связано с тем, что по умолчанию у вас есть административные разрешения в конфигурации без RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Настройка проверки подлинности Kubernetes RBAC

При включении авторизации Kubernetes RBAC используются два пользователя: **клустерусер** и **клустерадмин** для доступа к API Kubernetes. Это похоже на выполнение `az aks get-credentials -n {cluster_name} -g {rg_name}` без административного параметра. Это означает, что **клустерусер** должен предоставить доступ к конечным точкам в API Kubernetes.

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

2. Чтобы обновить конфигурацию, выполните следующую команду: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Если вы применили предыдущую версию файла `LogReaderRBAC.yaml` к кластеру, обновите ее, скопировав и вставляя новый код, показанный на шаге 1 выше, а затем выполните команду, показанную на шаге 2, чтобы применить ее к кластеру.

## <a name="configure-ad-integrated-authentication"></a>Настройка встроенной проверки подлинности AD 

В кластере AKS, настроенном для использования Azure Active Directory (AD) для проверки подлинности пользователей, используются учетные данные для входа пользователя, обращающегося к этой функции. В этой конфигурации вы можете войти в кластер AKS с помощью токена проверки подлинности Azure AD.

Чтобы разрешить портал Azure перенаправить страницы авторизации в качестве доверенного URL-адреса перенаправления, необходимо перенастроить регистрацию клиента Azure AD. Пользователи из Azure AD получают доступ непосредственно к тем же конечным точкам API Kubernetes через **клустерролес** и **клустерролебиндингс**. 

Дополнительные сведения о настройке расширенной безопасности в Kubernetes см. в [документации по Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Если вы создаете новый кластер с поддержкой RBAC, см. статью [интеграция Azure Active Directory со службой Kubernetes Azure](../../aks/azure-ad-integration.md) и выполните действия по настройке проверки подлинности Azure AD. В ходе действий по созданию клиентского приложения в этом разделе будут показаны два URL-адреса перенаправления, которые необходимо создать для Azure Monitor для контейнеров. 

### <a name="client-registration-reconfiguration"></a>Перенастройка регистрации клиента

1. Выберите регистрацию клиента для кластера Kubernetes в Azure AD в разделе **Azure Active Directory > Регистрация приложений** в портал Azure.

2. На панели слева выберите **Проверка подлинности** . 

3. Добавьте два URL-адреса перенаправления в этот список в качестве типов **веб-** приложений. Первое значение базового URL-адреса должно быть `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, а второе значение базового URL-адреса должно быть `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Если вы используете эту функцию в Azure для Китая, то первым базовым URL-адресом должно быть `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, а второе значение базового URL-адреса должно быть `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. После регистрации URL-адресов перенаправления в разделе **Дополнительные параметры**выберите параметры **маркеры доступа** и **маркеры идентификации** , а затем сохраните изменения.

>[!NOTE]
>Настройка проверки подлинности с помощью Azure Active Directory для единого входа может быть выполнена только во время первоначального развертывания нового кластера AKS. Настроить единый вход для кластера AKS, который уже развернут, невозможно.
  
>[!IMPORTANT]
>Если вы настроили Azure AD для проверки подлинности пользователя с помощью обновленного URI, очистите кэш браузера, чтобы убедиться, что обновленный маркер проверки подлинности скачан и применен.

## <a name="grant-permission"></a>Предоставление разрешения

Каждой учетной записи Azure AD должно быть предоставлено разрешение на доступ к соответствующим API-интерфейсам в Kubernetes для доступа к функции интерактивных данных (Предварительная версия). Действия по предоставлению учетной записи Azure Active Directory похожи на шаги, описанные в разделе [Проверка подлинности KUBERNETES RBAC](#configure-kubernetes-rbac-authentication) . Перед применением шаблона конфигурации YAML к кластеру замените **клустерусер** в **клустерролебиндинг** на нужного пользователя. 

>[!IMPORTANT]
>Если пользователь, которому вы предоставляете привязку RBAC для, находится в том же клиенте Azure AD, назначьте разрешения на основе userPrincipalName. Если пользователь находится в другом клиенте Azure AD, запросите и используйте свойство objectId.

Дополнительные сведения о настройке **клустерролебиндинг**кластера AKS см. в разделе [Создание привязки RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили проверку подлинности, вы можете просматривать в кластере [метрики](container-insights-livedata-metrics.md), [развертывания](container-insights-livedata-deployments.md), [события и журналы](container-insights-livedata-overview.md) в реальном времени.