---
title: Таблица версий и модулей Ansible для Azure | Документация Майкрософт
description: Таблица версий и модулей Ansible для Azure
keywords: ansible, roles, matrix, version, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 721179e12ed7f21312fe848a6bef1a8e19bc8083
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866053"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают виртуальные машины, масштабируемые наборы, сетевых служб и служб контейнеров. В этой статье перечислены различные модули Ansible для Azure и версий Ansible, в которых они входят.

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure

Следующие модули могут быть выполнены непосредственно на удаленных узлах или через сборники тренировочных заданий.

Эти модули доступны из официального выпуска Ansible и из указанных далее ролей playbook Microsoft.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Среда выполнения приложений**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_availabilityset_facts              | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_deployment                         | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_functionapp                        | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_functionapp_facts                  | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_image                              | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_resource                           | -            | -                           | Yes          | Да          | Да          | Yes          |
| azure_rm_resource_facts                     | -            | -                           | Yes          | Да          | Да          | Yes          |
| azure_rm_resourcegroup                      | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_resourcegroup_facts                | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachine                     | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_virtualmachineextension           | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachineimage_facts          | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachinescaleset            | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachinescaleset_facts      | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Yes          | Yes          |
| **Сеть**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_dnsrecordset                       | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_dnsrecordset_facts                 | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_dnszone                            | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_dnszone_facts                      | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_loadbalancer                       | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_loadbalancer_facts                 | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_networkinterface                   | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_networkinterface_facts             | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_publicipaddress                    | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_publicipaddress_facts              | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_route                              | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_securitygroup                      | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_subnet                             | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_virtualnetwork                     | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualnetwork_facts               | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Yes          | Yes          |
| **Хранилище**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_manageddisk_facts                  | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_storageaccount                     | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_storageaccount_facts               | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_storageblob                        | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| **Веб-приложения**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_webapp                             | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Yes          | Yes          |
| **Контейнеры**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Yes          | Да                         | Да          | Да          | Да          | Yes          |
| azure_rm_aks                                | -            | -                           | Yes          | Да          | Да          | Yes          |
| azure_rm_aks_facts                          | -            | -                           | Yes          | Да          | Да          | Yes          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerinstance                  | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistry                  | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Базы данных**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqldatabase                      | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlserver                        | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqldatabase                 | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlserver                   | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqldatabase                        | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Да          | Yes          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlserver                          | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_sqlserver_facts                    | -            | Yes                         | Да          | Да          | Да          | Yes          |
| **Аналитика в Application Insights**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Yes          | Yes          |
| **Интеграция**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Yes          | Yes          |
| **Безопасность**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Yes          | Yes          |
| azure_rm_keyvaultkey                        | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Да          | Да          | Да          | Yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Да          | Yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Yes          | Yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure

[Роль playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для подготовки ресурсов Azure, настоятельно рекомендуется установить `azure_preview_module` роль playbook.

`azure_preview_module` Роль playbook выпускается каждые три недели.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о ролях playbook см. в разделе [создание многократно используемых наборов инструкций playbook](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
