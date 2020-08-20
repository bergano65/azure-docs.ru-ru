---
title: Отправка содержимого в файл мультимедиа в Службе мультимедиа Azure с помощью Azure CLI
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
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b40c936006fa47964ef67ffda37c80eb5732bdf0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653902"
---
# <a name="create-an-asset"></a>Создание ресурса

В этой статье показано, как создать ресурс служб мультимедиа.  Вы будете использовать ресурс для хранения мультимедийного содержимого для кодирования и потоковой передачи.  Дополнительные сведения о ресурсах служб мультимедиа см. [в статье ресурсы в службах мультимедиа Azure v3](assets-concept.md) .

## <a name="prerequisites"></a>Предварительные требования

Выполните действия, описанные в разделе [Создание учетной записи служб мультимедиа](./create-account-howto.md) , чтобы создать необходимую учетную запись служб мультимедиа и группу ресурсов для создания ресурса.

## <a name="methods"></a>Методы

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="cli-shell"></a>[Оболочка CLI](#tab/clishell/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

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
