---
title: Создание учетной записи API-интерфейсов Cognitive Services на портале Azure
titlesuffix: Azure Cognitive Services
description: Описывается, как создать учетную запись API-интерфейсов Microsoft Cognitive Services на портале Azure.
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386422"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Краткое руководство. Создание учетной записи Cognitive Services на портале Azure

Используйте это краткое руководство, чтобы начать работать с Azure Cognitive Services. Эти службы представляются [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, которые позволяют подключиться к одному или нескольким доступным API-интерфейсам Cognitive Services.

## <a name="prerequisites"></a>Предварительные требования

* Действующая подписка Azure. Вы можете создать [учетную запись](https://azure.microsoft.com/free/) бесплатно.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Создание и подписка на ресурс Azure Cognitive Services

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
    | **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group), которая будет содержать ресурс Cognitive Services. Вы можете создать группу или добавить ее к созданной ранее группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Получение доступа к ресурсам 

> [!NOTE]
> Владелец подписки может отключить создание учетной записи Cognitive Services для группы ресурсов или подписки с помощью [политики Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), присвоив определение политики "Недопустимые типы ресурсов" и указав **Microsoft.CognitiveServices/accounts** в качестве целевого типа ресурсов.

После создания ресурса вы можете получить к нему доступ на панели мониторинга Azure, если она закреплена. В противном случае его можно найти в **Группе ресурсов**.

В пределах ресурса Cognitive Services можно использовать URL-адрес конечной точки и ключи в разделе **Обзор**, чтобы выполнять вызовы API в своих приложениях.

![Снимок экрана ресурсов](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Создание приложения для обработки изображений — C#](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>См. также

* [Краткое руководство по извлечению рукописного текста с помощью REST API и C#; в API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Руководство. Создание приложения WPF для обнаружения и выделения лиц на изображении с помощью пакета SDK .NET](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Руководство по созданию веб-страницы пользовательского поиска](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Бот LUIS в Node.js](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
