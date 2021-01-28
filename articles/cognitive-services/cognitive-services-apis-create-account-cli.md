---
title: Создание ресурса Cognitive Services с помощью интерфейса командной строки Azure
titleSuffix: Azure Cognitive Services
description: Приступая к работе с Azure Cognitive Services, создав и подпишитесь на ресурс с помощью интерфейса командной строки Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: Cognitive Services, когнитивня аналитика, когнитивные решения, службы ИИ
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: c52d935738916ebc546315d9913d7a48c77cbf9a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945007"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Краткое руководство. Создание Cognitive Services ресурса с помощью интерфейса Command-Line Azure (CLI)

Используйте это краткое руководство для начала работы с Azure Cognitive Services с помощью [интерфейса командной строки Azure (CLI)](/cli/azure/install-azure-cli).

Azure Cognitive Services — это облачные службы с REST API и пакетами SDK клиентских библиотек, которые помогают разработчикам без опыта работы со средствами искусственного интеллекта (ИИ) и обработки и анализа данных создавать когнитивные интеллектуальные приложения. С помощью Azure Cognitive Services разработчики могут без усилий добавлять в свои приложения когнитивные функции, создавая когнитивные решения, которые могут видеть, слышать, говорить, понимать и даже в некоторой степени размышлять.

Cognitive Services представлены [ресурсами](../azure-resource-manager/management/manage-resources-portal.md) Azure, созданными в подписке Azure. После создания ресурса используйте ключи и конечную точку, созданные для проверки подлинности приложений.

В этом кратком руководстве вы узнаете, как зарегистрироваться в Azure Cognitive Services и создать учетную запись с одной или несколькими службами, используя [интерфейс командной строки Azure (CLI)](/cli/azure/install-azure-cli). Эти службы представлены [ресурсами](../azure-resource-manager/management/manage-resources-portal.md)Azure, которые позволяют подключиться к одному или нескольким API-интерфейсы Cognitive Services Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Предварительные требования

* Допустимая подписка Azure — [создайте ее](https://azure.microsoft.com/free/cognitive-services) бесплатно.
* [Интерфейс командной строки Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Установка Azure CLI и вход в систему

Установка [Azure CLI](/cli/azure/install-azure-cli). Чтобы войти в локальную установку интерфейса командной строки, выполните команду [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

Для выполнения этих команд в браузере можно также использовать зеленую кнопку **попробовать** .

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Создание новой группы ресурсов Azure Cognitive Services

Перед созданием ресурса Cognitive Services необходимо иметь группу ресурсов Azure, в которой будет содержаться ресурс. При создании нового ресурса можно либо создать новую группу ресурсов, либо использовать существующую. В этой статье показано, как создать группу ресурсов.

### <a name="choose-your-resource-group-location"></a>Выберите расположение группы ресурсов

Чтобы создать ресурс, вам потребуется одно из расположений Azure, доступных для вашей подписки. Список доступных расположений можно получить с помощью команды [AZ Account List-Locations](/cli/azure/account#az-account-list-locations) . Большинство Cognitive Services доступны из нескольких расположений. Выберите ближайший вариант или посмотрите, какие расположения доступны для службы.

> [!IMPORTANT]
> * Запомните расположение Azure, так как оно понадобится вам при вызове Cognitive Services Azure.
> * Доступность некоторых Cognitive Services может варьироваться в зависимости от региона. Дополнительные сведения см. в статье [продукты Azure по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Создав расположение Azure, создайте новую группу ресурсов в Azure CLI с помощью команды [AZ Group Create](/cli/azure/group#az-group-create) .

В приведенном ниже примере замените расположение Azure на `westus2` одно из расположений Azure, доступных для вашей подписки.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Выбор службы и ценовой категории

При создании нового ресурса необходимо знать тип службы, которую вы хотите использовать, а также [ценовую категорию](https://azure.microsoft.com/pricing/details/cognitive-services/) (или номер SKU). Эта и другие сведения будут использоваться в качестве параметров при создании ресурса.

### <a name="multi-service"></a>Несколько служб

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Несколько служб. Дополнительные сведения см. на странице с [ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


> [!NOTE]
> Многие из Cognitive Services ниже имеют бесплатный уровень, который можно использовать для пробного использования службы. Чтобы использовать уровень "бесплатный", используйте в `F0` качестве номера SKU для ресурса.

### <a name="vision"></a>Компьютерное зрение

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Компьютерное зрение            | `ComputerVision`          |
| Ресурс прогнозирования службы "Пользовательское визуальное распознавание" | `CustomVision.Prediction` |
| Ресурс обучения службы "Пользовательское визуальное распознавание"   | `CustomVision.Training`   |
| Распознавание лиц                       | `Face`                    |
| Распознаватель документов            | `FormRecognizer`          |
| Распознаватель рукописного текста             | `InkRecognizer`           |

### <a name="search"></a>Поиск

| Служба            | Вид                  |
|--------------------|-----------------------|
| Автозаполнение Bing   | `Bing.Autosuggest.v7` |
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
| Анализ форм | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Анализ текста     | `TextAnalytics`     |
| Преобразование текста   | `TextTranslation`   |

### <a name="decision"></a>Решение

| Служба           | Вид               |
|-------------------|--------------------|
| Детектор аномалий  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Персонализатор      | `Personalizer`     |

Список доступных типов "виды" можно найти с помощью команды [AZ cognitiveservices Account List-видах](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Добавление нового ресурса в группу ресурсов

Чтобы создать новый ресурс Cognitive Services и подписываться на него, используйте команду [AZ cognitiveservices Account Create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . Эта команда добавляет новый оплачиваемый ресурс в группу ресурсов, созданную ранее. При создании нового ресурса необходимо знать тип службы, которую вы хотите использовать, а также ценовую категорию (или SKU) и расположение Azure:

Вы можете создать ресурс F0 (Free) для детектора аномалий с именем, `anomaly-detector-resource` используя приведенную ниже команду.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Получение ключей для ресурса

Чтобы войти в локальную установку интерфейса Command-Line (CLI), используйте команду [AZ login](/cli/azure/reference-index#az-login) .

```azurecli-interactive
az login
```

Используйте команду [AZ cognitiveservices Account Keys List](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) , чтобы получить ключи для своего ресурса службы.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Ценовые категории и выставление счетов

Ценовые категории (и сумма в выставленных счетах) основаны на количестве отправленных вами транзакций с использованием данных аутентификации. Каждая ценовая категория определяет:
* максимальное количество разрешенных транзакций, обрабатываемых в секунду (TPS);
* функции службы, включенные в ценовой категории;
* Стоимость предопределенного количества транзакций. При превышении этого объема взимается плата за дополнительную плату, указанную в [сведениях о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) для вашей службы.

## <a name="get-current-quota-usage-for-your-resource"></a>Получение сведений об используемой квоте для ресурса

Используйте команду [AZ cognitiveservices Account List-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) , чтобы получить сведения об использовании для вашего ресурса службы "неприятный".

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить Cognitive Services ресурс, можно удалить его или группу ресурсов. При удалении группы ресурсов удаляются также все другие ресурсы, содержащиеся в этой группе.

Чтобы удалить группу ресурсов и связанные с ней ресурсы, используйте команду AZ Group DELETE.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>См. также

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Общие сведения об Azure Cognitive Services](./what-are-cognitive-services.md)
* [Поддержка естественного языка в Azure Cognitive Services](language-support.md)
* [Поддержка контейнеров в Azure Cognitive Services](cognitive-services-container-support.md)
