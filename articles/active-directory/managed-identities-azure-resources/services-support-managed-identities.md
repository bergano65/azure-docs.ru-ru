---
title: Службы Azure, поддерживающие управляемые удостоверения — Azure AD
description: Список служб, которые поддерживают управляемые удостоверения для ресурсов Azure и аутентификацию Azure AD.
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c4c3da193a1f61280a13ff1e2fa9eea2ff4823
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196407"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Службы с поддержкой управляемых удостоверений для ресурсов Azure

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Управляемое удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. Мы работаем над интеграцией управляемых удостоверений для ресурсов Azure и аутентификации Azure AD на платформе Azure. Почаще проверяйте наличие обновлений.

> [!NOTE]
> Управляемые удостоверения для ресурсов Azure — это новое название службы "Управляемое удостоверение службы" (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Службы Azure с поддержкой управляемых удостоверений для ресурсов Azure

Ниже приведены службы Azure с поддержкой управляемых удостоверений для ресурсов Azure.

### <a name="azure-virtual-machines"></a>Виртуальные машины Azure

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | Предварительный просмотр | Предварительный просмотр | 
| Назначено пользователем | ![Доступно][check] | ![Доступно][check] | Предварительный просмотр | Предварительный просмотр |

Чтобы настроить управляемые удостоверения для виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Шаблоны Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин Azure

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр |
| Назначено пользователем | ![Доступно][check] | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр |

Чтобы настроить управляемые удостоверения для масштабируемых наборов виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Шаблоны Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Служба приложений Azure

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | ![Доступно][check] | ![Доступно][check] |
| Назначено пользователем | ![Доступно][check] | ![Доступно][check]  | ![Доступно][check]  | ![Доступно][check] |

Чтобы настроить управляемые удостоверения для Службы приложений Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)


### <a name="azure-kubernetes-service-aks"></a>Служба Azure Kubernetes (AKS)

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | - | - | - | 
| Назначено пользователем | ![Доступно][check] | - | - | - |


Дополнительные сведения см. [в статье Использование управляемых удостоверений в службе Kubernetes Azure](https://docs.microsoft.com/azure/aks/use-managed-identity).



### <a name="azure-blueprints"></a>Azure Blueprints

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | Недоступно | Недоступно |
| Назначено пользователем | ![Доступно][check] | ![Доступно][check] | Недоступно | Недоступно |

Чтобы использовать управляемое удостоверение с помощью проектов [Azure](../../governance/blueprints/overview.md), см. следующий список:

- [Портал Azure-назначение чертежей](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-назначение чертежей](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Функции Azure

Тип управляемого удостоверения |Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | ![Доступно][check] | ![Доступно][check] |
| Назначено пользователем | ![Доступно][check] | ![Доступно][check]  | ![Доступно][check]  | ![Доступно][check]  |

Чтобы настроить управляемые удостоверения для Функций Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | Недоступно | ![Доступно][check] |
| Назначено пользователем | ![Доступно][check] | ![Доступно][check] | Недоступно | ![Доступно][check] |


Чтобы настроить управляемые удостоверения для Logic Apps (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Шаблон Azure Resource Manager](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)

### <a name="azure-data-factory-v2"></a>Фабрика данных Azure версии 2

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | Недоступно | ![Доступно][check] |
| Назначено пользователем | Недоступно | Недоступно | Недоступно | Недоступно |

Чтобы настроить управляемые удостоверения для Фабрики данных Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [Портал Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Cлужба управления Azure API 

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | ![Доступно][check] | Недоступно | ![Доступно][check] |
| Назначено пользователем | Предварительный просмотр | Предварительный просмотр | Недоступно | Предварительный просмотр |

Чтобы настроить управляемые удостоверения для Управления API Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [Шаблон Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | Linux: предварительная версия<br>Windows: недоступно | Недоступно | Недоступно | Недоступно |
| Назначено пользователем | Linux: предварительная версия<br>Windows: недоступно | Недоступно | Недоступно | Недоступно |

Чтобы настроить управляемые удостоверения для Экземпляров контейнеров Azure (в регионах, где они доступны), см. следующий список.

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Шаблон Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Задачи Реестра контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | Недоступно | Недоступно | Недоступно |
| Назначено пользователем | Предварительный просмотр | Недоступно | Недоступно | Недоступно |

См. следующий список, чтобы настроить управляемое удостоверение для задач реестра контейнеров Azure (в регионах, где это возможно):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Управляемое удостоверение для Service Fabric приложений](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) доступно в предварительной версии и доступно во всех регионах.

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступно][check] | Недоступно | Недоступно | недоступно |
| Назначено пользователем | ![Доступно][check] | Недоступно | Недоступно |Недоступно |

Чтобы настроить управляемое удостоверение для приложений Azure Service Fabric во всех регионах, см. следующий список:
- [Шаблон Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Службы Azure, поддерживающие аутентификацию Azure AD

Ниже приведены службы, поддерживающие аутентификацию Azure AD, которые были проверены с помощью служб клиента, использующих управляемые удостоверения для ресурсов Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Чтобы настроить доступ к Azure Resource Manager, обратитесь к следующему списку:

- [Назначение доступа через портал Azure](howto-assign-access-portal.md)
- [Назначение доступа с помощью PowerShell](howto-assign-access-powershell.md)
- [Назначение доступа через Azure CLI](howto-assign-access-CLI.md)
- [Назначение доступа с помощью шаблона Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://management.azure.com/`| ![Доступно][check] |
| Azure для государственных организаций | `https://management.usgovcloudapi.net/` | ![Доступно][check] |
| Azure для Германии | `https://management.microsoftazure.de/` | ![Доступно][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Доступно][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://vault.azure.net`| ![Доступно][check] |
| Azure для государственных организаций | `https://vault.usgovcloudapi.net` | ![Доступно][check] |
| Azure для Германии |  `https://vault.microsoftazure.de` | ![Доступно][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Доступно][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://datalake.azure.net/` | ![Доступно][check] |
| Azure для государственных организаций |  | Недоступно |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |

### <a name="azure-sql"></a>Azure SQL 

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://database.windows.net/` | ![Доступно][check] |
| Azure для государственных организаций | `https://database.usgovcloudapi.net/` | ![Доступно][check] |
| Azure для Германии | `https://database.cloudapi.de/` | ![Доступно][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Доступно][check] |

### <a name="azure-event-hubs"></a>Центры событий Azure

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://eventhubs.azure.net` | ![Доступно][check] |
| Azure для государственных организаций |  | Недоступно |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |

### <a name="azure-service-bus"></a>Служебная шина Azure

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://servicebus.azure.net`  | ![Доступно][check] |
| Azure для государственных организаций |  | ![Доступно][check] |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |









### <a name="azure-storage-blobs-and-queues"></a>Большие двоичные объекты и очереди службы хранилища Azure

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Доступно][check] |
| Azure для государственных организаций | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Доступно][check] |
| Azure для Германии | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Доступно][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Доступно][check] |










### <a name="azure-analysis-services"></a>Службы Azure Analysis Services

| Облако | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://*.asazure.windows.net` | ![Доступно][check] |
| Azure для государственных организаций | `https://*.asazure.usgovcloudapi.net` | ![Доступно][check] |
| Azure для Германии | `https://*.asazure.cloudapi.de` | ![Доступно][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Доступно][check] |

> [!Note]
> Microsoft Power BI также [поддерживает управляемые удостоверения](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).


[check]: media/services-support-managed-identities/check.png "Имеющ"
