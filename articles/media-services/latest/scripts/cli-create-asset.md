---
title: Пример скрипта Azure CLI. Создание ресурса Служб мультимедиа Azure | Документация Майкрософт
description: В скрипте Azure CLI в этой статье показано, как создать ресурс Cлужб мультимедиа для отправки в него содержимого.
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 76d2adf3c408e174081c3106e07a87f751b4a179
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612667"
---
# <a name="cli-example-create-an-asset"></a>Пример использования CLI. Создание ресурса

В скрипте Azure CLI в этой статье показано, как создать ресурс Cлужб мультимедиа для отправки в него содержимого.

## <a name="prerequisites"></a>Предварительные требования 

- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.

- [Создание учетной записи Служб мультимедиа](../create-account-cli-how-to.md).

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
