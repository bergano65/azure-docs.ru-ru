---
title: Настройка управляемых идентификаторов с помощью конфигурации приложений Azure
description: Узнайте, как работают управляемые идентификаторы в конфигурации приложений Azure и как настроить управляемую идентификацию
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623027"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Как использовать управляемые идентификаторы для конфигурации приложений Azure

Эта тема показывает, как создать управляемый итик для конфигурации приложений Azure. Управляемый имитизм от Azure Active Directory (AAD) позволяет конфигурации приложений Azure легко получить доступ к другим защищенным AAD ресурсам, таким как Azure Key Vault. Идентификация управляется платформой Azure. Он не требует от вас предоставления или ротации каких-либо секретов. Дополнительные сведения об управляемых удостоверениях в Azure Active Directory см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Приложению можно предоставить два типа удостоверений:

- **Идентификационная личность, назначенная системой,** привязана к магазину конфигурации. Он удаляется, если ваш магазин конфигурации удален. Магазин конфигурации может иметь только одну системную идентификацию.
- **Идентификационная личность, назначенная пользователем,** является автономным ресурсом Azure, который может быть назначен в хранилище конфигураций. Магазин конфигурации может иметь несколько идентификаторов, назначенных пользователем.

## <a name="adding-a-system-assigned-identity"></a>Добавление назначаемого системой удостоверения

Создание магазина конфигурации приложений с установленным системой удостоверением требует дополнительного свойства, установленного на магазине.

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Чтобы настроить управляемую идентификацию с помощью Azure CLI, используйте команду [идентификации az appconfig,] присваивающую команду существующему магазину конфигураций. Примеры из этого раздела можно выполнять тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure;
- Используйте встроенную облачную оболочку Azure через кнопку "Попробуй это", расположенную в правом верхнем углу каждого блока кода ниже.
- [Установите последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 или более поздно), если вы предпочитаете использовать локальную консоль CLI.

Следующие шаги будут проходить через создание магазина конфигурации приложений и присвоение ему идентификатор с помощью CLI:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login]. Используйте учетную запись, связанную с подпиской Azure:

    ```azurecli-interactive
    az login
    ```

1. Создайте магазин конфигурации приложений с помощью CLI. Для получения дополнительных примеров использования CLI с конфигурацией приложений Azure, [см.](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Выполнить команду [идентификации az appconfig] для создания установленного системой иждивенного значения для этого хранилища конфигурации:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Добавление удостоверения, назначенного пользователем

Создание магазина конфигурации приложений с удостоверением, назначенным пользователем, требует создания идентификатора, а затем присвоения идентификатора ресурса вашему магазину.

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Чтобы настроить управляемую идентификацию с помощью Azure CLI, используйте команду [идентификации az appconfig,] присваивающую команду существующему магазину конфигураций. Примеры из этого раздела можно выполнять тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure;
- Используйте встроенную облачную оболочку Azure через кнопку "Попробуй это", расположенную в правом верхнем углу каждого блока кода ниже.
- [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 или выше), если вы предпочитаете использовать локальную консоль CLI.

Следующие шаги будут проходить через создание пользователя назначенных удостоверения и App Configuration магазин, а затем назначение идентификатора в магазин с помощью CLI:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login]. Используйте учетную запись, связанную с подпиской Azure:

    ```azurecli-interactive
    az login
    ```

1. Создайте магазин конфигурации приложений с помощью CLI. Для получения дополнительных примеров использования CLI с конфигурацией приложений Azure, [см.](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Создайте удостоверение, `myUserAssignedIdentity` назначенное пользователем, называемое с помощью CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    В выводе этой команды обратите внимание `id` на значение свойства.

1. Выполнить [итоговую идентификацию az appconfig] для присвоения нового установленного пользователем удостоверения в этом хранилище конфигураций. Используйте значение `id` свойства, которое вы отметили на предыдущем этапе.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Удаление удостоверения

Идентификационная личность, назначенная системой, может быть удалена путем отключения функции с помощью [итоговой идентификации az appconfig, удаленной](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) в Azure CLI. Назначаемые пользователем удостоверения можно удалить по отдельности. Такое удаление назначаемого системой удостоверения приводит к удалению из AAD. Назначаемые системой удостоверения также удаляются из AAD автоматически, когда удаляется ресурс приложения.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создайте приложение ASP.NET core с конфигурацией приложений Azure](quickstart-aspnet-core-app.md)

[az appconfig определение идентичности]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[аз-логин]: /cli/azure/reference-index#az-login
