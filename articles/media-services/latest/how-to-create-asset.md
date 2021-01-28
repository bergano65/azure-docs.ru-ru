---
title: Отправка содержимого в интерфейс командной строки актива
description: В скрипте Azure CLI в этой статье показано, как создать ресурс Cлужб мультимедиа для отправки в него содержимого.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6f3c5fa41150f5df2b0e89c4253cb0bb55b1d625
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955823"
---
# <a name="create-an-asset"></a>Создание ресурса

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

В этой статье описывается, как создать ресурс Служб мультимедиа.  Вы будете использовать ресурс для хранения мультимедийного содержимого для кодирования и потоковой передачи данных.  Дополнительные сведения о ресурсах Служб мультимедиа см. в статье [Ресурсы в Службах мультимедиа Azure версии 3](assets-concept.md).

## <a name="prerequisites"></a>Предварительные требования

Выполните инструкции из статьи [Создание учетной записи Служб мультимедиа](./create-account-howto.md), чтобы создать учетную запись Служб мультимедиа и группу ресурсов для создания ресурса.

## <a name="methods"></a>Методы

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Использование REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Использование cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Использование Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения о службах мультимедиа](media-services-overview.md)
