---
title: Матрица версий и модулей Ansible для Azure | Документация Майкрософт
description: Таблица версий и модулей Ansible для Azure
keywords: ansible, roles, matrix, version, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 275dca40ab20c222da2b9115f9a5dc141228c766
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385469"
---
# <a name="ansible-module-and-version-matrix"></a>Таблица версий и модулей Ansible

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают в себя виртуальные машины, масштабируемые наборы, сетевые службы и службы контейнеров. В этой статье перечислены различные модули Ansible для Azure и версии Ansible, в которых они входят.

## <a name="ansible-modules-for-azure"></a>Модули Ansible для Azure

Следующие модули можно выполнять непосредственно на удаленных узлах или с помощью модули PlayBook.  

Эти модули доступны в официальном выпуске Ansible и следующих ролях Microsoft сборник тренировочных заданий.

> [!NOTE]
> Начиная с Ansible 2,9, мы переименовали все модули * _facts в * _info, чтобы они соответствовали Соглашению об именовании Ansible. Старые и переименованные модули связаны так, чтобы не видеть предупреждение об устаревании, все модули работают как раньше.

| Модуль Ansible для Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Роль Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Среда выполнения приложений**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_availabilityset_info              | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_deployment                         | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_functionapp                        | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_functionapp_info                  | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_image                              | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_image_info                        | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_resource                           | -            | -                           | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_resource_info                     | -            | -                           | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_resourcegroup                      | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_resourcegroup_info                | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_virtualmachine                     | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachineextension            | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualmachineimage_info          | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescaleset             | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescaleset_info       | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | ДА          | ДА          | ДА          |
| **Сеть**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_dnsrecordset                       | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_dnsrecordset_info                 | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_dnszone                            | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_dnszone_info                      | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_loadbalancer                       | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_loadbalancer_info                 | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_networkinterface                   | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_networkinterface_info             | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_publicipaddress                    | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_publicipaddress_info              | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_route                              | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_routetable                         | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_routetable_info                   | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_securitygroup                      | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_subnet                             | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualnetwork                     | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualnetwork_info               | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | ДА          | ДА          |
| **Хранилище**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_manageddisk_info                  | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_storageaccount                     | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_storageaccount_info               | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_storageblob                        | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| **Веб-приложения**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_webapp                             | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_webapp_info                       | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | ДА          | ДА          | ДА          |
| **Контейнеры**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | ДА          | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_aks                                | -            | -                           | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_aks_info                          | -            | -                           | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_containerinstance                  | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_containerregistry                  | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_containerregistry_info            | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | ДА          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | ДА          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | ДА          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | ДА          |
| **Базы данных**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mysqldatabase                      | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_mysqlserver                        | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_postgresqldatabase                 | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_postgresqlserver                   | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_sqldatabase                        | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | ДА          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | ДА          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | ДА          | ДА          | ДА          | ДА          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_sqlserver                          | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_sqlserver_info                    | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | ДА          | ДА          |
| **Интеграция**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| **Безопасность**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | ДА          | ДА          | ДА          |
| azure_rm_keyvaultkey                        | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_keyvaultsecret                     | -            | ДА                         | ДА          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | ДА          | ДА          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_autoscale_info                    | -            | -                         | -          | ДА          | ДА          | ДА          | ДА          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | ДА          | ДА          | ДА          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | ДА          | ДА          |
| **Руководство и управление**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | ДА        | ДА          |
| **Интернет вещей**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | ДА        | ДА          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | ДА        | ДА          |

## <a name="introduction-to-playbook-role-for-azure"></a>Общие сведения о роли playbook для Azure

[Роль azure_preview_module сборник тренировочных заданий](https://galaxy.ansible.com/Azure/azure_preview_modules/) включает в себя все последние модули Azure. Обновления и исправления выполняются более оперативно, чем для официального выпуска Ansible. Если вы используете Ansible для целей подготовки ресурсов Azure, рекомендуется установить роль `azure_preview_module` сборник тренировочных заданий.

Роль `azure_preview_module` сборник тренировочных заданий выпускается каждые три недели.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о ролях сборник тренировочных заданий см. в статье [Создание повторно используемого модули PlayBook](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 