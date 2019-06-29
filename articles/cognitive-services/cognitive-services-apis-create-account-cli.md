---
title: Создание учетной записи Cognitive Services с помощью Azure CLI
titlesuffix: Azure Cognitive Services
description: Как создать учетную запись Azure Cognitive Services, интерфейсы API, с помощью Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454898"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Создание учетной записи Cognitive Services, с помощью Interface(CLI) командной строки Azure

В этом кратком руководстве вы узнаете, как зарегистрироваться в Azure Cognitive Services и создать учетную запись с одной службой или нескольких служб подписки, использование [Interface(CLI) командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Эти службы, представляются Azure [ресурсы](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), которые используются для подключения к одному или нескольким API-интерфейсов Cognitive Services Azure.

## <a name="prerequisites"></a>Технические условия

* Действующая подписка Azure. [Создайте учетную запись](https://azure.microsoft.com/free/) бесплатно.
* [Interface(CLI) командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Установите Azure CLI и войдите в систему 

Установка [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Чтобы войти в локальную установку CLI, выполните [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) команды:

```console
az login
```

Можно также использовать зеленую **попробовать** кнопки для выполнения этих команд в браузере.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Создать новую группу ресурсов Azure Cognitive Services

Подписки будут Cognitive Services представлены ресурсы Azure. Каждая учетная запись Cognitive Services (и его связанных ресурсов Azure) должны принадлежать к группе ресурсов Azure.

### <a name="choose-your-resource-group-location"></a>Выберите расположение группы ресурсов

Чтобы создать ресурс, вам потребуется один из доступных расположений Azure для вашей подписки. Можно получить список доступных расположений с [az учетной записи list-locations](/cli/azure/account#az_account_list) команды. Большинство Cognitive Services может осуществляться из нескольких мест. Выберите из них, ближайший к вам, или узнать, какие расположения доступны для службы.

> [!IMPORTANT]
> * Помните ваше расположение Azure, так как он потребуется при вызове Azure Cognitive Services.
> * Доступность некоторых Cognitive Services могут зависеть от региона. Дополнительные сведения см. в разделе [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Добавив ваше расположение azure, создать новую группу ресурсов в Azure CLI с помощью [Создание группы az](/cli/azure/group#az_group_create) команды.

В следующем примере замените расположение azure `westus2` с помощью нескольких местоположениях Azure, доступных для вашей подписки.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Выберите ценовую категорию и cognitive service

При создании нового ресурса, необходимо знать «kind» службы, которые вы хотите использовать, наряду с [ценовой](https://azure.microsoft.com/pricing/details/cognitive-services/) (или номер sku) вы хотите. Будет использовать это и другие сведения в качестве параметров при создании ресурса.

> [!NOTE]
> Многие Cognitive services есть бесплатный уровень, которые можно использовать в эту службу. Чтобы использовать уровень "бесплатный", используйте `F0` номер SKU для ресурса.

### <a name="vision"></a>Зрение

| Service                    | Вид                      |
|----------------------------|---------------------------|
| Компьютерное зрение            | `ComputerVision`          |
| Custom Vision - прогноза | `CustomVision.Prediction` |
| Настройка визуального распознавания - обучение   | `CustomVision.Training`   |
| API Распознавания лиц                   | `Face`                    |
| Распознаватель документов            | `FormRecognizer`          |
| Распознаватель рукописного текста             | `InkRecognizer`           |

### <a name="search"></a>поиска

| Service            | Вид                  |
|--------------------|-----------------------|
| API Автозаполнения Bing   | `Bing.Autosuggest.v7` |
| Пользовательский поиск Bing | `Bing.CustomSearch`   |
| API Поиска сущностей Bing | `Bing.EntitySearch`   |
| Поиск Bing        | `Bing.Search.v7`      |
| API Проверки орфографии Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Речь

| Service            | Вид                 |
|--------------------|----------------------|
| Службы "Речь"    | `SpeechServices`     |
| Распознавание речи | `SpeakerRecognition` |

### <a name="language"></a>Язык

| Service            | Вид                |
|--------------------|---------------------|
| Основные сведения о форме | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Анализ текста     | `TextAnalytics`     |
| Перевод текста   | `TextTranslation`   |

### <a name="decision"></a>Решение

| Service           | Вид               |
|-------------------|--------------------|
| Детектор аномалий  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Персонализатор      | `Personalizer`     |

Список доступных Cognitive Service можно найти «типы» с [az cognitiveservices учетной записи списка типов](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) команды:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Добавьте новый ресурс в группу ресурсов

Чтобы создать и Подпишитесь на новый ресурс Cognitive Services, используйте [создать учетную запись cognitiveservices az](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) команды. Эта команда добавляет новый ресурс оплачиваемых ранее созданной группе ресурсов. При создании нового ресурса, необходимо знать «kind» службы, которые вы хотите использовать, а также его или ценовой категории (sku) и расположение Azure:

Вы можете создать ресурс F0 (бесплатно) для обнаружения аномалий, с именем `anomaly-detector-resource` , выполнив следующую команду.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Получить ключи для вашей подписки

Чтобы войти в локальную установку из командной строки Interface(CLI), используйте [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) команды.

```console
az login
```

Используйте [az cognitiveservices учетной записи списка ключей](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) команду, чтобы получить ключи для ресурса Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, можно удалить ресурс или группу ресурсов. Удаление группы ресурсов также удаляет другие ресурсы, связанные с группой ресурсов.

Чтобы удалить группу ресурсов и его связанные ресурсы, включая новую учетную запись хранения, используйте команду az group delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>См. также

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Что такое Azure Cognitive Services?](Welcome.md)
* [Поддержка естественного языка](language-support.md)
* [Поддержка контейнеров docker](cognitive-services-container-support.md)
