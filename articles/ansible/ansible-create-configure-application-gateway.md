---
title: Управление веб-трафиком с помощью Шлюза приложений Azure в Ansible (предварительная версия)
description: Сведения об использовании службы Ansible для создания и настройки входящего веб-трафика Шлюза приложений Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, сборник схем, шлюз приложений azure, подсистема балансировки нагрузки, веб-трафик
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 02b98cb22d897fc9599f6e44ddc57ef4211b0893
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410869"
---
# <a name="manage-web-traffic-with-azure-application-gateway-using-ansible-preview"></a>Управление веб-трафиком с помощью Шлюза приложений Azure в Ansible (предварительная версия)
[Шлюз приложений Azure](https://docs.microsoft.com/azure/application-gateway/) — это подсистема балансировки нагрузки веб-трафика, предназначенная для управления трафиком веб-приложений. 

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. В этой статье показано, как использовать службу Ansible для создания Шлюза приложений Azure и использовать его для управления серверами Azure. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка сети
> * Создание двух экземпляров контейнеров Azure с помощью образа httpd
> * Создание шлюза приложений с помощью указанных выше экземпляров контейнеров Azure в серверном пуле


## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.7. Чтобы установить версию RC Ansible 2.7, выполните команду `sudo pip install ansible[azure]==2.7.0rc2`. Выпуск Ansible 2.7 запланирован на октябрь 2018 г. После этой даты указывать версию не нужно, так как по умолчанию будет использоваться версия 2.7. 

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.  

В следующем примере создается группа ресурсов с именем **myResourceGroup** в расположении **eastus**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Сохраните сборник схем выше как *rg.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов 
Для шлюза приложений нужно создать виртуальную сеть, чтобы он мог обмениваться данными с другими ресурсами. 

В следующем примере создается виртуальная сеть с именем **myVNet**, подсеть с именем **myAGSubnet** и общедоступный IP-адрес с именем **myAGPublicIPAddress** с помощью домена с именем **mydomain**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Сохраните сборник схем выше как *vnet_create.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook vnet_create.yml
```

## <a name="create-backend-servers"></a>Создание внутренних серверов
В этом примере вы создадите два экземпляра контейнеров Azure с помощью образа httpd, которые будут использоваться как внутренние серверы для шлюза приложений.  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Сохраните сборник схем выше как *aci_create.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Теперь давайте создадим шлюз приложения. В следующем примере создается шлюз приложения с именем **myAppGateway** с помощью настройки серверного внешнего интерфейса и http.  

> [!div class="checklist"]
> * **appGatewayIP**, определенный в блоке **gateway_ip_configurations**, — ссылка на подсеть требуется для настройки IP-адреса шлюза. 
> * **appGatewayBackendPool**, определенный в блоке **backend_address_pools**, — шлюз приложений должен иметь по крайней мере один внутренний пул адресов. 
> * **appGatewayBackendHttpSettings**, определенный в блоке **backend_http_settings_collection**, — указывает, что для обмена данными используются порт 80 и протокол HTTP. 
> * **appGatewayHttpListener**, определенный в блоке **backend_http_settings_collection**, — является прослушивателем по умолчанию, который связан с appGatewayBackendPool. 
> * **appGatewayFrontendIP**, определенный в блоке **frontend_ip_configurations**, — назначает myAGPublicIPAddress для appGatewayHttpListener. 
> * **rule1**, определенное в блоке **request_routing_rules**, — правило маршрутизации по умолчанию, связанное с прослушивателем appGatewayHttpListener. 

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Сохраните сборник схем выше как *appgw_create.yml*. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook appgw_create.yml
```

Создание шлюза приложений может занять несколько минут. 

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

В вышеприведенном образце сборника схем для сетевых ресурсов домен с именем **mydomain** был создан в **eastus**. Теперь можно перейти в браузер и ввести `http://mydomain.eastus.cloudapp.azure.com`. Отобразится следующая страница, подтверждающая, что Шлюз приложений работает должным образом.

![Доступ к Шлюзу приложений](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам не нужны эти ресурсы, вы можете удалить их, выполнив пример ниже. Будет удалена группа ресурсов с именем **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Сохраните сборник схем выше как *rg_delete*.yml. Чтобы запустить сборник схем, используйте команду **ansible-playbook** следующим образом:
```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Документация по Ansible в Azure](https://docs.microsoft.com/azure/ansible/)
