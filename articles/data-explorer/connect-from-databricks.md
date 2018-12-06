---
title: Подключение к Azure Data Explorer из Azure Databricks с помощью Python
description: Здесь показано, как использовать библиотеку Python в Azure Databricks для доступа к данным из Azure Data Explorer (ADX) с помощью одного из двух методов проверки подлинности.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429929"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Подключение к Azure Data Explorer из Azure Databricks с помощью Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) — это платформа аналитики на основе Apache Spark, оптимизированная для платформы облачных служб Microsoft Azure. В этой статье показано, как использовать библиотеку Python в Azure Databricks для доступа к данным из Azure Data Explorer (ADX). Существует несколько способов выполнения проверки подлинности в ADX, включая имя пользователя устройства и приложение Azure Active Directory (Azure AD) App.

## <a name="prerequisites"></a>Предварительные требования

- [Создайте кластер и базу данных Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
- [Создайте рабочую область Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace).

    В разделе **Служба Azure Databricks** в раскрывающемся списке **Ценовая категория** выберите **Премиум**. В этой категории вы сможете использовать секреты Azure Databricks для хранения учетных данных и ссылаться на них в записных книжках и заданиях.

- [Создайте кластер](https://docs.azuredatabricks.net/user-guide/clusters/create.html) в Azure Databricks со следующими спецификациями (минимальные параметры, необходимые для запуска примеров записных книжек):

![Создание кластера](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Установка библиотеки Python в кластере Azure Databricks

Для установки [библиотеки Python](/azure/kusto/api/python/kusto-python-client-library) в кластере Azure Databricks:

1. Перейдите в рабочую область Azure Databricks и [создайте библиотеку](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Передайте пакет Python PyPI или Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
    - Загрузите, установите и подключите библиотеку к кластеру Databricks.
    - Введите имя PyPI: *azure-kusto-data*.

## <a name="connect-to-adx-using-device-login"></a>Подключение к ADX с использованием имени пользователя устройства

[Импортируйте записную книжку](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook), используя экземпляр [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb), для подключения к ADX, используя свои учетные данные.

## <a name="connect-to-adx-using-azure-ad-app"></a>Подключение к ADX с помощью Azure AD App

1. Создайте Azure AD App, выполнив [подготовку приложения AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Предоставьте доступ к Azure AD App в вашей базе данных Azure Data Explorer следующим образом:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | имя базы данных |
    | ```AAD App ID``` | идентификатор Azure AD App |
    | ```AAD Tenant ID``` | идентификатор клиента Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Поиск идентификатора клиента Azure AD

Чтобы проверить подлинность приложения, Azure Data Explorer использует идентификатор клиента Azure AD. Чтобы найти идентификатор клиента, используйте следующий URL-адрес, заменив домен на *имя_вашего_домена*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Например, если ваш домен называется *contoso.com*, URL-адрес будет следующим: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Щелкните этот URL-адрес, чтобы просмотреть результаты. Первая строка выглядит следующим образом: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ваш идентификатор клиента — `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Хранение и защита идентификатора и ключа Azure AD App 

Храните и защищайте идентификатор и ключ Azure AD App с помощью [секретов](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks следующим образом:
1. [Настройте интерфейс командной строки](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Установите интерфейс командной строки](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Настройте проверку подлинности](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Настройте [секреты](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) с помощью приведенных ниже примеров команд:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Импорт записной книжки
[Импортируйте записную книжку](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook), используя экземпляр [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb), для подключения к ADX. Замените значения заполнителей собственными именами кластера и базы данных, а также идентификатором клиента Azure AD.