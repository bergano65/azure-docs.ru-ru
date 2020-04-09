---
title: Контейнеры Cognitive Services часто задают вопросы (Часто задаваемые вопросы)
titleSuffix: Azure Cognitive Services
description: Часто задаваемые вопросы и ответы.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8aae650065e8de11ccdc55a8a056c379a219fcb4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876512"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Контейнеры Azure Cognitive Services часто задают вопросы (Часто задаваемые вопросы)

## <a name="general-questions"></a>Общие вопросы

**В: Что доступно?**

**О:** [Поддержка контейнеров в Azure Cognitive Services](../cognitive-services-container-support.md) позволяет разработчикам использовать те же интеллектуальные ABI, которые доступны в Azure, но с [преимуществами](../cognitive-services-container-support.md#features-and-benefits) контейнеризации. Контейнерная поддержка в настоящее время доступна в предварительном просмотре для подмножества когнитивных служб Azure, включая части:

> [!div class="checklist"]
> * [Детектор аномалий][ad-containers]
> * [API Компьютерного зрения][cv-containers]
> * [Распознавание лиц][fa-containers]
> * [Распознаватель документов][fr-containers]
> * [Распознавание речи (LUIS)][lu-containers]
> * [API службы "Речь"][sp-containers]
> * [Анализ текста][ta-containers]

**В: Есть ли разница между облаком Когнитивных Услуг и контейнерами?**

**A:** Контейнеры Cognitive Services являются альтернативой облаку Cognitive Services. Контейнеры предлагают те же возможности, что и соответствующие облачные службы. Клиенты могут развернуть контейнеры на месте или в Azure. Основные технологии ИИ, уровни ценообразования, клавиши API и подпись API являются одинаковыми между контейнером и соответствующими облачными службами. Ниже приведены [особенности и преимущества](../cognitive-services-container-support.md#features-and-benefits) выбора контейнеров по сравнению с их эквивалентом облачного сервиса.

**Вопрос: Будут ли контейнеры доступны для всех когнитивных служб и какой следующий набор контейнеров мы должны ожидать?**

**A:** Мы хотели бы сделать больше Cognitive Services доступными в качестве контейнерных предложений. Свяжитесь с местным менеджером учетных записей Майкрософт, чтобы получать обновления о новых выпусках контейнеров и других объявлениях Cognitive Services.

**В: Каким будет Соглашение об уровне обслуживания (SLA) для контейнеров Cognitive Services?**

**A:** Контейнеры Cognitive Services не имеют SLA.

Конфигурации контейнеров Cognitive Services контролируются клиентами, поэтому корпорация Майкрософт не будет предлагать SLA для общей доступности (GA). Клиенты могут свободно развертывать контейнеры на месте, таким образом, они определяют среды узла.

> [!IMPORTANT]
> Чтобы узнать больше о соглашениях уровня услуг по предоставлению когнитивных услуг, [посетите нашу страницу SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**Вопрос: Доступны ли эти контейнеры в суверенных облаках?**

**A:** Не все знакомы с термином "суверенное облако", поэтому давайте начнем с определения:

> "Суверенное облако" состоит из облаков [Azure,](../../azure-government/documentation-government-welcome.md) [Azure Germany](../../germany/germany-welcome.md)и [Azure China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

К сожалению, контейнеры Cognitive Services *не* поддерживаются в суверенных облаках. Контейнеры могут работать в этих облаках, но они будут выведены из общедоступного облака и должны отправлять данные об использовании в общедоступную точку.

### <a name="versioning"></a>Управление версиями

**В: Как обновляются контейнеры до последней версии?**

**A:** Клиенты могут выбрать, когда обновить контейнеры, которые они развернули. Контейнеры будут помечены стандартными [тегами Docker,](https://docs.docker.com/engine/reference/commandline/tag/) `latest` например, для обозначения последней версии. Мы рекомендуем клиентам тянуть последнюю версию контейнеров, как они будут выпущены, выезд [Azure контейнер реестра webhooks](../../container-registry/container-registry-webhook.md) для получения уведомления, когда изображение обновляется.
 
**В: Какие версии будут поддерживаться?**

**A:** Текущая и последняя основная версия контейнера будет поддерживаться. Тем не менее, мы призываем клиентов оставаться в курсе, чтобы получить новейшие технологии.
 
**В: Как обновляются обновления?**

**A:** Основные изменения версии указывают на то, что в подписи API происходит нарушение. Мы ожидаем, что это, как правило, совпадает с основными изменениями версии соответствующего облачного предложения Cognitive Service. Незначительные изменения версии указывают на исправления ошибок, обновления моделей или новые функции, которые не вносят изменения в подпись API.

## <a name="technical-questions"></a>Технические вопросы

**В: Как запустить контейнеры Cognitive Services на устройствах IoT?**

Если у вас нет надежного подключения к Интернету, или вы хотите сэкономить на стоимости пропускной способности. Или, если у вас есть требования к низкой задержке или они имеют дело с конфиденциальными данными, которые необходимо анализировать на месте, [Azure IoT Edge с контейнерами Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) обеспечивает согласованность с облаком.

**В: Как обеспечить обратную связь с продуктом и рекомендации?**

**A:** Клиентам рекомендуется [публично высказывать свои опасения,](https://cognitive.uservoice.com/) а также голосовать за других лиц, которые сделали то же самое, когда потенциальные проблемы пересекаются. Голосовой инструмент пользователя может использоваться как для обратной связи с продуктом, так и для рекомендаций.

**В: С кем я могу обратиться за поддержкой?**

**A:** Каналы поддержки клиентов такие же, как и облачные услуги Cognitive Services. Все контейнеры Cognitive Services включают функции регистрации, которые помогут нам и клиентам сообщества поддерживать. Для дополнительной поддержки ознакомьтесь со следующими вариантами.

### <a name="customer-support-plan"></a>План поддержки клиентов

Клиенты должны обратиться к [своему плану поддержки Azure,](https://azure.microsoft.com/support/plans/) чтобы узнать, к кому обратиться за поддержкой.

### <a name="azure-knowledge-center"></a>Центр знаний Azure

Клиент может свободно исследовать [центр знаний Azure,](https://azure.microsoft.com/resources/knowledge-center/) чтобы ответить на вопросы и вопросы поддержки.

### <a name="stack-overflow"></a>Stack Overflow

> [Стек переполнения](https://en.wikipedia.org/wiki/Stack_Overflow) вопросов и ответов сайт для профессиональных и энтузиастов программистов.

Изучите следующие теги для потенциальных вопросов и ответов, которые соответствуют вашим потребностям.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**В: Как работает выставление счетов?**

**A:** Клиенты взимаются на основе потребления, подобно облаку Когнитивных Услуг. Контейнеры должны быть настроены для отправки данных измерений в Azure, и транзакции будут выставлены соответствующим образом. Ресурсы, используемые в размещенных и местных службах, добавят к единой квоте с многоуровневыми ценами, считая с обоими обычаями. Для получения более подробной информации обратитесь к ценовой странице соответствующего предложения.

* [Детектор аномалий][ad-containers-billing]
* [API Компьютерного зрения][cv-containers-billing]
* [Распознавание лиц][fa-containers-billing]
* [Распознаватель документов][fr-containers-billing]
* [Распознавание речи (LUIS)][lu-containers-billing]
* [API службы "Речь"][sp-containers-billing]
* [Анализ текста][ta-containers-billing]

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют данные клиента в корпорацию Майкрософт.
 
**В: Какова текущая гарантия поддержки контейнеров?**

**A:** Гарантия на предварительный просмотр не требуется. Стандартная гарантия Microsoft на корпоративное программное обеспечение будет применяться, когда контейнеры официально объявлены как общая доступность (GA).
 
**В: Что происходит с контейнерами Cognitive Services при потере подключения к Интернету?**

**A:** Контейнеры Cognitive Services *не имеют лицензии* на запуск без подключения к Azure для измерения. Клиентам необходимо постоянно сообщать контейнеры со службой учета.

**В: Как долго контейнер может работать без подключения к Azure?**

**A:** Контейнеры Cognitive Services *не имеют лицензии* на запуск без подключения к Azure для измерения. Клиентам необходимо постоянно сообщать контейнеры со службой учета.
 
**В: Что такое современное оборудование, необходимое для запуска этих контейнеров?**

**A:** Контейнеры Cognitive Services — это контейнеры на основе x64, которые могут запускать любые совместимые linux-узлы, VM и краевое устройство, поддерживающее контейнеры x64 Linux Docker. Все они требуют процессоров процессоров. Минимальные и рекомендуемые конфигурации для каждого предложения контейнеров доступны ниже:

* [Детектор аномалий][ad-containers-recommendations]
* [API Компьютерного зрения][cv-containers-recommendations]
* [Распознавание лиц][fa-containers-recommendations]
* [Распознаватель документов][fr-containers-recommendations]
* [Распознавание речи (LUIS)][lu-containers-recommendations]
* [API службы "Речь"][sp-containers-recommendations]
* [Анализ текста][ta-containers-recommendations]
 
**В: Поддерживаются ли эти контейнеры в настоящее время в Windows?**

**A:** Контейнеры Cognitive Services — это контейнеры Linux, однако на Windows есть определенная поддержка контейнеров Linux. Для получения дополнительной информации о контейнерах Linux на Windows [см.](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)
 
**В: Как обнаружить контейнеры?**

**A:** Контейнеры когнитивных служб доступны в различных местах, таких как портал Azure, концентратор Docker и реестры контейнеров Azure. В самых последних местах контейнера обратитесь к [репозиториям контейнеров и изображениям.](../cognitive-services-container-support.md#container-repositories-and-images)

**В: Как контейнеры Cognitive Services соотносятся с предложениями AWS и Google?**

**A:** Корпорация Майкрософт является первым поставщиком облачных технологий для перемещения своих предварительно обученных моделей ИИ в контейнерах с простым выставлением счетов за транзакцию, как если бы клиенты использовали облачный сервис. Microsoft считает, что гибридное облако дает клиентам больше выбора.

**В: Какие сертификаты соответствия имеют контейнеры?**

**A:** Контейнеры когнитивных услуг не имеют сертификатов соответствия

**В: В каких регионах доступны контейнеры Cognitive Services?**

**A:** Контейнеры могут быть запущены в любом месте в любом регионе, однако они нуждаются в ключе и перезвонить в Azure для измерения. Все поддерживаемые области облачной службы поддерживаются для вызова измерения контейнеров.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
