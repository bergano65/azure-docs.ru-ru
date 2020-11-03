---
title: Общие сведения об Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Cognitive Services делает ИИ доступным для всех разработчиков, и опыт работы с машинным обучением и анализом данных не требуется. Необходимо просто выполнить вызов API из приложения, чтобы добавить в свои приложения возможность для просмотра (расширенные поиск и распознавание изображений) и прослушивания данных, использования речи, поиска данных и принятия решений.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: cognitive services, когнитивня аналитика, когнитивные решения, службы ИИ, когнитивное распознавание, когнитивные функции
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 82531466c80917087ca007900ca79b3485f38a21
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546268"
---
# <a name="what-are-azure-cognitive-services"></a>Общие сведения об Azure Cognitive Services

Azure Cognitive Services — это облачные службы с REST API и пакетами SDK клиентской библиотеки, которые помогают интегрировать когнитивные средства искусственного интеллекта в свои приложения. Вы можете добавлять когнитивные функции в свои приложения, не обладая опытом работы с искусственным интеллектом (ИИ) или навыками обработки и анализа данных. Azure Cognitive Services содержит различные службы искусственного интеллекта, которые позволяют создавать когнитивные решения, использующие функции просмотра и прослушивания данных, функцию речи, анализа данных и даже принятия решений.

## <a name="categories-of-cognitive-services"></a>Категории Cognitive Services

Каталог служб Cognitive Services, которые обеспечивают когнитивное распознавание, состоит из пяти основных категорий:

* Зрение
* Речь
* Язык
* Решение
* Поиск

В следующих разделах этой статьи представлен список служб, которые являются частью этих пяти основных категорий.

