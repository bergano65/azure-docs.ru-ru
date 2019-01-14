---
title: Использование CLI для создания фильтров с помощью служб мультимедиа Azure | Документация Майкрософт
description: В этом разделе показано, как использовать CLI для создания фильтров с помощью служб мультимедиа.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000528"
---
# <a name="creating-filters-with-cli"></a>Создание фильтров с помощью CLI 

При доставке содержимого пользователям (потоковой трансляции мероприятий или видео по запросу) клиенту может потребоваться больше возможностей, чем описано в файле манифеста ресурса по умолчанию. Службы мультимедиа Azure позволяют определять фильтры учетной записи и фильтры ресурсов для содержимого. Чтобы узнать больше, ознакомьтесь со [Фильтры и динамические манифесты](filters-dynamic-manifest-overview.md).

В этой статье показано, как настроить фильтр для ресурса-контейнера видео по запросу и использовать CLI для Служб мультимедиа версии 3, чтобы создать [фильтры учетных записей](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) и [фильтры ресурсов](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Предварительные требования 

- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 
- См. дополнительные сведения о [фильтрах и динамических манифестах](filters-dynamic-manifest-overview.md).

## <a name="define-a-filter"></a>Определение фильтра 

В следующем примере определяются условия выбора дорожки, которые добавляются в манифест. Этот фильтр включает любые аудиодорожки в формате EC3 и любые видеодорожки со скоростью в диапазоне 0–1 000 000.

Фильтры, определенные в REST, включают объект JSON программы-оболочки для указания свойств.  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Создание фильтров учетной записи

Следующая команда [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) создает фильтр учетной записи с выбранными дорожками для отслеживания, которые были [определены ранее](#define-a-filter). 

Команде можно передать необязательный параметр `--tracks`, который содержит код JSON, представляющий выбранные элементы для отслеживания.  Чтобы загрузить JSON из файла, используйте синтаксис @{файл}. Если вы используете Azure CLI локально, укажите путь к файлу полностью.


```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Если вы используете Azure Cloud Shell, отправьте файл в Cloud Shell (для этого найдите кнопки для отправки и скачивания файлов в верхней части окна оболочки). После этого можно сослаться на файл следующим образом.

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Создание фильтров ресурсов

Следующая команда [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) создает фильтр ресурса с выбранными дорожками для отслеживания, которые были [определены ранее](#define-a-filter). 

> [!TIP]
> См. сведения об указании расположения имени файла в предыдущем разделе.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Также см. [примеры JSON для фильтров](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Дальнейшие действия

[Потоковая передача видео](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
