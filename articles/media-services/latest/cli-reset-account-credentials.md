---
title: Пример сценария Azure CLI. Сброс учетных данных учетной записи | Документация Майкрософт
description: Используйте сценарий Azure CLI, что сбросить учетные данные учетной записи и вернуть параметры app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 63f2abe7a3890efbaf4c79186467a3eb20d8afda
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382993"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Пример для Azure CLI: Сбросьте данные учетной записи

В сценарии Azure CLI в этой статье показано, как сбросить учетные данные учетной записи и вернуть параметры app.config.

## <a name="prerequisites"></a>Предварительные требования

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Пример сценария

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Дальнейшие действия

* [az ams](/cli/azure/ams)
* [Сброс учетных данных](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
