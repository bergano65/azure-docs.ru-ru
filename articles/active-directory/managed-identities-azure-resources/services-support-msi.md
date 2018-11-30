---
title: Службы Azure с поддержкой управляемых удостоверений для ресурсов Azure
description: Список служб, которые поддерживают управляемые удостоверения для ресурсов Azure и аутентификацию Azure AD.
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 5ff03a4a2aab796084ce726114c2f4b556fd44b1
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497962"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Службы с поддержкой управляемых удостоверений для ресурсов Azure

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Управляемое удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. Мы работаем над интеграцией управляемых удостоверений для ресурсов Azure и аутентификации Azure AD на платформе Azure. Почаще проверяйте наличие обновлений.

> [!NOTE]
> Управляемые удостоверения для ресурсов Azure — это новое название службы "Управляемое удостоверение службы" (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Службы Azure с поддержкой управляемых удостоверений для ресурсов Azure

Ниже приведены службы Azure с поддержкой управляемых удостоверений для ресурсов Azure.

| Service | Назначение системой | Назначение пользователем| Настройка | Получение маркера |
| ------- | ------ | ---- | --------- | ----------- |
| Виртуальные машины Azure | Доступна | Предварительный просмотр | [портал Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Интерфейс командной строки Azure](qs-configure-cli-windows-vm.md)<br>[Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[GO](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Масштабируемые наборы виртуальных машин Microsoft Azure | Доступна | Предварительный просмотр | [портал Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Интерфейс командной строки Azure](qs-configure-cli-windows-vmss.md)<br>[Шаблоны диспетчера ресурсов Azure](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[GO](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Служба приложений Azure | Windows (доступно) <br> Linux: предварительная версия | Предварительный просмотр | [портал Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Интерфейс командной строки Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Шаблон Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Функции Azure | Доступна | Предварительный просмотр | [портал Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Интерфейс командной строки Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Шаблон Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Logic Apps | Доступна | Недоступно | [портал Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Шаблон Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Фабрика данных Azure версии 2 | Доступна | Недоступно | [портал Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Пакет SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Cлужба управления Azure API  | Доступна | Недоступно | [Шаблон Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Экземпляры контейнеров Azure | Linux: предварительная версия<br>Windows: недоступно | Linux: предварительная версия<br>Windows: недоступно | [Интерфейс командной строки Azure](~/articles/container-instances/container-instances-managed-identity.md)<br>[Шаблон Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Службы Azure, поддерживающие аутентификацию Azure AD

Ниже приведены службы, поддерживающие аутентификацию Azure AD, которые были проверены с помощью служб клиента, использующих управляемые удостоверения для ресурсов Azure.

| Service | Идентификатор ресурса | Status | Дата | Назначение доступа |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Доступна | Сентябрь 2017 г. | [портал Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Интерфейс командной строки Azure](howto-assign-access-CLI.md) |
| Хранилище ключей Azure | `https://vault.azure.net` | Доступна | Сентябрь 2017 г. | |
| Azure Data Lake; | `https://datalake.azure.net/` | Доступна | Сентябрь 2017 г. | |
| Azure SQL | `https://database.windows.net/` | Доступна | Октябрь 2017 г. | |
| Центры событий Azure | `https://eventhubs.azure.net` | Предварительный просмотр | Декабрь 2017 г. | |
| Azure Service Bus | `https://servicebus.azure.net` | Предварительный просмотр | Декабрь 2017 г. | |
| Хранилище Azure | `https://storage.azure.com/` | Предварительный просмотр | Май 2018 г. | |