---
title: Пример скрипта Azure CLI для создания подписки на службу "Сетка событий Azure" | Документация Майкрософт
description: В скрипте Azure CLI в этой статье показано, как создать подписку на службу "Сетка событий" уровня учетной записи для изменений состояния задания.
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
ms.openlocfilehash: a3cff649001adf569f1454d16a2a97b32972ef00
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612631"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Пример для Azure CLI. Создание подписки на службу "Сетка событий Azure" 

В скрипте Azure CLI в этой статье показано, как создать подписку на службу "Сетка событий" уровня учетной записи для изменений состояния задания.

## <a name="prerequisites"></a>Предварительные требования 

- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.

- [Создание учетной записи Служб мультимедиа](../create-account-cli-how-to.md).

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
