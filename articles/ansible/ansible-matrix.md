---
title: Таблица версий и модулей Ansible для Azure
description: Таблица версий и модулей Ansible для Azure
ms.service: ansible
keywords: ansible, roles, matrix, version, azure, devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: 149f37d0231ecc0547e8dc7937d22a9cc38b7df3
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810224"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure
Ansible поставляется с большим количеством модулей, которые могут выполняться прямо на удаленных узлах или с помощью наборов инструкций playbook.
В этой статье перечислены модули Ansible для Azure, которые могут использоваться для подготовки таких облачных ресурсов Azure, как виртуальные машины, сети и службы контейнеров. Эти модули можно получить из официального выпуска Ansible или из указанных далее ролей playbook, опубликованных корпорацией Майкрософт.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Среда выполнения приложений**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_deployment                         | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_resource                           | -            | -                           | Yes          | Да          | Yes                                 | 
| azure_rm_resource_facts                     | -            | -                           | Yes          | Да          | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_image                              |              | Yes                         | Да          | Да          | Yes                                 | 
| **Сеть**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_subnet                             | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_securitygroup                      | Yes          | Да                         | Да          | Да          | Yes                                 |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Yes                                 | 
| **Хранилище**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Да                         | Да          | Да          | Yes                                 | 
| **Контейнеры**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Yes          | Да          | Yes                                 | 
| azure_rm_aks_facts                          | -            | -                           | Yes          | Да          | Yes                                 | 
| azure_rm_acs                                | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_containerinstance                  | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes                                 | 
| **Функции Azure**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Yes          | Да                         | Да          | Да          | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Да                         | Да          | Да          | Yes                                 | 
| **Базы данных**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_sqldatabase                        | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | Yes                         | Да          | Да          | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes                                 | 
| **хранилище ключей;**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Yes                         | Да          | Да          | Yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Yes                               |
| azure_rm_keyvaultkey                        | -            | Yes                         | Да          | Да          | Yes                                 |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Да          | Да          | Yes                                 |
| **Веб-приложения**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Yes          | Yes                                 | 
| **Диспетчер трафика**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Yes          | Yes                                 | 
| **AutoScale**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Yes                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure
[Роль azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) — наиболее полная роль playbook, которая включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для подготовки ресурсов Azure, рекомендуем установить роль playbook azure_preview_module.

Роль playbook azure_preview_module выпускается каждые три недели.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о ролях playbook см. в документе [о создании повторно используемых наборов инструкций playbook](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
