---
title: Матрица версий и модулей Ansible для Azure | Документация Майкрософт
description: Таблица версий и модулей Ansible для Azure
keywords: ansible, roles, matrix, version, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155989"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают в себя виртуальные машины, масштабируемые наборы, сетевые службы и службы контейнеров. В этой статье перечислены различные модули Ansible для Azure и версии Ansible, в которых они входят.

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure

Следующие модули можно выполнять непосредственно на удаленных узлах или с помощью модули PlayBook.  

Эти модули доступны в официальном выпуске Ansible и следующих ролях Microsoft сборник тренировочных заданий.

> [!NOTE]
> Начиная с Ansible 2,9, мы переименовали все модули * _facts на * _info, чтобы соблюдать соглашение об именовании Ansible. Старые и переименованные модули связаны так, чтобы не видеть предупреждение об устаревании, все модули работают как раньше.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Вычисления**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_availabilityset_info              | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_deployment                         | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_functionapp                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_functionapp_info                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_image                              | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_resource                           | -            | -                           | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resource_info                     | -            | -                           | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resourcegroup_info                | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachineextension            | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualmachineimage_info          | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescaleset             | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescaleset_info       | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Yes          | Yes          | Yes          |
| **Сеть**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnsrecordset_info                 | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnszone                            | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnszone_info                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_loadbalancer_info                 | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_networkinterface_info             | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress_info              | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_routetable_info                   | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_subnet                             | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetwork_info               | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Yes          | Yes          |
| **Хранилище**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_manageddisk_info                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageaccount_info               | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageblob                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| **Веб-приложения**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_webapp                             | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_webapp_info                       | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Yes          | Yes          | Yes          |
| **Контейнеры**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aks                                | -            | -                           | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aks_info                          | -            | -                           | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_containerinstance                  | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_containerregistry                  | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Yes          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Yes          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Yes          |
| **Базы данных**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqlserver                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_sqldatabase                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Yes          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_sqlserver                          | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqlserver_info                    | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| **Аналитика**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Yes          | Yes          |
| **Интеграция**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| **безопасность**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Yes          | Yes          | Yes          |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Yes          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Yes          | Yes          | Yes          | Yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Yes          | Yes          | Yes          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Yes          | Yes          |
| **Руководство и управление**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Yes        | Yes          |
| **Интернет вещей**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Yes        | Yes          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Yes        | Yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure

[Роль azure_preview_module сборник тренировочных заданий](https://galaxy.ansible.com/Azure/azure_preview_modules/) включает все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для целей подготовки ресурсов Azure, рекомендуется установить роль `azure_preview_module` сборник тренировочных заданий.

Роль `azure_preview_module` сборник тренировочных заданий выпускается каждые три недели.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о ролях сборник тренировочных заданий см. в статье [Создание повторно используемого модули PlayBook](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 