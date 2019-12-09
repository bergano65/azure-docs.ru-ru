---
title: Примеры шаблонов Azure Resource Manager
description: В этой статье приведены примеры шаблонов Azure Resource Manager для некоторых распространенных сценариев Службы приложений. Узнайте, как автоматизировать задачи развертывания и управления в Службе приложений.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 58871035eac89931dce0c1cd289cf3fab97c0c3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688420"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Шаблоны Azure Resource Manager для Службы приложений

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для Службы приложений Azure. Рекомендации по предотвращению распространенных ошибок при создании шаблонов приложений см. в статье [Руководство по развертыванию веб-приложений с помощью шаблонов Azure Resource Manager](deploy-resource-manager-template.md).

Синтаксис и свойства JSON для ресурсов Службы приложений см. в статье о [типах ресурсов Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Развертывание приложения**||
| [План службы приложений и базовое приложение Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Развертывает приложение Службы приложений, настроенное для Linux. |
| [План службы приложений и базовое приложение Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Развертывает приложение Службы приложений, настроенное для Windows. |
| [Приложение, связанное с репозиторием GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Развертывает приложение Службы приложений Azure, которое вытягивает код из GitHub. |
| [Приложение с пользовательскими слотами развертывания](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Развертывает приложение Службы приложений с пользовательскими слотами развертывания или средами. |
|**Настройка приложения**||
| [Сертификат приложения из Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Развертывает сертификат приложения Службы приложений из секрета Azure Key Vault и использует его для привязки SSL. |
| [Приложение с личным доменом](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Развертывает приложение Службы приложений с пользовательским именем узла. |
| [Приложение с личным доменом и SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Развертывает приложение Службы приложений с пользовательским именем узла и получает сертификат приложения из Key Vault для привязки SSL. |
| [Приложение с расширением GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Развертывает приложение Службы приложений с расширением сайта Golang. Вы сможете запускать веб-приложения, разработанные на Golang в Azure. |
| [Приложения с Java 8 и Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Развертывает приложение Службы приложений с поддержкой Java 8 и Tomcat 8. Вы сможете запускать приложения Java в Azure. |
|**Приложение с подключенными ресурсами**||
| [Приложение в Linux с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Развертывает приложение Службы приложений в Linux со службой "База данных Azure для MySQL". |
| [Приложения в Linux с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Развертывает приложение Службы приложений в Linux со службой "База данных Azure для PostgreSQL". |
|**Приложение с подключенными ресурсами**||
| [Приложение с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Развертывает приложение Службы приложений в Windows со службой "База данных Azure для MySQL". |
| [Приложение с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Развертывает приложение Службы приложений в Windows со службой "База данных Azure для PostgreSQL". |
| [Приложение с базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Развертывает приложение Службы приложений и базу данных SQL с уровнем обслуживания "Базовый". |
| [Приложение с подключением к хранилищу BLOB-объектов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Развертывает приложение Службы приложений со строкой подключения к хранилищу BLOB-объектов Azure. Вы сможете использовать хранилище BLOB-объектов из приложения. |
| [Приложение с кэшем Azure для Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Развертывает приложение Службы приложений с кэшем Azure для Redis. |
|**Среда службы приложений для PowerApps**||
| [Создание среды службы приложений версии 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Позволяет создать среду службы приложений версии 2 в виртуальной сети. |
| [Создание среды службы приложений версии 2 с адресом ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Позволяет создать среду службы приложений версии 2 в виртуальной сети с адресом частной внутренней подсистемы балансировки нагрузки. |
| [Настройка SSL-сертификата по умолчанию для среды службы приложений ILB версии 1 или 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Позволяет настроить SSL-сертификат по умолчанию для среды службы приложений ILB версии 1 или 2. |
| | |
