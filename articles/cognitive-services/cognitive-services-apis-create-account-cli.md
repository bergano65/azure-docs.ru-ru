---
title: Создание ресурса когнитивных служб с помощью ClI Azure
titleSuffix: Azure Cognitive Services
description: Начинайте с Azure Cognitive Services, создавая и подписываясь на ресурс с помощью интерфейса командной строки Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219613"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Создание ресурса когнитивных служб с помощью интерфейса командования azure (CLI)

Используйте этот быстрый запуск, чтобы начать работу с Azure Cognitive Services с помощью [интерфейса командной строки Azure (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Когнитивные службы представлены [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, которые создаются в подписке Azure. После создания ресурса используйте ключи и конечную точку, созданные для проверки подлинности приложений. 


В этом быстром запуске вы узнаете, как подписаться на Azure Cognitive Services и создать учетную запись с подпиской на одну службу или многофункционную службу с помощью [интерфейса командной строки Azure (CLI).](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Эти службы представлены [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)Azure, которые позволяют подключиться к одному или несколько AAP когнитивных служб Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Предварительные требования

* Действительная подписка Azure - [Создайте ее](https://azure.microsoft.com/free/) бесплатно.
* [Интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Установите Azure CLI и вопийте 

Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Чтобы войти в локальную установку CLI, запустите команду [входа в az:](https://docs.microsoft.com/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

Вы также можете использовать зеленую кнопку **Try It** для выполнения этих команд в вашем браузере.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Создание новой группы ресурсов когнитивных служб Azure

Перед созданием ресурса Cognitive Services необходимо создать группу ресурсов Azure для хранения ресурса. При создании нового ресурса у вас есть возможность либо создать новую группу ресурсов, либо использовать существующую. В этой статье показано, как создать новую группу ресурсов.

### <a name="choose-your-resource-group-location"></a>Выберите местоположение группы ресурсов

Для создания ресурса потребуется одно из местоположений Azure, доступных для подписки. Вы можете получить список доступных местоположений с командой [список-местоположений аз-списка.](/cli/azure/account#az-account-list-locations) К большинству когнитивных служб можно получить доступ из нескольких мест. Выберите ближайший к вам или посмотрите, какие места доступны для службы.

> [!IMPORTANT]
> * Помните о своем местоположении Azure, так как оно вам понадобится при вызове когнитивных служб Azure.
> * Доступность некоторых когнитивных услуг может варьироваться в зависимости от региона. Для получения дополнительной информации смотрите [продукты Azure по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

После того, как у вас есть расположение azure, создайте новую группу ресурсов в Azure CLI с помощью команды [группы az.](/cli/azure/group#az-group-create)

В приведенном ниже примере `westus2` замените расположение azure одним из мест, доступных для подписки, в Azure.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Выберите когнитивный сервис и ценовой уровень

При создании нового ресурса необходимо знать "вид" службы, которую вы хотите использовать, а также [уровень ценообразования](https://azure.microsoft.com/pricing/details/cognitive-services/) (или sku), который вы хотите использовать. Вы будете использовать эту и другую информацию в качестве параметров при создании ресурса.

### <a name="multi-service"></a>Несколько служб

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Несколько услуг. Более подробную информацию можно узнать на странице [ценообразования.](https://azure.microsoft.com/pricing/details/cognitive-services/)            | `CognitiveServices`     |


> [!NOTE]
> Многие из когнитивных служб ниже имеют бесплатный уровень, который вы можете использовать, чтобы попробовать службу. Чтобы использовать свободный `F0` уровень, используйте в качестве sku для вашего ресурса.

### <a name="vision"></a>Зрение

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| API Компьютерного зрения            | `ComputerVision`          |
| Пользовательское видение - Прогнозирование | `CustomVision.Prediction` |
| Пользовательское видение - Обучение   | `CustomVision.Training`   |
| Распознавание лиц                       | `Face`                    |
| Распознаватель документов            | `FormRecognizer`          |
| Распознаватель рукописного текста             | `InkRecognizer`           |

### <a name="search"></a>Поиск

| Служба            | Вид                  |
|--------------------|-----------------------|
| API Автозаполнения Bing   | `Bing.Autosuggest.v7` |
| Пользовательский поиск Bing | `Bing.CustomSearch`   |
| API Поиска сущностей Bing | `Bing.EntitySearch`   |
| Поиск Bing        | `Bing.Search.v7`      |
| API Проверки орфографии Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Речь

| Служба            | Вид                 |
|--------------------|----------------------|
| Службы "Речь"    | `SpeechServices`     |
| Распознавание речи | `SpeakerRecognition` |

### <a name="language"></a>Язык

| Служба            | Вид                |
|--------------------|---------------------|
| Понимание формы | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Анализ текста     | `TextAnalytics`     |
| Перевод текста   | `TextTranslation`   |

### <a name="decision"></a>Решение

| Служба           | Вид               |
|-------------------|--------------------|
| Детектор аномалий  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Персонализатор      | `Personalizer`     |

Вы можете найти список доступных "видов когнитивной службы" с командой [список-виды списка когнитивных услуг az:](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Добавление нового ресурса в группу ресурсов

Чтобы создать и подписаться на новый ресурс Cognitive Services, используйте учетную запись [az cognitiveservices, создавая](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) команду. Эта команда добавляет новый ресурс, пригодный для выставления счетов, в группу ресурсов, созданную ранее. При создании нового ресурса необходимо знать "вид" службы, которую вы хотите использовать, а также его ценовой уровень (или sku) и местоположение Azure:

Можно создать ресурс F0 (бесплатный) для детектора аномалий, названный `anomaly-detector-resource` с командой ниже.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Получите ключи для вашего ресурса

Чтобы войти в локальную установку интерфейса Командно-линийного интерфейса (CLI), используйте команду [входа в систему az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Используйте команду [списка ключей учетной записи az cognitiveservices,](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) чтобы получить ключи для вашего ресурса Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Уровни ценообразования и выставления счетов

Уровни ценообразования (и сумма, которую вы получаете счет) основаны на количестве транзакций, которые вы отправляете с использованием вашей информации о проверке подлинности. Каждый ценовой уровень определяет:
* максимальное количество разрешенных транзакций в секунду (TPS).
* функции обслуживания, включенные в рамках уровня ценообразования.
* Стоимость заранее определенного количества транзакций. Переход выше этой суммы вызовет дополнительную плату, как указано в [ценовых деталях](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) для вашего сервиса.

## <a name="get-current-quota-usage-for-your-resource"></a>Получите текущее использование квот для вашего ресурса

Используйте команду по [использованию списка учетных](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) записей az cognitiveservices, чтобы получить использование ресурса Cognitive Service.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить ресурс Cognitive Services, вы можете удалить его или группу ресурсов. Удаление группы ресурсов также удаляет любые другие ресурсы, содержащиеся в группе.

Чтобы удалить группу ресурсов и связанные с ней ресурсы, используйте команду удаления группы az.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>См. также

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Что такое когнитивные службы Azure?](Welcome.md)
* [Поддержка естественного языка](language-support.md)
* [Поддержка контейнеров Docker](cognitive-services-container-support.md)
