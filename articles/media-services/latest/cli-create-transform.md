---
title: Пример сценария Azure CLI. Создание преобразования | Документация Майкрософт
description: Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом"). В этой статье показано, как создать преобразование с помощью сценария Azure CLI.
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
ms.openlocfilehash: 0e85116aaad1eecbe137ae3e470811b31d1a855f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295977"
---
# <a name="cli-example-create-a-transform"></a>Пример CLI. Создание преобразования

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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
