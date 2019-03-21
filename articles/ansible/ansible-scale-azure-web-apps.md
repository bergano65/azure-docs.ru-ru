---
title: Масштабирование веб-приложений Службы приложений Azure с помощью Ansible
description: Узнайте, как с помощью Ansible создать веб-приложение со средой выполнения контейнера Java 8 и Tomcat в службе приложений на платформе Linux.
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 2bafb73afa35c7670ac45f7027545277c70075ef
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792282"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Масштабирование веб-приложений Службы приложений Azure с помощью Ansible
[Веб-приложения Службы приложений Azure](https://docs.microsoft.com/azure/app-service/overview) (или просто "Веб-приложения") размещают веб-приложения, REST API и серверные части мобильных приложений. Вы можете выполнять разработку на привычном языке: &mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP или Python.

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье показано, как масштабировать приложение в Службе приложений Azure с помощью Ansible.

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Веб-приложения Службы приложений Azure**. Если у вас нет такого веб-приложения Azure, вы можете [создать его с помощью Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Масштабирование приложения в Службе приложений
Чтобы масштабировать приложение, измените ценовую категорию плана Службы приложений, к которому относится ваше приложение. В этом разделе представлен пример сборника схем Ansible, который определяет следующие операции:
- получение сведений о существующем плане Службы приложений;
- обновление плана Службы приложений до уровня S2 с тремя рабочими ролями;

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

сохранение этого сборника схем как файл *webapp_scaleup.yml*.

Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook webapp_scaleup.yml
```

После запуска сборника схем в выходным данных (как в следующем примере) показано, что план Службы приложений обновлен до уровня S2 с тремя рабочими ролями.
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App service plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)