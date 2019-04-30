---
title: Задачи, выполняемые после развертывания OpenShift в Azure | Документация Майкрософт
description: Дополнительные задачи, которые необходимо выполнить после развертывания кластера OpenShift.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: fba29cd55f2d765faa107de3a8961032ef44deec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771369"
---
# <a name="post-deployment-tasks"></a>Задачи, выполняемые после развертывания

После развертывания кластера OpenShift можно настроить дополнительные элементы. В этой статье рассматриваются следующие действия:

- Настройка единого входа с использованием Azure Active Directory (Azure AD).
- Как настроить журналы Azure Monitor для мониторинга OpenShift
- Настройка метрик и ведения журнала.
- Установка открытого компонента Service Broker для Azure (OSBA).

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Настройка единого входа с использованием Azure Active Directory

Чтобы использовать Azure Active Directory для аутентификации, прежде всего нужно зарегистрировать приложение в Azure AD. Этот процесс состоит из двух этапов: регистрация приложения и настройка разрешений.

### <a name="create-an-app-registration"></a>Регистрация приложения

В этом примере для регистрации приложения используется Azure CLI, а для настройки разрешений — графический пользовательский интерфейс (портал). Чтобы зарегистрировать приложение, нужно знать следующие сведения для пяти параметров:

- Отображаемое имя: Имя регистрации приложения (например, OCPAzureAD)
- Главная страница: URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Идентификатор URI: URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URL-адрес ответа: Главный общедоступный URL-адрес и имя регистрации приложения (например, https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Пароль: Защищенный пароль (используйте надежный пароль)

В следующем примере регистрируется приложение на основе приведенных выше сведений.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Если команда будет выполнена успешно, вы получите такие выходные данные в формате JSON:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Запишите свойство appId, возвращенное из команды. Оно понадобится в дальнейшем.

На портале Azure выполните следующие действия.

1. Выберите **Azure Active Directory** > **App Registration** (Регистрация приложений).
2. Найдите зарегистрированное приложение (например, OCPAzureAD).
3. В результатах поиска щелкните нужное зарегистрированное приложение.
4. В области **Параметры** щелкните **Требуемые разрешения**.
5. В области **Необходимые разрешения** щелкните **Добавить**.

   ![Регистрация приложения](media/openshift-post-deployment/app-registration.png)

6. Щелкните шаг 1: Выберите API и нажмите кнопку **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Щелкните **Выбрать** внизу.

   ![Регистрация приложения. Выбор API](media/openshift-post-deployment/app-registration-select-api.png)

7. На шаг 2. Выбор разрешений **вход и чтение профиля пользователя** под **делегированные разрешения**, а затем нажмите кнопку **выберите**.

   ![Доступ к регистрации приложения](media/openshift-post-deployment/app-registration-access.png)

8. Нажмите кнопку **Готово**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Настройка OpenShift для аутентификации Azure AD

Чтобы OpenShift использовал Azure AD в качестве поставщика проверки подлинности, внесите изменения в файл /etc/origin/master/master-config.yaml на всех главных узлах.

Определите идентификатор клиента, выполнив следующую команду интерфейса командной строки:

```azurecli
az account show
```

В файле yaml найдите следующие строки:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Сразу после указанных выше строк вставьте следующие строки:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Убедитесь, что текст в разделе identityProviders выравнивается правильно. Определите идентификатор клиента, выполнив следующую команду интерфейса командной строки: ```az account show```

Перезапустите основные службы OpenShift на всех главных узлах.

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

В консоли OpenShift предложены два варианта аутентификации: htpasswd_auth и [Регистрация приложения].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Мониторинг OpenShift с помощью журналов Azure Monitor

Существует три способа добавить агент Log Analytics в OpenShift:
- установить агент Log Analytics для Linux непосредственно на каждом узле OpenShift;
- Включение расширения виртуальной Машины Azure Monitor на каждом узле OpenShift
- Установка агента Log Analytics как OpenShift набора daemon-set

Полностью прочитать [инструкции](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) для получения дополнительных сведений.

## <a name="configure-metrics-and-logging"></a>Настройка метрик и ведения журнала

В зависимости от используемой ветви, шаблоны Azure Resource Manager для платформы контейнеров OpenShift и OKD могут поддерживать входные параметры для включения метрик и ведения журнала в процессе установки.

Предложение платформы контейнеров OpenShift в Marketplace предоставляет также возможность включить метрики и ведение журнала во время установки кластера.

Если метрики и (или) ведение журнала не были включены во время установки кластера, их можно легко включить позже.

### <a name="azure-cloud-provider-in-use"></a>Использование поставщика облачных служб Azure

Создайте SSH-подключение к узлу-бастиону или первому главному узлу (в зависимости от шаблона и ветви, которые вы используете) и введите учетные данные, которые вы предоставили во время развертывания. Введите следующую команду:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Поставщик облачных служб Azure не используется

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Установите Open Service Broker для Azure (OSBA)

Open Service Broker для Azure (OSBA) позволяет подготавливать облачные службы Azure непосредственно на платформе OpenShift. OSBA предоставляется в реализации API Open Service Broker для Azure. Спецификация API Open Service Broker определяет общий язык для облачных поставщиков, который позволяет управлять облачными службами в облачных приложениях без проблем с блокировками.

Чтобы установить OSBA на OpenShift, следуйте представленные здесь инструкции: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Выполните только действия, описанные в разделе шаблона проекта OpenShift и не весь раздел Установка.

## <a name="next-steps"></a>Дальнейшие действия

- [Overview](https://docs.openshift.com/container-platform) (Обзор)