## <a name="vision-apis"></a>API, связанные со зрением

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Компьютерное зрение](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "API Компьютерного зрения")|Служба Компьютерного зрения предоставляет доступ к передовым когнитивным алгоритмам обработки изображений и возврата данных.|
|[Пользовательское визуальное распознавание](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Служба "Пользовательское визуальное распознавание"")|Пользовательская служба визуального распознавания позволяет создавать пользовательские классификаторы изображений.|
|[Распознавание лиц](https://docs.microsoft.com/azure/cognitive-services/face/ "Распознавание лиц")| Служба "Распознавание лиц" обеспечивает доступ к расширенным алгоритмам, позволяя определять и распознавать лица на основе атрибутов.|
|[Распознаватель документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Распознаватель документов")|Распознаватель документов идентифицирует и извлекает пары "ключ —значение" и данные таблиц из документов, а затем выводит структурированные данные, включая отношения в исходном файле.|
|[Распознаватель рукописного текста](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Распознаватель рукописного текста") (прекращение использования)|Распознаватель рукописного текста позволяет распознавать и анализировать данные мазка кистью рукописного ввода, документы и рукописное содержимое, а также выводить структуру документа со всеми распознанными объектами.|
|[Индексатор видео](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Индексатор видео")|Индексатор видео позволяет извлекать аналитические сведения из видео.|

## <a name="speech-apis"></a>API, связанные с речью

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Служба распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Служба "Речь"")|Служба "Речь" позволяет добавлять в приложения функции с поддержкой речи. Служба речи включает в себя различные возможности, такие как преобразование речи в текст, преобразование текста в речь и многое другое.|
<!--
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>API, связанные с языком

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Распознавание речи (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/ "Распознавание речи")|Служба "Распознавание речи" (LUIS) позволяет приложению распознавать желания людей на основе их собственных слов.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|Служба QnA Maker позволяет создавать службу для работы с вопросами и ответами на основе частично структурированного содержимого.|
|[Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Анализ текста")| Служба "Анализ текста" — это служба обработки естественного языка, выполняющая анализ тональности необработанного текста, извлечение ключевых фраз и определение языка.|
|[Переводчик](https://docs.microsoft.com/azure/cognitive-services/translator/ "API перевода")|Переводчик выполняет машинный перевод текстов почти в реальном времени.|
| [Иммерсивное средство чтения](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/ "Иммерсивное средство чтения") | Иммерсивное средство чтения добавляет в приложения возможности чтения и анализа данных экрана. |

## <a name="decision-apis"></a>API-интерфейсы принятия решений

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Детектор аномалий](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Детектор аномалий") |Детектор аномалий позволяет отслеживать и обнаруживать отклонения в данных временных рядов.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator позволяет отслеживать потенциально оскорбительное, нежелательное и представляющее риск содержимое.|
|[Помощник по метрикам](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (предварительная версия) | Помощник по метрикам обеспечивает настраиваемое обнаружение аномалий для многовариантных данных временных рядов с использованием полнофункционального веб-портала.|
|[Персонализатор](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Персонализатор")|Персонализатор позволяет выбирать максимально удобный режим работы для своих пользователей, изучая их поведение в реальном времени.|

## <a name="search-apis"></a>Интерфейсы API для поиска

> [!NOTE]
> Хотите узнать о [Когнитивном поиске Azure](https://docs.microsoft.com/azure/search/)? Хотя для некоторых задач используется Cognitive Services, это другая технология поиска, поддерживающая иные сценарии.

|Имя службы|Описание службы|
|:-----------|:------------------|
|[Поиск новостей Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "API Поиска новостей Bing")|Служба "Поиск новостей Bing" выполняет поиск новостных статей релевантных запросу пользователя.|
|[Поиск видео Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "API Поиска видео Bing")|Служба "Поиск видео Bing" выполняет поиск видео, релевантных запросу пользователя.|
|[Поиск в Интернете Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "API Поиска в Интернете Bing")|Служба "Поиск в Интернете Bing" выполняет поиск материалов в Интернете, релевантных запросу пользователя.|
|[Автозаполнение Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "API Автозаполнения Bing")|Служба "Автозаполнение Bing" предлагает отправлять в Bing часть слова поискового запроса и получать список предлагаемых запросов.|
|[Пользовательский поиск Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Пользовательский поиск Bing")|Служба "Пользовательский поиск Bing" позволяет выполнять пользовательскую настройку функции поиска требуемых материалов.|
|[Поиск сущностей Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "API Поиска сущностей Bing")|Служба "Поиск сущностей Bing" возвращает сведения о сущностях, которые Bing определяет как релевантные запросу пользователя.|
|[Поиск изображений Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "API Поиска изображений Bing")|Служба "Поиск изображений Bing" отображает изображения, релевантные запросу пользователя.|
|[Визуальный поиск Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Визуальный поиск Bing")|Служба "Визуальный поиск Bing" возвращает аналитические сведения об изображении, например визуально схожие изображения, ресурсы, на которых можно купить продукты, найденные на изображении, а также связанные результаты поиска.|
|[Поиск местных компаний Bing](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Поиск местных компаний Bing")| С помощью API Поиска местных компаний Bing для приложений можно находить контактную информацию о местных компаниях, а также сведения об их расположении на основе поисковых запросов.|
|[Проверка орфографии Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "API Проверки орфографии Bing")|Служба "Проверка орфографии Bing" предназначена для контекстной проверки грамматики и орфографии.|

## <a name="development-options"></a>Варианты разработки 

В Azure и Cognitive Services вы можете использовать несколько вариантов разработки:

* средства автоматизации и интеграции, такие как Logic Apps и Power Automate;
* варианты развертывания, такие как "Функции Azure" и "Служба приложений"; 
* контейнеры Docker Cognitive Services для безопасного доступа;
* такие средства, как Apache Spark, Azure Databricks, Azure Synapse Analytics и "Служба Azure Kubernetes" для сценариев с большими данными. 

Дополнительные сведения см. в статье о [вариантах разработки для Cognitive Services](./cognitive-services-development-options.md).

## <a name="learn-with-the-quickstarts"></a>Изучение по кратким руководствам

Начните с создания ресурса Cognitive Services с помощью практических руководств, используя указанные ниже методы.

* [Портал Azure](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Портал Azure")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Клиентские библиотеки пакетов Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Шаблоны Azure Resource Manager (ARM)](resource-manager-template.md?tabs=portal "Шаблоны Azure Resource Manager (ARM)")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Безопасное использование Cognitive Services

Службы Azure Cognitive Services предоставляют многоуровневую модель безопасности, включая [проверки подлинности](authentication.md "проверка подлинности") с помощью учетных данных Azure Active Directory, действительного ключа ресурса и [виртуальных сетей Azure](cognitive-services-virtual-networks.md "Виртуальные сети Azure").

## <a name="containers-for-cognitive-services"></a>Контейнеры для Cognitive Services

 Cognitive Services предоставляет контейнеры для развертывания в облаке Azure или локально. Дополнительные сведения о [контейнерах Cognitive Services](cognitive-services-container-support.md "Контейнеры Cognitive Services").

## <a name="regional-availability"></a>Доступность по регионам

API-интерфейсы в Cognitive Services размещаются в растущей сети центров обработки данных корпорации Майкрософт. Вы можете найти локализацию для каждого API в [списке регионов Azure](https://azure.microsoft.com/regions "Список регионов Azure").

Ищете регион, который пока не поддерживается? Сообщите нам, отправив запрос функции на наш [форум UserVoice](https://cognitive.uservoice.com/ "Форум UserVoice").

## <a name="supported-cultural-languages"></a>Поддерживаемые региональные языки

Cognitive Services поддерживает широкий спектр региональных языков на уровне службы. Дополнительные сведения о доступности языков для каждого API можно найти в [списке поддерживаемых языков](language-support.md "Список поддерживаемых языков").

## <a name="certifications-and-compliance"></a>Соответствие требованиям и сертификаты

Службы Cognitive Services получили такие сертификаты, как сертификация CSA STAR, FedRAMP Moderate и HIPAA BAA. Вы можете [скачать](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "скачиваемого файла") сертификаты для собственных аудитов и проверок безопасности.

Чтобы понять, как работает конфиденциальность и управление данными, перейдите в [центр управления безопасностью](https://servicetrust.microsoft.com/ "Центр управления безопасностью").

## <a name="support"></a>Поддержка

Cognitive Services предоставляет несколько вариантов поддержки, которые помогут вам эффективно создавать интеллектуальные приложения. Для пользователей Cognitive Services также обеспечивается поддержка от сообщества разработчиков, которые помогут ответить на конкретные вопросы. Полный список доступных вариантов см. в статье о [возможностях поддержки и справки для Cognitive Services](cognitive-services-support-options.md "Возможности получения поддержки и справки Cognitive Services").

## <a name="next-steps"></a>Дальнейшие действия

* [Создание учетной записи Cognitive Services](cognitive-services-apis-create-account.md "Создание учетной записи Cognitive Services")
* [Новые возможности в документации по Cognitive Services](whats-new-docs.md "Новые возможности в документации по Cognitive Services")
