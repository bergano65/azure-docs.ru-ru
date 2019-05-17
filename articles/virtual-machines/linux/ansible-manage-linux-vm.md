---
title: Краткое руководство по управлению виртуальными машинами Linux в Azure с помощью Ansible | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Ansible управлять виртуальной машиной Linux в Azure.
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a7862e95966d7b0e0ab31f242dff0244735fe7a1
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409234"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Краткое руководство. Управление виртуальными машинами Linux в Azure с помощью Ansible

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье описано, как запустить и остановить виртуальную машину Linux с помощью сборника схем Ansible. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Остановка виртуальной машины

В этом разделе вы отмените выделение (остановите) виртуальной машины Azure с помощью Ansible.

1. Войдите на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл `azure-vm-stop.yml` и откройте его в редакторе.

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Замените заполнители `{{ resource_group_name }}` и `{{ vm_name }}` собственными значениями.

1. Сохраните файл и закройте редактор.

1. Запустите сборник схем с помощью команды `ansible-playbook`.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>запуск виртуальной машины

В этом разделе вы запустите остановленную виртуальную машину Azure (т. е. выделение которой было отменено) с помощью Ansible.

1. Войдите на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл `azure-vm-start.yml` и откройте его в редакторе.

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Замените заполнители `{{ resource_group_name }}` и `{{ vm_name }}` собственными значениями.

1. Сохраните файл и закройте редактор.

1. Запустите сборник схем с помощью команды `ansible-playbook`.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. После запуска сборника схем отобразятся результаты, аналогичные приведенным ниже.

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Руководство по управлению динамическими списками Azure с помощью Ansible](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)