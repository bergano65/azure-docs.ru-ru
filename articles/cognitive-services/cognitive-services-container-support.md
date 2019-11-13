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
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: fc4b9469d8634cbb46eccb842c9795a155384eb7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961918"
---
# <a name="container-support-in-azure-cognitive-services"></a>Поддержка контейнеров в Azure Cognitive Services

Благодаря поддержке контейнеров в Azure Cognitive Services разработчики могут использовать те же интеллектуальные интерфейсы API, которые доступны в Azure, но с традиционной для [контейнеров Docker](https://www.docker.com/what-container) гибкостью в выборе расположения для развертывания и размещения служб. В настоящее время поддержка контейнеров доступна для подмножества Cognitive Services Azure, включая части:

> [!div class="checklist"]
> * [Детектор аномалий][ad-containers]
> * [Компьютерное зрение][cv-containers]
> * [Распознавание лиц][fa-containers]
> * [Распознаватель форм][fr-containers]
> * [Распознавание речи (LUIS)][lu-containers]
> * [API службы "Речь"][sp-containers]
> * [Анализ текста][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Контейнеризация — это подход к распространению программного обеспечения, при котором приложение или служба упаковывается в образ контейнера вместе со всеми зависимостями и конфигурациями. Образ контейнера можно развертывать на узле контейнера с незначительной модификацией или без нее. Контейнеры изолированы друг от друга и от базовой операционной системы. При этом они расходуют меньше ресурсов, чем виртуальная машина. Контейнеры для краткосрочных задач можно создавать из образов контейнеров и удалять, когда они станут ненужными.

Cognitive Services ресурсы доступны на [Microsoft Azure](https://azure.microsoft.com). Войдите на [портал Azure](https://portal.azure.com/), чтобы создать и изучить ресурсы Azure для этих служб.

## <a name="features-and-benefits"></a>Возможности и преимущества:

- **Управление данными**: позволяет клиентам выбирать, где эти Cognitive Services обрабатывать свои данные. Это особенно важно для тех клиентов, у которых есть потребность в технологии Cognitive Services, но нет возможности отправлять данные в облако. Поддержка согласованности в гибридной среде, в том числе данных, управления, идентификатора и безопасности.
- **Контроль над обновлениями модели.** Предоставьте гибкость в управлении версиями и обновлением моделей, которые клиенты развертывают в своих решениях.
- **Переносимая архитектура**: позволяет создавать переносимую архитектуру приложений, которая может быть развернута в Azure, в локальной среде и на границе. Контейнеры можно развертывать непосредственно в [Службе Azure Kubernetes](../aks/index.yml), [Экземплярах контейнеров Azure](../container-instances/index.yml) или в кластере [Kubernetes](https://kubernetes.io/), развернутом в [Azure Stack](/azure-stack/operator). Дополнительные сведения см. в статье [Развертывание Kubernetes в Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Высокая пропускная способность и низкая задержка**. Предоставьте клиентам возможность масштабирования для повышения пропускной способности и низкой задержки, позволяя Cognitive Services физически близко к логике приложения и данным. Контейнеры не ограничивают число транзакций в секунду (TPS), и их можно увеличивать и уменьшать для удовлетворения спроса, предоставив необходимые аппаратные ресурсы. 

## <a name="containers-in-azure-cognitive-services"></a>Контейнеры в Azure Cognitive Services

Контейнеры Azure Cognitive Services предоставляют описанный ниже набор контейнеров Docker, каждый из которых содержит подмножество функций нескольких служб в Azure Cognitive Services.

| Служба | Поддерживаемая ценовая категория | Контейнер | ОПИСАНИЕ |
|---------|----------|----------|-------------|
|[Детектор аномалий][ad-containers] |F0, S0|**Аномалия-детектор** |API Детектор аномалий позволяет отслеживать и обнаруживать отклонения в данных временных рядов с помощью машинного обучения.<br>[Запрос доступа](https://aka.ms/adcontainer)|
|[Компьютерное зрение][cv-containers] |F0, S1|**чтение** |Извлекает печатный текст из изображений различных объектов с разными поверхностями и фонами, например чеков, плакатов и визитных карточек. Контейнер чтения также обнаруживает *рукописный текст* в изображениях и предоставляет поддержку PDF/TIFF/многостраничной поддержки.<br/><br/>**Важно.** Контейнер чтения в настоящее время работает только на английском языке.|
|[Распознавание лиц][fa-containers] |F0, S0|**Распознавание лиц** |Обнаруживает человеческие лица на изображениях и определяет методом машинного прогнозирования их характеристики, например черты лица (нос, глаза и т. д.), пол, возраст и многое другое. Кроме обнаружения лиц, служба может сравнивать два лица на одном или на разных изображениях, используя оценку достоверности, а также искать по базе данных похожие или идентичные лица. Схожие лица можно упорядочивать в группы по близким характеристикам.<br>[Запрос доступа](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Распознаватель форм][fr-containers] |F0, S0|**Распознаватель форм** |Понимание форм применяет технологию машинного обучения для обнаружения и извлечения пар "ключ-значение" и таблиц из форм.<br>[Запрос доступа](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([образ](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Загружает обученную или опубликованную модель Распознавания речи, также известную как приложение LUIS, в контейнер Docker и предоставляет доступ к прогнозам запроса из конечных точек API контейнера. Можно собирать журналы запросов из контейнера и отправлять их обратно на [портал LUIS](https://www.luis.ai) для повышения точности прогнозов приложения.|
|[API службы "Речь"][sp-containers-stt] |F0, S0|**Преобразование речи в текст** |Расшифровывает непрерывную речь в режиме реального времени в текстовый формат.|
|[API службы "Речь"][sp-containers-cstt] |F0, S0|**Пользовательское распознавание речи к тексту** |Расшифровывает непрерывной речи в режиме реального времени в текст с помощью пользовательской модели.|
|[API службы "Речь"][sp-containers-tts] |F0, S0|**Преобразование текста в речь** |Преобразует текст в естественно звучащую речь.|
|[API службы "Речь"][sp-containers-ctts] |F0, S0|**Пользовательский текст в речь** |Преобразует текст в голосовую речь с помощью пользовательской модели.|
|[Анализ текста][ta-containers] |F0, S|**Извлечение ключевых фраз** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Извлеките ключевые фразы, чтобы определить основные положения. Например, для входного текста "Еда была вкусной и персонал был замечательным" API вернет основные тезисы в записи: "еда" и "замечательный персонал". |
|[Анализ текста][ta-containers]|F0, S|**Определение языка** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Решение может определить язык введенного текста (поддерживается более 120 языков) и сообщить один код языка для каждого документа, отправленного в запросе. Код языка сопряжен с показателем, указывающим степень оценки. |
|[Анализ текста][ta-containers]|F0, S|**Анализ тональности** ([изображение](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Анализирует необработанный текст на наличие сведений о положительной или отрицательной тональности. Этот API возвращает оценку тональности (0 или 1) для каждого документа, где 1 означает положительную тональность. Модели анализа предварительно обучены с использованием обширного набора текстов и технологий естественного языка корпорации Майкрософт. Для [выбранных языков](./text-analytics/language-support.md) API может анализировать и оценивать любой необработанный текст, напрямую возвращая результаты вызывающему приложению. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Кроме того, некоторые контейнеры поддерживаются Cognitive Services ключами ресурсов " [**все в одном**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) ". Вы можете создать один Cognitive Services ресурс "все в одном" и использовать один и тот же ключ выставления счетов в поддерживаемых службах для следующих служб:

* API Компьютерного зрения
* Распознавание лиц
* LUIS
* Текстовая аналитика

## <a name="container-availability-in-azure-cognitive-services"></a>Доступность контейнеров в Azure Cognitive Services

Контейнеры Azure Cognitive Services общедоступны через подписку Azure, а образы для контейнеров Docker можно извлечь из Реестра контейнеров Microsoft или Docker Hub. Используйте команду [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера из соответствующего репозитория.

> [!IMPORTANT]
> В настоящее время необходимо завершить процесс регистрации для доступа к следующим контейнерам, в которых вы заполните и отправите анкету с вопросами о вас, вашей компании и варианте использования, для которого требуется реализовать контейнеры. После предоставления доступа и предоставленных учетных данных можно извлечь образы контейнеров из закрытого реестра контейнеров, размещенного в реестре контейнеров Azure.
> * [Детектор аномалий](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Распознавание лиц](Face/face-how-to-install-containers.md)
> * [Распознаватель форм](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [чтение](computer-vision/computer-vision-how-to-install-containers.md)
> * [Преобразование речи в текст и преобразование текста в речь](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем использовать контейнеры Azure Cognitive Services, необходимо выполнить указанные ниже предварительные требования.

**Модуль Docker.** Модуль Docker должен быть установлен в локальной среде. Docker предоставляет пакеты для настройки среды с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) и [Windows](https://docs.docker.com/docker-for-windows/). В Windows Docker нужно настроить для поддержки контейнеров Linux. Контейнеры Docker можно также развертывать непосредственно в [Службе Azure Kubernetes](../aks/index.yml) или [Экземплярах контейнеров Azure](../container-instances/index.yml).

Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure.

**Опыт работы с Реестром контейнеров Майкрософт и Docker.** Требуется базовое представление о Реестре контейнеров Майкрософт и понятиях Docker, таких как реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.

См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).

Для некоторых контейнеров могут действовать дополнительные требования, в том числе к характеристикам сервера и (или) объему памяти.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Дополнительная информация

Узнайте о [рецептах контейнеров](containers/container-reuse-recipe.md) , которые можно использовать с Cognitive Services.

Установите и изучите функциональные возможности, предоставляемые контейнерами в Azure Cognitive Services:

* [Контейнеры детекторов аномалий][ad-containers]
* [Контейнеры Компьютерное зрение][cv-containers]
* [Контейнеры лиц][fa-containers]
* [Контейнеры распознавателя форм][fr-containers]
* [Контейнеры Language Understanding (LUIS)][lu-containers]
* [Контейнеры API речевой службы][sp-containers]
* [Контейнеры Анализ текста][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md