---
title: Таблица версий и модулей Ansible для Azure | Документация Майкрософт
description: Таблица версий и модулей Ansible для Azure
keywords: ansible, roles, matrix, version, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b3396b2f4639cc7298b77810dbaafadd308d6b24
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230982"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают виртуальные машины, масштабируемые наборы, сетевых служб и служб контейнеров. В этой статье перечислены различные модули Ansible для Azure и версий Ansible, в которых они входят.

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure

Следующие модули могут быть выполнены непосредственно на удаленных узлах или через сборники тренировочных заданий.

Эти модули доступны из официального выпуска Ansible и из указанных далее ролей playbook Microsoft.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Среда выполнения приложений**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_availabilityset_facts              | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_deployment                         | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_functionapp                        | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_functionapp_facts                  | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_image                              | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_resource                           | -            | -                           | Да          | Да          | Да          | Да          |
| azure_rm_resource_facts                     | -            | -                           | Да          | Да          | Да          | Да          |
| azure_rm_resourcegroup                      | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_resourcegroup_facts                | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachine                     | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_virtualmachineextension           | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_virtualmachineimage_facts          | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescaleset            | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescaleset_facts      | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Да          | Да          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Да          | Да          |
| **Сеть**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Да          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Да          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Да          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Да          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Да          | Да          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Да          | Да          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Да          | Да          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Да          | Да          |
| azure_rm_dnsrecordset                       | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_dnsrecordset_facts                 | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_dnszone                            | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_dnszone_facts                      | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_loadbalancer                       | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_loadbalancer_facts                 | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_networkinterface                   | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_networkinterface_facts             | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_publicipaddress                    | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_publicipaddress_facts              | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_route                              | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_routetable                         | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_securitygroup                      | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_subnet                             | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_virtualnetwork                     | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualnetwork_facts               | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Да          | Да          |
| **Хранилище**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_manageddisk_facts                  | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_storageaccount                     | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_storageaccount_facts               | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_storageblob                        | Да          | Да                         | Да          | Да          | Да          | Да          |
| **Веб-приложения**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_webapp                             | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Да          | Да          |
| **Контейнеры**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Да          | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_aks                                | -            | -                           | Да          | Да          | Да          | Да          |
| azure_rm_aks_facts                          | -            | -                           | Да          | Да          | Да          | Да          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_containerinstance                  | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_containerregistry                  | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Да          | Да          |
| **Базы данных**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mysqldatabase                      | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_mysqlserver                        | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_postgresqldatabase                 | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_postgresqlserver                   | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_sqldatabase                        | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Да          | Да          | Да          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_sqlserver                          | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_sqlserver_facts                    | -            | Да                         | Да          | Да          | Да          | Да          |
| **Аналитика в Application Insights**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Да          | Да          |
| **Интеграция**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Да          | Да          |
| **Безопасность**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Да          | Да          |
| azure_rm_keyvaultkey                        | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_keyvaultsecret                     | -            | Да                         | Да          | Да          | Да          | Да          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Да          | Да          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Да          | Да          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Да          | Да          | Да          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Да          | Да          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Да          | Да          |

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure

[Роль playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для подготовки ресурсов Azure, настоятельно рекомендуется установить `azure_preview_module` роль playbook.

`azure_preview_module` Роль playbook выпускается каждые три недели.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о ролях playbook см. в разделе [создание многократно используемых наборов инструкций playbook](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
