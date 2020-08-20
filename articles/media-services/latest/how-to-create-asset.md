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
ms.openlocfilehash: 4d1e5e48e70cee1cf75b1d6605837695d26dcbf0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608905"
---
# <a name="create-an-asset"></a>Создание ресурса

В этой статье показано, как создать ресурс служб мультимедиа.  Вы будете использовать ресурс для хранения мультимедийного содержимого для кодирования и потоковой передачи.  Дополнительные сведения о ресурсах служб мультимедиа см. [в статье ресурсы в службах мультимедиа Azure v3](assets-concept.md) .

## <a name="prerequisites"></a>Предварительные условия

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

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения о службах мультимедиа](media-services-overview.md)
