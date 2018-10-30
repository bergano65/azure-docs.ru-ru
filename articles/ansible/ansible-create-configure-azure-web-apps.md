---
title: Создание веб-приложений Azure с помощью Ansible (предварительная версия)
description: Узнайте, как с помощью Ansible создать веб-приложение со средой выполнения контейнера Java 8 и Tomcat в службе приложений на платформе Linux.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, azure app service, web app, java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 48b4c201b2b96bd4662e8c90be7298a4f418af53
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426562"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible-preview"></a>Создание веб-приложений службы приложений Azure с помощью Ansible (предварительная версия)
[Веб-приложения службы приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (или просто "Веб-приложения") размещают веб-приложения, интерфейсы REST API и серверные части мобильных решений. Вы можете выполнять разработку на привычном языке: &mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP или Python.

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье показано, как с помощью Ansible создать веб-приложение со средой выполнения Java. 

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7. Чтобы установить версию RC Ansible 2.7, выполните команду `sudo pip install ansible[azure]==2.7.0rc2`. После выпуска Ansible 2.7 не нужно указывать здесь версию, так как версия по умолчанию будет 2.7. 

## <a name="create-a-simple-app-service"></a>Создание простой службы приложений
В этом разделе представлен пример сборника схем Ansible, который определяет следующие ресурсы:
- группу ресурсов, в которой будет развернут план службы и веб-приложение;
- веб-приложение со средой выполнения контейнера Java 8 и Tomcat в службе приложений на платформе Linux.

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: myappplan
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
Сохраните упомянутый выше сборник схем, присвоив ему имя **firstwebapp.yml**.

Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook firstwebapp.yml
```

Выходные данные запуска сборника схем Ansible показывают, что веб-приложение успешно создано:

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Создание службы приложений с помощью диспетчера трафика
С помощью [диспетчера трафика Azure](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) можно управлять распределением запросов от веб-клиентов между приложениями в службе приложений Azure. Диспетчер трафика отслеживает состояние приложений службы при добавлении конечных точек службы приложений в профиль диспетчера трафика Azure. Состояния включают запуск, остановку и удаление. Затем диспетчер трафика может решить, какая из этих конечных точек должна получать трафик.

В службе приложений приложение выполняется в [плане службы приложений](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
). План службы приложений определяет набор вычислительных ресурсов, на которых выполняется веб-приложение. Вы можете управлять планом службы приложений и веб-приложением в разных группах.

В этом разделе представлен пример сборника схем Ansible, который определяет следующие ресурсы:
- группу ресурсов, в которой будет развернут план службы;
- План службы приложений
- дополнительную группу ресурсов, в которой будет развернуто веб-приложение;
- веб-приложение со средой выполнения контейнера Java 8 и Tomcat в службе приложений на платформе Linux;
- профиль диспетчера трафика;
- конечную точку диспетчера трафика с использованием созданного веб-сайта.

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
Сохраните упомянутый выше сборник схем, присвоив ему имя **webapp.yml**, или [скачайте этот сборник](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook webapp.yml
```

Выходные данные запуска сборника схем Ansible показывают, что план службы приложений, веб-приложение, профиль диспетчера трафика и конечная точка успешно созданы:
```
TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)