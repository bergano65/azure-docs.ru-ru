---
title: Пример сценария Azure CLI. Создание преобразования | Документация Майкрософт
description: Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом"). В этой статье показано, как создать преобразование с помощью сценария Azure CLI.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 365c6a6a10ee79d96c1054416669e84c5392344c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092173"
---
# <a name="cli-example-create-a-transform"></a>Пример CLI. Создание преобразования

В этой статье показано, как создать преобразование с помощью сценария Azure CLI. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом"). Всегда проверяйте, существует ли уже "рецепт" и преобразование с нужным именем. Если да, используйте их.

## <a name="prerequisites"></a>предварительные требования 

[Создание учетной записи Служб мультимедиа](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Вы можете указать путь к пользовательскому JSON-файлу предустановок кодировщика ценовой категории "Стандартный" в параметре [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) (см. пример [кодирования с помощью пользовательского преобразования](custom-preset-cli-howto.md)).
>
> При использовании [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) нельзя передать имя файла.

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Дальнейшие действия

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
