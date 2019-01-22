---
title: Создание, изменение или удаление таблицы маршрутов Azure с помощью Ansible
description: Сведения о создании, изменении и удалении таблицы маршрутов с помощью Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 29672a75408e42fb9239e5d826784b46e7280805
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332274"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Создание, изменение или удаление таблицы маршрутов Azure с помощью Ansible
Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов в Azure, это можно сделать путем создания [таблицы маршрутов](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье показано, как создавать, изменять или удалять таблицы маршрутов Azure и подключать таблицу маршрутов к подсети. 

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7.

## <a name="create-a-route-table"></a>Создание таблицы маршрутов
В этом разделе представлен пример сборника схем Ansible, создающий таблицу маршрутов. Количество таблиц маршрутов, которые можно создать в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Сохраните этот сборник схем как `route_table_create.yml`. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью
Подсеть может иметь не более одной таблицы маршрутов, связанной с ней. Таблица маршрутов может быть не связана с подсетями или связана с несколькими. Так как таблицы маршрутов не связаны с виртуальными сетями, вам необходимо связать таблицу маршрутов с каждой соответствующей подсетью. Весь трафик, исходящий из подсети, направляется на основе маршрутов, созданных в таблицах маршрутов, [стандартных маршрутах](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) и маршрутах, распространяемых из локальной сети, если виртуальная сеть подключена к шлюзу виртуальной сети Azure (ExpressRoute или VPN при использовании BGP со шлюзом VPN). Вы можете связать таблицы маршрутов только с теми подсетями в виртуальной сети, которые находятся в том же расположении и той же подписке Azure, что и таблица маршрутов.

В этом разделе представлен пример сборника схем Ansible, который создает виртуальную сеть и подсеть, а затем связывает таблицу маршрутов с подсетью.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Сохраните этот сборник схем как `route_table_associate.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Отмена связи таблицы маршрутов с подсетью
Для отмены связи таблицы маршрутов и подсети для `None` нужно задать `route_table` (в подсети). Ниже приведен пример сборника схем Ansible. 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Сохраните этот сборник схем как `route_table_dissociate.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Создание маршрута
В этом разделе представлен пример сборника схем Ansible, создающий маршрут в таблице маршрутов. Он определяет `virtual_network_gateway` как `next_hop_type` и `10.1.0.0/16` как `address_prefix`. Префикс не может повторяться в нескольких маршрутах в таблице маршрутов, хотя может содержаться в другом префиксе. Дополнительные сведения о том, как Azure выбирает маршруты, и подробное описание всех типов следующих прыжков см. в статье [Маршрутизация трафика в виртуальной сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
Сохраните этот сборник схем как `route_create.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Удаление маршрута
В этом разделе представлен пример сборника схем Ansible, который удаляет маршрут из таблицы маршрутов.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Сохраните этот сборник схем как `route_delete.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Получение сведений из таблицы маршрутов
Вы можете просмотреть сведения о таблице маршрутов route_table с помощью модуля Ansible с именем `azure_rm_routetable_facts`. Модуль фактов возвратит данные таблицы маршрутов со всеми подключенными к ней маршрутами.
Ниже приведен пример сборника схем Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Сохраните этот сборник схем как `route_table_facts.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Удаление таблицы маршрутов
Таблицу маршрутов нельзя удалить, если она связана с какими-либо подсетями. [Отмените связь](#dissociate-a-route-table-from-a-subnet) таблицы маршрутов со всеми подсетями, а затем попытайтесь удалить ее.

Вы можете удалить таблицу маршрутов и все маршруты. Ниже приведен пример сборника схем Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Сохраните этот сборник схем как `route_table_delete.yml`. Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)
