---
title: Службы Azure с поддержкой управляемых удостоверений для ресурсов Azure
description: Список служб, которые поддерживают управляемые удостоверения для ресурсов Azure и аутентификацию Azure AD.
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37eba245933bc94e9fb631abd3b4ff495f8c6d67
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087598"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Службы с поддержкой управляемых удостоверений для ресурсов Azure

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Управляемое удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. Мы работаем над интеграцией управляемых удостоверений для ресурсов Azure и аутентификации Azure AD на платформе Azure. Почаще проверяйте наличие обновлений.

> [!NOTE]
> Управляемые удостоверения для ресурсов Azure — это новое название службы "Управляемое удостоверение службы" (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Службы Azure с поддержкой управляемых удостоверений для ресурсов Azure

Ниже приведены службы Azure с поддержкой управляемых удостоверений для ресурсов Azure.

### <a name="azure-virtual-machines"></a>Виртуальные машины Azure

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр | 
| Назначено пользователем | Доступно | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр |

Чтобы настроить управляемые удостоверения для виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](qs-configure-cli-windows-vm.md)
- [Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин Azure

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр |
| Назначено пользователем | Доступно | Предварительный просмотр | Предварительный просмотр | Предварительный просмотр |

Чтобы настроить управляемые удостоверения для масштабируемых наборов виртуальных машин Azure (в регионах, где они доступны), см. следующий список.

- [портал Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Интерфейс командной строки Azure](qs-configure-cli-windows-vm.md)
- [Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Служба приложений Azure

| Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Доступно | Доступно | Доступно |
| Назначено пользователем | Доступно | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Службы приложений Azure (в регионах, где они доступны), см. следующий список.

- [портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Интерфейс командной строки Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Доступно | Недоступен | Недоступен |
| Назначено пользователем | Доступно | Доступно | Недоступен | Недоступен |

Чтобы использовать управляемое удостоверение с помощью проектов [Azure](../../governance/blueprints/overview.md), см. следующий список:

- [Портал Azure-назначение чертежей](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-назначение чертежей](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Функции Azure

Тип управляемого удостоверения |Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Доступно | Доступно | Доступно |
| Назначено пользователем | Доступно | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Функций Azure (в регионах, где они доступны), см. следующий список.

- [портал Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Интерфейс командной строки Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Шаблон Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Предварительный просмотр | Предварительный просмотр | Недоступен | Предварительный просмотр |
| Назначено пользователем | Недоступен | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Logic Apps (в регионах, где они доступны), см. следующий список.

- [портал Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Шаблон Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Фабрика данных Azure версии 2

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Недоступен | Недоступен | Недоступен |
| Назначено пользователем | Недоступен | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Фабрики данных Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [портал Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Пакет SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Управление API Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Доступно | Недоступен | Недоступен |
| Назначено пользователем | Недоступен | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Управления API Azure версии 2 (в регионах, где они доступны), см. следующий список.

- [Шаблон Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Экземпляры контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Linux: Предварительный просмотр<br>Windows: Недоступен | Недоступен | Недоступен | Недоступен |
| Назначено пользователем | Linux: Предварительный просмотр<br>Windows: Недоступен | Недоступен | Недоступен | Недоступен |

Чтобы настроить управляемые удостоверения для Экземпляров контейнеров Azure (в регионах, где они доступны), см. следующий список.

- [Интерфейс командной строки Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Шаблон Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Задачи Реестра контейнеров Azure

Тип управляемого удостоверения | Все общедоступные версии<br>Глобальные регионы Azure | Azure для государственных организаций | Azure для Германии | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Назначено системой | Доступно | Недоступен | Недоступен | Недоступен |
| Назначено пользователем | Предварительный просмотр | Недоступен | Недоступен | Недоступен |

См. следующий список, чтобы настроить управляемое удостоверение для задач реестра контейнеров Azure (в регионах, где это возможно):

- [Интерфейс командной строки Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Службы Azure, поддерживающие аутентификацию Azure AD

Ниже приведены службы, поддерживающие аутентификацию Azure AD, которые были проверены с помощью служб клиента, использующих управляемые удостоверения для ресурсов Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Чтобы настроить доступ к Azure Resource Manager, обратитесь к следующему списку:

- [Назначение доступа через портал Azure](howto-assign-access-portal.md)
- [Назначение доступа с помощью PowerShell](howto-assign-access-powershell.md)
- [Назначение доступа через Azure CLI](howto-assign-access-CLI.md)
- [Назначение доступа с помощью шаблона Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://management.azure.com/`| Доступно |
| Azure для государственных организаций | `https://management.usgovcloudapi.net/` | Доступно |
| Azure для Германии | `https://management.microsoftazure.de/` | Доступно |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Доступно |

### <a name="azure-key-vault"></a>Хранилище Azure Key Vault

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://vault.azure.net`| Доступно |
| Azure для государственных организаций | `https://vault.usgovcloudapi.net` | Доступно |
| Azure для Германии |  `https://vault.microsoftazure.de` | Доступно |
| Azure China 21Vianet | `https://vault.azure.cn` | Доступно |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://datalake.azure.net/` | Доступно |
| Azure для государственных организаций |  | Недоступен |
| Azure для Германии |   | Недоступен |
| Azure China 21Vianet |  | Недоступен |

### <a name="azure-sql"></a>Azure SQL 

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://database.windows.net/` | Доступно |
| Azure для государственных организаций | `https://database.usgovcloudapi.net/` | Доступно |
| Azure для Германии | `https://database.cloudapi.de/` | Доступно |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Доступно |

### <a name="azure-event-hubs"></a>Центры событий Azure

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://eventhubs.azure.net` | Доступно |
| Azure для государственных организаций |  | Недоступен |
| Azure для Германии |   | Недоступен |
| Azure China 21Vianet |  | Недоступен |

### <a name="azure-service-bus"></a>Служебная шина Azure

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://servicebus.azure.net`  | Доступно |
| Azure для государственных организаций |  | Доступно |
| Azure для Германии |   | Недоступен |
| Azure China 21Vianet |  | Недоступен |

### <a name="azure-storage-blobs-and-queues"></a>Большие двоичные объекты и очереди службы хранилища Azure

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Доступно |
| Azure для государственных организаций | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Доступно |
| Azure для Германии | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Доступно |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Доступно |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Облако | ИД ресурса | Status |
|--------|------------|--------|
| Azure (глобальный) | `https://*.asazure.windows.net` | Доступно |
| Azure для государственных организаций | `https://*.asazure.usgovcloudapi.net` | Доступно |
| Azure для Германии | `https://*.asazure.cloudapi.de` | Доступно |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Доступно |
