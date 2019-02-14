---
title: Создание учетной записи Cognitive Services на портале Azure
titlesuffix: Azure Cognitive Services
description: Описывается, как создать учетную запись API-интерфейсов Microsoft Cognitive Services на портале Azure.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: c69e8b7360774971316b5a060aff9e7057d3e9aa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873978"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Краткое руководство. Создание учетной записи Cognitive Services на портале Azure

Из этого краткого руководства вы узнаете, как зарегистрироваться в Azure Cognitive Services и создать подписку на одну или несколько служб. Эти службы представляются [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, которые позволяют подключиться к одному или нескольким API-интерфейсам Azure Cognitive Services.

## <a name="prerequisites"></a>Предварительные требования

* Действующая подписка Azure. [Создайте учетную запись](https://azure.microsoft.com/free/) бесплатно.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Создание и подписка на ресурс Azure Cognitive Services

Прежде чем приступать к работе, важно понимать, что существует два типа подписок Azure Cognitive Services. Первый — подписка на одну службу (например, "Компьютерное зрение" или службы распознавания речи). Подписка на одну службу распространяется только на соответствующий ресурс. Второй тип — подписка на несколько служб Azure Cognitive Services. Эта подписка позволяет использовать одну подписку для работы с большинством служб Azure Cognitive Services. Этот вариант предполагает консолидацию выставления счетов. Дополнительные сведения см. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> В настоящее время ключи для нескольких служб **не** поддерживаются в следующих службах: QnA Maker, службах распознавания речи и "Пользовательское визуальное распознавание".

В следующих разделах рассматривается создание подписки на одну или несколько служб.

### <a name="single-service-subscription"></a>Подписка на одну службу

1. Войдите на [портал Azure](http://portal.azure.com) и щелкните **+Создать ресурс**.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. В разделе Azure Marketplace щелкните **Искусственный интеллект + Машинное обучение**. Если вы не видите службу, которая вам нужна, щелкните **Показать все**, чтобы просмотреть весь каталог API-интерфейсов Cognitive Services.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. На странице **Создание** введите приведенные ниже сведения.

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Рекомендуется использовать описательное имя, например *MyNameFaceAPIAccount*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Местоположение.** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group), которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)

### <a name="multi-service-subscription"></a>Подписка на несколько служб

1. Войдите на [портал Azure](http://portal.azure.com) и щелкните **+Создать ресурс**.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Найдите строку поиска и введите следующий запрос: **Cognitive Services**.

    ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Выберите **Cognitive Services**.

    ![Выбор Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. На странице **Создание** введите приведенные ниже сведения.

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Рекомендуем использовать описательное имя, например *MyCognitiveServicesAccount*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Местоположение.** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group), которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

## <a name="access-your-resource"></a>Получение доступа к ресурсам

> [!NOTE]
> Владелец подписки может отключить создание учетной записи Cognitive Services для группы ресурсов или подписки с помощью [политики Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), присвоив определение политики "Недопустимые типы ресурсов" и указав **Microsoft.CognitiveServices/accounts** в качестве целевого типа ресурсов.

После создания ресурса вы можете получить к нему доступ на панели мониторинга Azure, если она закреплена. В противном случае его можно найти в **Группе ресурсов**.

В пределах ресурса Cognitive Services можно использовать URL-адрес конечной точки и ключи в разделе **Обзор**, чтобы выполнять вызовы API в своих приложениях.

![Снимок экрана ресурсов](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>См. также

* [Краткое руководство по извлечению рукописного текста из изображения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Руководство по созданию приложения для поиска лиц на изображении и помещения их в рамку](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Руководство по созданию веб-страницы пользовательского поиска](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Бот LUIS в Node.js](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
