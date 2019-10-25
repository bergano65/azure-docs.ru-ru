---
title: Создание Cognitive Services ресурса в портал Azure
titleSuffix: Azure Cognitive Services
description: Начните работу с Cognitive Services Azure, создав подписку на ресурс в портал Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: 14157ed22ea40a2157f696e91c59af9b58110bf2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819936"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Создание Cognitive Services ресурса с помощью портал Azure

Используйте это краткое руководство для начала работы с Azure Cognitive Services. После создания ресурса "поисковой службы" в портал Azure вы получите конечную точку и ключ для проверки подлинности приложений.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Технические условия

* Допустимая подписка Azure — [создайте ее бесплатно](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Создание нового ресурса Azure Cognitive Services

1. Создает ресурса.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Ресурс с несколькими службами](#tab/multiservice)
    
    Ресурс с несколькими службами называется **Cognitive Services** на портале. [Создайте ресурс Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    В настоящее время ресурс с несколькими службами обеспечивает доступ к следующим Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Компьютерное зрение  | Content Moderator                                    | Распознавание лиц               | Распознавание речи (LUIS) | Анализ текста   |
    | Translator Text  | Поиск Bing версии 7 <br>(Веб, изображение, Новости, видео, визуальный элемент) | Пользовательский поиск Bing | API Bing для поиска сущностей            | Автозаполнение Bing |
    | Проверка орфографии Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Ресурс с одной службой](#tab/singleservice)

    Используйте следующие ссылки, чтобы создать ресурс для доступных Cognitive Services.

    | Зрение                      | Речь                  | Язык                          | Решение             | служба поиска.                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Компьютерное зрение](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Службы "Речь"](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Иммерсивное средство чтения](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Детектор аномалий](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Версии 7 API Поиск Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Пользовательская служба визуального представления](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Распознавание говорящего](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Распознавание речи (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Пользовательский поиск Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Распознавание лиц](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Персонализация](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Поиск сущностей Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Распознаватель рукописного ввода](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Проверка орфографии Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Перевод текстов](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Автозаполнение Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. На странице **Создание** введите приведенные ниже сведения.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Ресурс с несколькими службами](#tab/multiservice)

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Например, *микогнитивесервицесресаурце*. |
    | **подписка** | Выберите одну из доступных подписок Azure. |
    | **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. Запомните расположение Azure, так как оно может потребоваться при вызове Cognitive Services Azure. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Щелкните **Create**(Создать).

    #### <a name="single-service-resourcetabsingleservice"></a>[Ресурс с одной службой](#tab/singleservice)

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Например, *текстаналитиксресаурце*. |
    | **подписка** | Выберите одну из доступных подписок Azure. |
    | **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. Запомните расположение Azure, так как оно может потребоваться при вызове Cognitive Services Azure. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Щелкните **Create**(Создать).

    ***

## <a name="get-the-keys-for-your-resource"></a>Получение ключей для ресурса

1. После успешного развертывания ресурса щелкните **"переход к ресурсу"** в разделе **дальнейшие действия**.

    ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. В открывшейся области быстрого запуска можно получить доступ к ключу и конечной точке.

    ![Получить ключ и конечную точку](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При удалении группы ресурсов также удаляются все другие ресурсы, содержащиеся в этой группе.

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Определение группы ресурсов, содержащей ресурс для удаления
3. Щелкните правой кнопкой мыши список групп ресурсов. Выберите **Удалить группу ресурсов** и подтвердите выбор.

## <a name="see-also"></a>Дополнительные материалы

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Что такое Azure Cognitive Services?](Welcome.md)
* [Поддержка естественного языка](language-support.md)
* [Поддержка контейнеров DOCKER](cognitive-services-container-support.md)
