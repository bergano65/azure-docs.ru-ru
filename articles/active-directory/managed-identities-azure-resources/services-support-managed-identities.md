---
title: Службы Azure, поддерживающие управляемые идентификаторы - Azure AD
description: Список служб, которые поддерживают управляемые удостоверения для ресурсов Azure и аутентификацию Azure AD.
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 03/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9e01f1f5abfc0f76926ce503fae058c196c6e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282109"
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
| Назначено системой | ![Доступны][check] | ![Доступны][check] | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) | 
| Назначено пользователем | ![Доступны][check] | ![Доступны][check] | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) |

Чтобы настроить управляемые удостоверения для виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Лазурный CLI](qs-configure-cli-windows-vm.md)
- [Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md)
- [Остальные](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин Azure

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) |
| Назначено пользователем | ![Доступны][check] | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) | Preview (Предварительный просмотр) |

Чтобы настроить управляемые удостоверения для масштабируемых наборов виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Лазурный CLI](qs-configure-cli-windows-vm.md)
- [Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md)
- [Остальные](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Служба приложений Azure

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | ![Доступны][check] | ![Доступны][check] |
| Назначено пользователем | ![Доступны][check] | ![Доступны][check]  | ![Доступны][check]  | ![Доступны][check] |

Чтобы настроить управляемые удостоверения для Службы приложений Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Лазурный CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Лазурная силаШелл](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон менеджера ресурсов Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | Недоступно | Недоступно |
| Назначено пользователем | ![Доступны][check] | ![Доступны][check] | Недоступно | Недоступно |

Обратитесь к следующему списку, чтобы использовать управляемый итог с [помощью чертежей Azure:](../../governance/blueprints/overview.md)

- [Портал Azure - назначение чертежа](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - назначение чертежа](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Проверка

Тип управляемого удостоверения |Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | ![Доступны][check] | ![Доступны][check] |
| Назначено пользователем | ![Доступны][check] | ![Доступны][check]  | ![Доступны][check]  | ![Доступны][check]  |

Чтобы настроить управляемые удостоверения для Функций Azure (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Лазурный CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Лазурная силаШелл](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон менеджера ресурсов Azure](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | Недоступно | ![Доступны][check] |
| Назначено пользователем | ![Доступны][check] | ![Доступны][check] | Недоступно | ![Доступны][check] |


Чтобы настроить управляемые удостоверения для Logic Apps (в регионах, где они доступны), см. следующий список.

- [Портал Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Шаблон менеджера ресурсов Azure](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Фабрика данных Azure версии 2

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | Недоступно | ![Доступны][check] |
| Назначено пользователем | Недоступно | Недоступно | Недоступно | Недоступно |

Чтобы настроить управляемые удостоверения для Фабрики данных Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [Портал Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [Остальные](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Пакет SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Cлужба управления Azure API 

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | ![Доступны][check] | Недоступно | Недоступно |
| Назначено пользователем | Недоступно | Недоступно | Недоступно | Недоступно |

Чтобы настроить управляемые удостоверения для Управления API Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [Шаблон менеджера ресурсов Azure](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | Linux: предварительная версия<br>Windows: недоступно | Недоступно | Недоступно | Недоступно |
| Назначено пользователем | Linux: предварительная версия<br>Windows: недоступно | Недоступно | Недоступно | Недоступно |

Чтобы настроить управляемые удостоверения для Экземпляров контейнеров Azure (в регионах, где они доступны), см. следующий список.

- [Лазурный CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Шаблон менеджера ресурсов Azure](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Задачи Реестра контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | Недоступно | Недоступно | Недоступно |
| Назначено пользователем | Preview (Предварительный просмотр) | Недоступно | Недоступно | Недоступно |

Обратитесь к следующему списку для настройки управляемой идентификации для задач реестра контейнеров Azure (в регионах, где это возможно):

- [Лазурный CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Управляемая идентификация для приложений Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) находится в предварительном просмотре и доступна во всех регионах.

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Назначено системой | ![Доступны][check] | Недоступно | Недоступно | недоступно |
| Назначено пользователем | ![Доступны][check] | Недоступно | Недоступно |Недоступно |

Обратитесь к следующему списку для настройки управляемых интизантов для приложений Azure Service Fabric во всех регионах:
- [Шаблон менеджера ресурсов Azure](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Службы Azure, поддерживающие аутентификацию Azure AD

Ниже приведены службы, поддерживающие аутентификацию Azure AD, которые были проверены с помощью служб клиента, использующих управляемые удостоверения для ресурсов Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Обратитесь к следующему списку, чтобы настроить доступ к менеджеру ресурсов Azure:

- [Назначить доступ через портал Azure](howto-assign-access-portal.md)
- [Назначить доступ через PowerShell](howto-assign-access-powershell.md)
- [Назначить доступ через Azure CLI](howto-assign-access-CLI.md)
- [Назначить доступ через шаблон менеджера ресурсов Azure](../../role-based-access-control/role-assignments-template.md)

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://management.azure.com/`| ![Доступны][check] |
| Azure для государственных организаций | `https://management.usgovcloudapi.net/` | ![Доступны][check] |
| Azure для Германии | `https://management.microsoftazure.de/` | ![Доступны][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Доступны][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://vault.azure.net`| ![Доступны][check] |
| Azure для государственных организаций | `https://vault.usgovcloudapi.net` | ![Доступны][check] |
| Azure для Германии |  `https://vault.microsoftazure.de` | ![Доступны][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Доступны][check] |

### <a name="azure-data-lake"></a>Azure Data Lake; 

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://datalake.azure.net/` | ![Доступны][check] |
| Azure для государственных организаций |  | Недоступно |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://database.windows.net/` | ![Доступны][check] |
| Azure для государственных организаций | `https://database.usgovcloudapi.net/` | ![Доступны][check] |
| Azure для Германии | `https://database.cloudapi.de/` | ![Доступны][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Доступны][check] |

### <a name="azure-event-hubs"></a>Центры событий Azure

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://eventhubs.azure.net` | ![Доступны][check] |
| Azure для государственных организаций |  | Недоступно |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |

### <a name="azure-service-bus"></a>Служебная шина Azure

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://servicebus.azure.net`  | ![Доступны][check] |
| Azure для государственных организаций |  | ![Доступны][check] |
| Azure для Германии |   | Недоступно |
| Azure China 21Vianet |  | Недоступно |









### <a name="azure-storage-blobs-and-queues"></a>Капли и очереди для хранения azure

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Доступны][check] |
| Azure для государственных организаций | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Доступны][check] |
| Azure для Германии | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Доступны][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Доступны][check] |










### <a name="azure-analysis-services"></a>Службы Azure Analysis Services

| Cloud | Идентификатор ресурса | Состояние |
|--------|------------|:-:|
| Azure (глобальный) | `https://*.asazure.windows.net` | ![Доступны][check] |
| Azure для государственных организаций | `https://*.asazure.usgovcloudapi.net` | ![Доступны][check] |
| Azure для Германии | `https://*.asazure.cloudapi.de` | ![Доступны][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Доступны][check] |


[check]: media/services-support-managed-identities/check.png "Доступны"
