---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как c помощью контейнеров Docker выполнять обработку в Cognitive Services как можно ближе к своим данным.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c3aaf7537d233b9652f10ee240ebbe4fc4ec995c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592768"
---
# <a name="container-support-in-azure-cognitive-services"></a>Поддержка контейнеров в Azure Cognitive Services

Благодаря поддержке контейнеров в Azure Cognitive Services разработчики могут использовать те же интеллектуальные интерфейсы API, которые доступны в Azure, но с традиционной для [контейнеров Docker](https://www.docker.com/what-container) гибкостью в выборе расположения для развертывания и размещения служб. Поддержка контейнеров в настоящее время доступен в предварительной версии для подмножества Azure Cognitive Services, включая части:

* [Средства обнаружения аномалий](Anomaly-Detector/overview.md)
* [Компьютерное зрение](Computer-vision/Home.md)
* [Распознавание лиц](Face/Overview.md)
* [Распознаватель формы](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Language Understanding](LUIS/luis-container-howto.md) (LUIS)
* [API службы "Речь"](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Анализ текста](text-analytics/overview.md)

<!--
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->

Контейнеризация — это подход к распространению программного обеспечения, при котором приложение или служба упаковывается в образ контейнера вместе со всеми зависимостями и конфигурациями. Образ контейнера можно развертывать на узле контейнера с незначительной модификацией или без нее. Контейнеры изолированы друг от друга и от базовой операционной системы. При этом они расходуют меньше ресурсов, чем виртуальная машина. Контейнеры для краткосрочных задач можно создавать на основе образов контейнеров и удалять их, когда они станут ненужными.

В следующем видео показано использование контейнера Cognitive Services.

[![Демонстрация контейнера для Cognitive Services](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Ресурсы служб Cognitive Services доступны на [Microsoft Azure](https://azure.microsoft.com). Войдите на [портал Azure](https://portal.azure.com/), чтобы создать и изучить ресурсы Azure для этих служб.

## <a name="features-and-benefits"></a>Возможности и преимущества:

- **Контроль над данными**. Позвольте клиентам выбирать, где Cognitive Services будут обрабатывать их данные. Это особенно важно для тех клиентов, у которых есть потребность в технологии Cognitive Services, но нет возможности отправлять данные в облако. Поддержка согласованности в гибридной среде, в том числе данных, управления, идентификатора и безопасности.
- **Контроль над обновлениями модели**. Предоставьте клиентам гибкость в управлении версиями и обновлением моделей, развернутых в своих решениях.
- **Переносимая архитектура**. Позвольте создавать архитектуру переносимого приложения, которое можно развернуть в Azure, в локальной среде или на пограничном устройстве. Контейнеры можно развертывать непосредственно в [Службе Azure Kubernetes](../aks/index.yml), [Экземплярах контейнеров Azure](../container-instances/index.yml) или в кластере [Kubernetes](https://kubernetes.io/), развернутом в [Azure Stack](/azure-stack/operator). Дополнительные сведения см. в статье [Развертывание Kubernetes в Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Высокая пропускная способность и низкая задержка.** Обеспечьте клиентам возможность масштабировать требования для повышения пропускной способности и снижения задержек, позволив выполнение Cognitive Services поблизости от физического расположения логики и данных приложения. Контейнеры не ограничивают число транзакций в секунду (TPS), и их можно увеличивать и уменьшать для удовлетворения спроса, предоставив необходимые аппаратные ресурсы. 


## <a name="containers-in-azure-cognitive-services"></a>Контейнеры в Azure Cognitive Services

Контейнеры Azure Cognitive Services предоставляют описанный ниже набор контейнеров Docker, каждый из которых содержит подмножество функций нескольких служб в Azure Cognitive Services.

| Service | Поддерживаемые ценовую категорию | Контейнер | Описание |
|---------|----------|----------|-------------|
|[Средства обнаружения аномалий](https://go.microsoft.com/fwlink/?linkid=2083925&clcid=0x409) |F0, S0|**Средства обнаружения аномалий** |API обнаружения аномалий позволяет отслеживать и выявлять аномалии в данных временных рядов с помощью машинного обучения.<br>[Запрос доступа](https://aka.ms/adcontainer)|
|[Компьютерное зрение](Computer-vision/computer-vision-how-to-install-containers.md) |F0 S1|**Распознавание текста** |Извлекает печатный текст из изображений различных объектов с разными поверхностями и фонами, например чеков, плакатов и визитных карточек.<br/><br/>**Важно!** Контейнер распознавания текста в настоящее время работает только на английском языке.<br>[Запрос доступа](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Распознавание лиц](Face/face-how-to-install-containers.md) |F0, S0|**Распознавание лиц** |Обнаруживает человеческие лица на изображениях и определяет методом машинного прогнозирования их характеристики, например черты лица (нос, глаза и т. д.), пол, возраст и многое другое. Кроме обнаружения лиц, служба может сравнивать два лица на одном или на разных изображениях, используя оценку достоверности, а также искать по базе данных похожие или идентичные лица. Схожие лица можно упорядочивать в группы по близким характеристикам.<br>[Запрос доступа](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Распознаватель формы](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) |F0, S0|**Распознаватель формы** |Основные сведения о форме применяется технологии машинного обучения для идентификации и извлечения пар "ключ значение" и таблиц из форм.<br>[Запрос доступа](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**LUIS** ([образ](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Загружает обученную или опубликованную модель Распознавания речи, также известную как приложение LUIS, в контейнер Docker и предоставляет доступ к прогнозам запроса из конечных точек API контейнера. Можно собирать журналы запросов из контейнера и отправлять их обратно на [портал LUIS](https://www.luis.ai) для повышения точности прогнозов приложения.|
|[API службы "Речь"](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Преобразование речи в текст** |Расшифровывает непрерывную речь в режиме реального времени в текстовый формат.<br>[Запрос доступа](https://aka.ms/speechcontainerspreview/)|
|[API службы "Речь"](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Преобразование текста в речь** |Преобразует текст в естественно звучащую речь.<br>[Запрос доступа](https://aka.ms/speechcontainerspreview/)|
|[Анализ текста](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Извлечение ключевых фраз** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Извлекает ключевые фразы для определения основных идей текста. Например, для входного текста "Еда была вкусной и персонал был замечательным" API вернет основные тезисы в записи: "еда" и "замечательный персонал". |
|[Анализ текста](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Определение языка** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Решение может определить язык введенного текста (поддерживается более 120 языков) и сообщить один код языка для каждого документа, отправленного в запросе. Код языка сопряжен с показателем, указывающим степень оценки. |
|[Анализ текста](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Анализ тональности** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Анализирует необработанный текст на предмет положительной или отрицательной тональности. Этот API возвращает оценку тональности (0 или 1) для каждого документа, где 1 означает положительную тональность. Модели анализа предварительно обучены с использованием обширного набора текстов и технологий естественного языка корпорации Майкрософт. Для [выбранных языков](./text-analytics/language-support.md) API может анализировать и оценивать любой необработанный текст, напрямую возвращая результаты вызывающему приложению. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Кроме того, некоторые контейнеры поддерживаются в Cognitive Services [ **предложения все в одном** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) ключи ресурсов. Можно создать один единый ресурс Cognitive Services в одном и использовать один и тот же ключ выставления счетов для поддерживаемых служб для следующих служб:

* Компьютерное зрение
* Распознавание лиц
* LUIS
* Анализ текста

## <a name="container-availability-in-azure-cognitive-services"></a>Доступность контейнеров в Azure Cognitive Services

Контейнеры Azure Cognitive Services общедоступны через подписку Azure, а образы для контейнеров Docker можно извлечь из Реестра контейнеров Microsoft или Docker Hub. Используйте команду [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера из соответствующего репозитория.

> [!IMPORTANT]
> В настоящее время необходимо выполнить процесс регистрации для доступа к со следующими контейнерами, в которых вы заполнения и отправки анкеты с вопросами о вас, вашей компании и вариант использования, для которого вы хотите реализовать контейнеры. Как только вы уже предоставлен доступ и предоставляются учетные данные, может извлекать образы контейнера из закрытого реестра контейнеров проводимых реестр контейнеров Azure.
> * [Dectector аномалий](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Распознавание лиц](Face/face-how-to-install-containers.md)
> * [Распознаватель формы](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Распознавание текста](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Преобразование речи в текст и преобразования текста в речь](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать контейнеры Azure Cognitive Services, необходимо выполнить указанные ниже предварительные требования.

**Модуль Docker**. Модуль Docker должен быть установлен в локальной среде. Docker предоставляет пакеты для настройки среды с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) и [Windows](https://docs.docker.com/docker-for-windows/). В Windows Docker нужно настроить для поддержки контейнеров Linux. Контейнеры Docker можно также развертывать непосредственно в [Службе Azure Kubernetes](../aks/index.yml) или [Экземплярах контейнеров Azure](../container-instances/index.yml).

Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure.

**Опыт работы с Реестром контейнеров Майкрософт и Docker**. Требуется базовое представление о Реестре контейнеров Майкрософт и понятиях Docker, о реестрах, репозиториях, контейнерах и образах контейнеров, а также знание основных команд `docker`.

Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).

Для некоторых контейнеров могут действовать дополнительные требования, в том числе к характеристикам сервера и (или) объему памяти.

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [рецепты контейнера](containers/container-reuse-recipe.md) можно использовать с Cognitive Services.

Установите и изучите функциональные возможности, предоставляемые контейнерами в Azure Cognitive Services:

* [Контейнеры обнаружения аномалий](Anomaly-Detector/anomaly-detector-container-howto.md)
* [Концепция контейнеров компьютера](Computer-vision/computer-vision-how-to-install-containers.md)
* [Контейнеры лиц](Face/face-how-to-install-containers.md)
* [Контейнеры распознаватель формы](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Language Understanding (LUIS) контейнеры](LUIS/luis-container-howto.md)
* [Контейнеры API службы распознавания речи](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Text Analytics контейнеров](text-analytics/how-tos/text-analytics-how-to-install-containers.md)

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->
