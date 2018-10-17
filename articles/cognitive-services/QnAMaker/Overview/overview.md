---
title: Общие сведения о QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker — это облачная служба API, которая применяет пользовательскую аналитику машинного обучения к вопросам пользователя на естественном языке, чтобы предоставить наилучший ответ.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 742c18815445b038e85c33a96743790491976945
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901113"
---
# <a name="what-is-qna-maker"></a>Общие сведения о QnA Maker

QnA Maker — это служба на основе базы знаний вопросов и ответов, которая применяет пользовательскую аналитику машинного обучения к вопросам пользователя на естественном языке, чтобы предоставить наилучший ответ.

Служба QnA Maker позволяет отправлять данные в облачную службу из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов, руководств по продуктам и вопросов и ответов пользователей. Простой в использовании [веб-портал](https://qnamaker.ai) позволяет создавать, обучать и публиковать службу и управлять ею, не выполняя действий по разработке. После публикации службы в конечной точке клиентское приложение, например чат-бот, может управлять диалогом с пользователем, чтобы получить вопросы и ответить на них. 

![Обзор](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Основные процессы QnA Maker

QnA Maker предоставляет две основные службы данных.

* **Извлечение**. Структурированные данные вопросов и ответов извлекаются из частично структурированных [источников данных](../Concepts/data-sources-supported.md), например из документов с часто задаваемыми вопросами и руководств по продуктам. Извлечение файлов может выполняться как этап процесса [создания](https://aka.ms/qnamaker-docs-createkb) базы знаний или позже как часть процесса редактирования.

* **Сопоставление**. [Обученную и протестированную](https://aka.ms/qnamaker-docs-trainkb) базу знаний нужно [опубликовать](https://aka.ms/qnamaker-docs-publishkb). Таким образом формируется конечная точка доступа к базе знаний QnA Maker, которую затем можно использовать в боте или клиентском приложении. Эта конечная точка принимает вопрос пользователя и дает ответ на основе лучшего совпадения в базе знаний, указывая показатель достоверности совпадения.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Архитектура QnA Maker

Служба QnA Maker состоит из следующих служб API:

1. **Службы управления QnA Maker**. Возможности управления для базы знаний QnA Maker, включая первоначальное создание, обновление, обучение и публикацию. Эти действия можно выполнить с помощью [портала](https://qnamaker.ai) или [API управления](https://aka.ms/qnamaker-v4-apis). 

2. **Служба прогнозирования QnA Maker**. Она развертывается в подписке Azure в указанном вами регионе. Пользовательское содержимое базы знаний хранится в службе [Поиск Azure](https://azure.microsoft.com/services/search/), а конечная точка развертывается как [служба приложений](https://azure.microsoft.com/services/app-service/). Вы можете также развернуть ресурс [Application Insights](https://azure.microsoft.com/services/application-insights/) для получения аналитических сведений.

![Архитектура](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Краткие сведения о службе

- [Создание бота для ответа на вопросы](https://aka.ms/qnamaker-docs-create-faqbot) **без необходимости написания кода**.
- **Отсутствует регулирование сети для прогнозов**. Платите за размещение службы, а не за количество транзакций. Дополнительные сведения см. на [странице с ценами](https://aka.ms/qnamaker-docs-pricing).
- **Масштабирование по мере необходимости**. Выберите соответствующие номера SKU отдельных компонентов в соответствии с вашим сценарием. Узнайте, как [выбрать ресурсы](https://aka.ms/qnamaker-docs-capacity) для службы QnA Maker.
- **Полное соответствие данных**. Компоненты службы прогнозирования развертываются в подписке Azure в пределах границ обеспечения соответствия требованиям.


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание службы QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
