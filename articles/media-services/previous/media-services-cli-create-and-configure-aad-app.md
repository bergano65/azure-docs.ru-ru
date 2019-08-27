---
title: Создание приложения Azure AD и его настройка для доступа к API Служб мультимедиа Azure с помощью Azure CLI | Документация Майкрософт
description: В этой статье описывается, как создать и настроить приложение Azure AD для доступа к API Служб мультимедиа Azure с помощью Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035813"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Создание и настройка приложения Azure AD для доступа к API Служб мультимедиа с помощью Azure CLI 

> [!NOTE]
> В Cлужбы мультимедиа версии 2 больше не добавляются новые компоненты или функциональные возможности. <br/>Ознакомьтесь с новейшей версией Служб мультимедиа — [версией 3](https://docs.microsoft.com/azure/media-services/latest/). См. также [руководство по миграции из v2 в версии 3](../latest/migrate-from-v2-to-v3.md) .

В этой статье показано, как с помощью Azure CLI создать приложение Azure Active Directory (Azure AD) и субъект-службу для доступа к ресурсам Служб мультимедиа Azure. 

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure. Дополнительные сведения см. на странице с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Учетная запись служб мультимедиа. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Использование Azure Cloud Shell

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Запустите Cloud Shell с помощью верхней области навигации портала.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Дополнительные сведения см. в [обзоре Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Создание приложения Azure AD и настройка доступа к учетной записи Служб мультимедиа с помощью Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Пример:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

В этом примере **область** — полный путь к ресурсу учетной записи служб мультимедиа. Однако **область** может быть задана на любом уровне.

Например, ее можно задать на одном из следующих уровней:
 
* **подписка**;
* **группа ресурсов**;
* **ресурс** (например, учетная запись служб мультимедиа).

Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli).

Ознакомьтесь также с разделом [Управление доступом на основе ролей с помощью интерфейса командной строки Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Следующие шаги

Приступите к [передаче файлов в учетную запись](media-services-portal-upload-files.md).
