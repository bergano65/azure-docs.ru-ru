---
title: Процесс ограничения Cognitive Services
titleSuffix: Azure Cognitive Services
description: Узнайте, как применять для раннего доступа к новым контейнерам Cognitive Services и интерфейсам API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599516"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Процесс ограничения для Azure Cognitive Services

> [!NOTE]
> После того как предложение услуги выполнит предварительную версию для предварительного просмотра, она переходит в "общедоступный" Открытый предварительный просмотр, для которого не требуется приложение для доступа. После завершения процесса предварительной версии он выпускается как общедоступный (GA).

По мере знакомства с новыми предложениями Azure Cognitive Services они проходят через предварительную версию, где клиенты должны запрашивать доступ через приложение. Этот процесс ограничения помогает выявление возможностей для улучшения предложений услуг, прежде чем они будут широко доступны. 

Эта статья поможет вам выполнить процесс приложения для условного Cognitive Services предложений.

## <a name="eligibility-and-approval-process"></a>Процесс приемлемости и утверждения

Для оценки интересов и лучшего понимания потребностей клиентов можно использовать процесс ограничения. Группа разработчиков Майкрософт принимает [приложения](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) коммерческих клиентов с действительной подпиской Azure и допустимым бизнес-сценарием. Клиенты потенциально могут отклонять свои приложения в следующих случаях:

 - Они не связаны ни с одной организацией
 - У них нет действующей подписки Azure.
 - Приложение было отправлено с помощью личной электронной почты ( @hotmail.com , @gmail.com , @yahoo.com ).
 - Не было указано правильное обоснование или бизнес-сценарий

Учитывая спрос из разных сегментов клиента, мы пытаемся ускорить процесс утверждения. Однако не удается выполнить фиксацию на временной шкале. После принятия решения Группа разработчиков Майкрософт свяжется с вами и группой управления учетной записью для выполнения дальнейших действий. Мы ценим ваше понимание и терпение.

Если приложение утверждено, команда Майкрософт отправит сообщение электронной почты с подробными сведениями, документацией и рекомендациями. Cognitive Services сведения о ценах доступны [здесь](https://azure.microsoft.com/pricing/details/cognitive-services/).


В настоящее время перечисленные ниже службы предлагаются с помощью процесса ограничения:

## <a name="gated-web-apis"></a>Веб-интерфейсы с проверкой изменений

|Служба  |
|---------|
|Детектор аномалий v2     | 

## <a name="gated-online-containers"></a>Контейнеры в сети с проверкой изменений

| Служба                             | Контейнеры                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Компьютерное зрение][cv-containers]    | Чтение                                                                          |
| [Распознавание лиц][fa-containers]               | Распознавание лиц                                                                          |
| [Распознаватель документов][fr-containers]    | Распознаватель документов                                                               |
| [API службы "Речь"][sp-containers] | Преобразование речи в текст (пользовательский и стандартный) и преобразование текста в речь (пользовательский и стандартный) |
| [Перевод текстов][tt-containers]    | Перевод текстов                                                               |

> [!IMPORTANT]
> Если служба или контейнерное предложение отсутствуют в списке, они не включены в шлюз или недоступны.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация в службах с проверкой изменений](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
