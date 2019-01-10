---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fe995535cd42571ad96f192883e48f4b6ea1eb0e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160409"
---
1. В Cloud Shell создайте файл с именем `rg.yml`.

    ```bash
    vi rg.yml
    ```

1. Нажмите ключ **I**, чтобы войти в режим вставки.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

   ```yaml
   ---
   - hosts: localhost
     connection: local
     tasks:
       - name: Create resource group
         azure_rm_resourcegroup:
           name: ansible-rg
           location: eastus
         register: rg
       - debug:
           var: rg
   ```

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1. Запустите сборник схем `rg.yml`.

   ```bash
   ansible-playbook rg.yml
   ```

Результаты выполнения команды ansible должны выглядеть примерно следующим образом.

```output
PLAY [localhost] *********************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [localhost]

TASK [Create resource group] *********************************************************************
changed: [localhost]

TASK [debug] *************************************************************************************
ok: [localhost] => {
    "rg": {
        "changed": true,
        "contains_resources": false,
        "failed": false,
        "state": {
            "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/ansible-rg",
            "location": "eastus",
            "name": "ansible-rg",
            "provisioning_state": "Succeeded",
            "tags": null
        }
    }
}

PLAY RECAP ***************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```