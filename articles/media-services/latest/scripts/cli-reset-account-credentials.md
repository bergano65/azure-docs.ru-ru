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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 1c70441ea5b35a55ba39f934e74a6512d783fcf0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615394"
---
# <a name="cli-example-reset-the-account-credentials"></a>Пример CLI: сброс учетных данных учетной записи

В сценарии Azure CLI в этой статье показано, как сбросить учетные данные учетной записи и вернуть параметры app.config.

## <a name="prerequisites"></a>Предварительные требования 

- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.

- [Создание учетной записи Служб мультимедиа](../create-account-cli-how-to.md).

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
