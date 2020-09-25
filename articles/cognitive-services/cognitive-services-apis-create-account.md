---
title: Создание Cognitive Services ресурса в портал Azure
titleSuffix: Azure Cognitive Services
description: Начните работу с Cognitive Services Azure, создав подписку на ресурс в портал Azure.
services: cognitive-services
author: aahill
manager: nitinme
keywords: службы для перестановки, анализа и перестановки, службы AI
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 623982826f82e44b8d031d19002d33e0a8e798bd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326870"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Краткое руководство. Создание Cognitive Services ресурса с помощью портал Azure

Используйте это краткое руководство для начала работы с Azure Cognitive Services. После создания ресурса "поисковой службы" в портал Azure вы получите конечную точку и ключ для проверки подлинности приложений.

Azure Cognitive Services — это облачные службы с REST API и пакетами SDK клиентских библиотек, которые помогают разработчикам без опыта работы со средствами искусственного интеллекта (ИИ) и обработки и анализа данных создавать когнитивные интеллектуальные приложения. С помощью Azure Cognitive Services разработчики могут без усилий добавлять в свои приложения когнитивные функции, создавая когнитивные решения, которые могут видеть, слышать, говорить, понимать и даже в некоторой степени размышлять.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Предварительные требования

* Действующая подписка Azure ([создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/)).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Создание нового ресурса Azure Cognitive Services

1. Создание ресурса.

    #### <a name="multi-service-resource"></a>[Ресурс для нескольких служб](#tab/multiservice)

    Ресурс с несколькими службами называется **Cognitive Services** на портале. [Создайте ресурс Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

    В настоящее время ресурс с несколькими службами обеспечивает доступ к следующим Cognitive Services:

    - API Компьютерного зрения
    - Content Moderator
    - Распознавание лиц
    - Распознавание речи (LUIS)
    - Анализ текста
    - API перевода
    - Поиск Bing версии 7 <br>(Веб, изображение, Новости, видео, визуальный элемент)
    - Пользовательский поиск Bing
    - API Поиска сущностей Bing
    - API Автозаполнения Bing
    - API Проверки орфографии Bing

    #### <a name="single-service-resource"></a>[Ресурс с одной службой](#tab/singleservice)

    Используйте следующие ссылки, чтобы создать ресурс для доступных Cognitive Services.

    | Зрение                      | Речь                  | Язык                          | Решение             | Поиск                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Компьютерное зрение](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Речевые службы](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Иммерсивное средство чтения](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Детектор аномалий](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Версии 7 API Поиск Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Пользовательская служба визуального представления](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Распознавание говорящего](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Распознавание речи (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Пользовательский поиск Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Распознавание лиц](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Персонализатор](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Поиск сущностей Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Распознаватель рукописного текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Помощник по метрикам](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Проверка орфографии Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Переводчик](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Автозаполнение Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. На странице **Создание** введите приведенные ниже сведения.

    #### <a name="multi-service-resource"></a>[Ресурс для нескольких служб](#tab/multiservice)

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Например, *микогнитивесервицесресаурце*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Экран создания ресурса с несколькими службами](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Нажмите кнопку **Создать**.

    #### <a name="single-service-resource"></a>[Ресурс с одной службой](#tab/singleservice)

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Например, *текстаналитиксресаурце*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Расположение** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Экран создания ресурса с одной службой](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Нажмите кнопку **Создать**.

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Получение ключей для ресурса

1. После успешного развертывания ресурса щелкните **"переход к ресурсу"** в разделе **дальнейшие действия**.

    ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. В открывшейся области быстрого запуска можно получить доступ к ключу и конечной точке.

    ![Получить ключ и конечную точку](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При удалении группы ресурсов удаляются также все другие ресурсы, содержащиеся в этой группе.

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, содержащую ресурс, который необходимо удалить.
3. Щелкните правой кнопкой мыши список групп ресурсов. Выберите **Удалить группу ресурсов** и подтвердите выбор.

## <a name="see-also"></a>См. также раздел

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Общие сведения об Azure Cognitive Services](Welcome.md)
* [Создание нового ресурса с помощью клиентской библиотеки управления Azure](.\cognitive-services-apis-create-account-client-library.md)
* [Поддержка естественного языка в Azure Cognitive Services](language-support.md)
* [Поддержка контейнеров в Azure Cognitive Services](cognitive-services-container-support.md)
