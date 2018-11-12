---
title: Задачи, выполняемые после развертывания OpenShift в Azure | Документация Майкрософт
description: Дополнительные задачи, которые необходимо выполнить после развертывания кластера OpenShift.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 8436b530ac01f03e071604e5023b50f8de6989fd
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034813"
---
# <a name="post-deployment-tasks"></a>Задачи, выполняемые после развертывания

После развертывания кластера OpenShift можно настроить дополнительные элементы. В этой статье рассматриваются следующие вопросы:

- Настройка единого входа с использованием Azure Active Directory (Azure AD).
- Как настроить мониторинг OpenShift с помощью Log Analytics
- Настройка метрик и ведения журнала.
- Установка открытого компонента Service Broker для Azure (OSBA).

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Настройка единого входа с использованием Azure Active Directory

Чтобы использовать Azure Active Directory для аутентификации, прежде всего нужно зарегистрировать приложение в Azure AD. Этот процесс состоит из двух этапов: регистрация приложения и настройка разрешений.

### <a name="create-an-app-registration"></a>Регистрация приложения

В этом примере для регистрации приложения используется Azure CLI, а для настройки разрешений — графический пользовательский интерфейс (портал). Чтобы зарегистрировать приложение, нужно знать следующие сведения для пяти параметров:

- Отображаемое имя. Имя регистрируемого приложения (например, OCPAzureAD).
- Домашняя страница. URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URI. URL-адрес консоли OpenShift (например, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URL-адрес ответа. Главный общедоступный URL-адрес и имя регистрации приложения (например, https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Пароль. Защищенный пароль (используйте надежный пароль).

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

1.  Выберите **Azure Active Directory** > **App Registration** (Регистрация приложений).
2.  Найдите зарегистрированное приложение (например, OCPAzureAD).
3.  В результатах поиска щелкните нужное зарегистрированное приложение.
4.  В области **Параметры** щелкните **Требуемые разрешения**.
5.  В области **Необходимые разрешения** щелкните **Добавить**.

  ![Регистрация приложения](media/openshift-post-deployment/app-registration.png)

6.  Щелкните "Шаг 1. Выбрать API" и выберите **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Щелкните **Выбрать** внизу.

  ![Регистрация приложения. Выбор API](media/openshift-post-deployment/app-registration-select-api.png)

7.  В области "Шаг 2. Выбор разрешений" выберите **Вход и чтение профиля пользователя** в разделе **Делегированные разрешения**, затем щелкните **Выбрать**.

  ![Доступ к регистрации приложения](media/openshift-post-deployment/app-registration-access.png)

8.  Нажмите кнопку **Готово**.

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

**Платформа контейнеров OpenShift (OCP) с несколькими главными узлами**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Платформа контейнеров OpenShift с одним главным узлом**

```bash
sudo systemctl restart atomic-openshift-master
```

**OKD с несколькими главными узлами**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OKD с одним главным узлом**

```bash
sudo systemctl restart origin-master
```

В консоли OpenShift предложены два варианта аутентификации: htpasswd_auth и [Регистрация приложения].

## <a name="monitor-openshift-with-log-analytics"></a>Мониторинг OpenShift с помощью Log Analytics

Существует три способа добавить агент Log Analytics в OpenShift:
- установить агент Log Analytics для Linux непосредственно на каждом узле OpenShift;
- включить расширение виртуальной машины Log Analytics на каждом узле OpenShift;
- установить агент Log Analytics как набор daemon-set для OpenShift.

Полные инструкции можно найти здесь: https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift.

## <a name="configure-metrics-and-logging"></a>Настройка метрик и ведения журнала

В зависимости от используемой ветви, шаблоны Azure Resource Manager для платформы контейнеров OpenShift и OKD могут поддерживать входные параметры для включения метрик и ведения журнала в процессе установки.

Предложение платформы контейнеров OpenShift в Marketplace предоставляет также возможность включить метрики и ведение журнала во время установки кластера.

Если метрики и (или) ведение журнала не были включены во время установки кластера, их можно легко включить позже.

### <a name="ansible-inventory-pre-work"></a>Предварительные действия для инвентаризации Ansible

Убедитесь, что файл инвентаризации Ansible (/etc/ansible/hosts) имеет все необходимые переменные для метрик и (или) ведения журнала. Файл инвентаризации размещается на разных узлах в зависимости от используемого шаблона.

Шаблон контейнера OpenShift и предложение Marketplace помещают файл инвентаризации на узел-бастион. Шаблон OKD помещает файл инвентаризации на узел master-0 или на узел-бастион в зависимости от используемой ветви.

1. Откройте для редактирования файл /etc/ansible/hosts и добавьте в него следующие строки после раздела поставщика удостоверений (# Enable HTPasswdPasswordIdentityProvider). Если эти строки уже присутствуют в файле, не следует добавлять их снова.

   OpenShift и OKD 3.9 и более ранних версий

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

   OpenShift и OKD 3.10 и более поздних версий

   ```yaml
   # Setup metrics
   openshift_metrics_install_metrics=false
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}

   # Setup logging
   openshift_logging_install_logging=false
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_kibana_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_curator_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_master_public_url=https://kibana.$ROUTING
   ```

3. Замените $ROUTING строкой, которая указана в качестве значения для openshift_master_default_subdomain в том же файле /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Использование поставщика облачных служб Azure

Создайте SSH-подключение к узлу-бастиону или первому главному узлу (в зависимости от шаблона и ветви, которые вы используете) и введите учетные данные, которые вы предоставили во время развертывания. Введите следующую команду:

**Платформа контейнеров OpenShift 3.7 и более ранних версий**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**Платформа контейнеров OpenShift 3.9 и более поздних версий**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

**OKD 3.7 и более ранних версий**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OKD 3.9 и более поздних версий**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Поставщик облачных служб Azure не используется

Создайте SSH-подключение к узлу-бастиону или первому главному узлу (в зависимости от шаблона и ветви, которые вы используете) и введите учетные данные, которые вы предоставили во время развертывания. Введите следующую команду:


**Платформа контейнеров OpenShift 3.7 и более ранних версий**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**Платформа контейнеров OpenShift 3.9 и более поздних версий**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.7 и более ранних версий**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.9 и более поздних версий**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True
ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Установите Open Service Broker для Azure (OSBA)

Open Service Broker для Azure (OSBA) позволяет подготавливать облачные службы Azure непосредственно на платформе OpenShift. OSBA предоставляется в реализации API Open Service Broker для Azure. Спецификация API Open Service Broker определяет общий язык для облачных поставщиков, который позволяет управлять облачными службами в облачных приложениях без проблем с блокировками.

Чтобы установить OSBA на OpenShift, следуйте представленные здесь инструкции: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 

## <a name="next-steps"></a>Дополнительная информация

- [Overview](https://docs.openshift.com/container-platform) (Обзор)
- [Начало работы с OKD](https://docs.okd.io/latest)
