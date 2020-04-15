---
title: Руководство по Проверка речевых фрагментов конечной точки — LUIS
description: Из этого руководства вы узнаете, как повысить точность прогнозирования в приложении, проверяя или корректируя речевые фрагменты, полученные через неизвестную для LUIS конечную точку HTTP службы распознавания речи. Некоторые высказывания могут быть проверены на наличие намерений, а другие — на наличие сущностей.
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 307c18d3326cb1a64b884463a571985a015834ed
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548725"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Руководство по Исправление неточных прогнозов с помощью проверки высказываний конечной точки
Из этого учебника вы узнаете, как повысить точность прогнозирования приложения, проверяя или корректируя речевые фрагменты, полученные через неизвестную для LUIS конечную точку HTTP интеллектуальной службы распознавания речи. Речевые фрагменты конечных точек необходимо проверять в рамках планового обслуживания LUIS.

Этот процесс проверки позволяет обучить LUIS для использования вашего домена приложений. В LUIS выбираются те речевые фрагменты, которые отображаются в списке проверки. Этот список:

* относится к конкретному приложению;
* предназначен для повышения точности прогнозирования в приложении;
* должен периодически проверяться.

Проверив фрагменты речи конечной точки, вы подтверждаете или исправляете прогнозируемое намерение фрагмента речи.

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Импортировать пример приложения
> * Просмотр речевых фрагментов конечной точки
> * Обучение и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Импортировать пример приложения

Чтобы импортировать приложение, сделайте следующее:

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Обучение приложения для внесения изменений сущности в приложение

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Публикация приложения для доступа к нему через конечную точку HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Добавление речевого фрагмента в конечную точку

В этом приложении у вас есть намерения и сущности, но отсутствует использование конечных точек. Использование конечной точки необходимо для улучшения приложения путем проверки речевых фрагментов конечной точки.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Перейдите в конец URL-адреса в адресной строке и замените _YOUR_QUERY_HERE_ на речевые фрагменты из приведенной ниже таблицы. Для каждого речевого фрагмента отправьте такой фрагмент и получите результат. Затем замените речевой фрагмент в конце строки на следующий речевой фрагмент.

    |Речевой фрагмент конечной точки|Сопоставленное намерение|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Для проверки используется единственный пул высказываний независимо от того, какую версию вы активно редактируете или какая версия приложения опубликована в конечной точке.

## <a name="review-endpoint-utterances"></a>Просмотр речевых фрагментов конечной точки

Просмотрите, правильно ли сопоставлено намерение для речевых фрагментов в конечной точке. Хотя существует единый пул речевых фрагментов для просмотра во всех версиях, процесс правильного сопоставления намерения добавляет пример речевого фрагмента только к текущей _активной модели_.

1. В разделе **Сборка** портала выберите **Review endpoint utterances** (Просмотреть речевые фрагменты конечной точки) на панели навигации слева. Список будет отфильтрован для намерения **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с кнопкой Review endpoint utterances (Просмотреть речевые фрагменты конечной точки) на панели навигации слева](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Этот фрагмент речи, `I'm looking for a job with Natural Language Processing`, неправильно соотнесен с намерением.

1.  Чтобы сопоставить этот фрагмент речи, в строке фрагмента выберите правильное **сопоставленное намерение**`GetJobInformation`. Добавьте измененный речевой фрагмент в приложение, установив флажок.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с кнопкой Review endpoint utterances (Просмотреть речевые фрагменты конечной точки) на панели навигации слева](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Изучите оставшиеся речевые фрагменты в этом намерении и исправьте сопоставленное намерение при необходимости. Используйте таблицу изначальных речевых фрагментов в этом учебнике, чтобы просмотреть сопоставленное намерение.

    Список **проверки речевых фрагментов конечной точки** больше не должен содержать такие речевые фрагменты. Если появляется больше речевых фрагментов, продолжайте работу по списку, исправляя сопоставленные намерения, пока этот список не опустеет.

    Любое исправление метки сущности выполняется после того, как намерение будет согласовано на странице сведений о намерениях.

1. Обучите и опубликуйте приложение еще раз.

## <a name="get-intent-prediction-from-endpoint"></a>Получение прогноза намерений из конечной точки

Чтобы проверить, что правильно сопоставленные примеры речевых фрагментов улучшили прогнозирование в приложении, попробуйте использовать фрагмент речи, близкий к исправленному.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Перейдите в конец URL-адреса в адресной строке и замените _YOUR_QUERY_HERE_ на `Are there any natural language processing jobs in my department right now?`.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Теперь, когда неточные речевые фрагменты правильно сопоставлены, правильное намерение будет предсказано с **высокой вероятностью**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Можно ли вместо проверки добавить больше фрагментов речи?
У вас может возникнуть вопрос, почему бы не добавить больше примеров фрагментов речи. Какова цель проверки фрагментов речи конечной точки? В реальном приложении LUIS фрагменты речи конечной точки поступают от пользователей после выбора и организации слов. Эти операции вы еще не выполняли. Если бы вы использовали выбор и организацию одинаковых слов, для исходного прогноза процент был бы выше.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Почему намерения с самым высоким показателем находятся в списке фрагментов речи?
Некоторые высказывания конечной точки будут иметь высокую оценку прогнозирования в списке проверки. Их по-прежнему нужно проверить и подтвердить. Они входят в список, так как оценка следующего намерения с самым высоким показателем слишком близка к самой высокой оценке намерения. Между двумя верхними намерениями разница должна составлять около 15 %.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как просматривать высказывания, отправленные неизвестной для LUIS конечной точке. После того как эти высказывания будут проверены и перенесены в правильные намерения в качестве примеров высказываний, точность прогнозов LUIS увеличится.

> [!div class="nextstepaction"]
> [Сведения об использовании шаблонов](luis-tutorial-pattern.md)
