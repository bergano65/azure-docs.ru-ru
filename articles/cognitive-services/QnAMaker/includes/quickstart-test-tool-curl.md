---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f3a1a33b2fe859839deec587191b3b3a319c0cf8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77495302"
---
В этом кратком руководстве описывается, как получить ответ из базы знаний с помощью cURL.

## <a name="prerequisites"></a>предварительные требования

* Последние [**cURL**](https://curl.haxx.se/).
* Вам понадобится:
    * [Служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
    * Обученная и опубликованная база знаний с вопросами и ответами, на основе предыдущего [краткого руководства](../Quickstarts/add-question-metadata-portal.md), с настроенными метаданными и беседой.

> [!NOTE]
> Когда вы будете готовы создать ответ на вопрос из базы знаний, [обучите](../Quickstarts/create-publish-knowledge-base.md#save-and-train) и [опубликуйте](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) эту базу знаний. После публикации базы знаний на странице **публикации** отображаются параметры запроса HTTP для создания ответа. Вкладка **cURL** отображает параметры, необходимые для создания ответа из командной строки.

## <a name="use-metadata-to-filter-answer"></a>Применение метаданных для фильтрации ответов

Используйте базу знаний из предыдущего краткого руководства, чтобы запросить ответ на основе метаданных.

1. На странице **Параметры** базы знаний выберите вкладку **CURL**, чтобы увидеть пример команды cURL, используемой для создания ответа из базы знаний.
1. Скопируйте эту команду в редактируемую среду (например, текстовый файл), чтобы изменить ее. Измените значение вопроса следующим образом, чтобы метаданные `service:qna_maker` использовались в качестве фильтра для наборов из вопроса и ответа.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Этот вопрос состоит всего из одного слова (`size`), по которому может возвращаться любой набор из вопроса и ответа. Массив `strictFilters` указывает, что ответ нужно сократить до `qna_maker` ответов.

1. Ответ включает только тот ответ, который соответствует критериям фильтра. Следующий ответ cURL был отформатирован для удобства чтения:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Если существует набор вопросов и ответов, который не соответствует поисковому запросу, но соответствует фильтру, он не будет возвращен. Вместо этого возвращается общий ответ `No good match found in KB.`.

## <a name="use-debug-query-property"></a>Использование свойства debug в запросе

Отладочная информация поможет понять, как был определен возвращаемый ответ. Это иногда полезно, но необязательно. Чтобы создать ответ с отладочной информацией, добавьте свойство `debug`:

```json
Debug: {Enable:true}
```

1. Отредактируйте команду cURL, чтобы включить свойство отладки для получения дополнительной информации.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. Ответ включает необходимые сведения об ответе. В следующих выходных данных JSON, некоторые отладочные сведения заменены многоточием для краткости.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Использование тестовой базы знаний

Если вы хотите получить ответ от тестовой базы знаний, используйте в тексте свойство `isTest`.

Свойство является логическим значением.

```json
isTest:true
```

Команда cURL выглядит следующим образом:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

В ответе в формате JSON используется та же схема, что и в запросе к опубликованной базе знаний.

> [!NOTE]
> Если тестовая и опубликованная базы знаний полностью совпадают, незначительные вариации могут возникать из-за того, что тестовый индекс является общим для всех баз знаний в ресурсе.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Использование cURL для запроса ответа в беседе

1. В окне терминала с поддержкой cURL используйте оператор завершения общения с ботом со стороны пользователя, например ответом `Thank you`, в качестве вопроса. Других свойств устанавливать не нужно.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Выполните команду cURL и получите ответ JSON, включающий оценку и ответ.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Так как вопрос `Thank you` точно соответствует вопросу в беседе, достоверность QnA Maker составляет 100 %. QnA Maker также возвращает все связанные вопросы и свойство метаданных, содержащее сведения о теге метаданных беседы.

## <a name="use-curl-with-threshold-and-default-answer"></a>Использование cURL с пороговым значением и ответом по умолчанию

Вы можете запросить минимальный порог для возвращаемого ответа. Если указанное пороговое значение не достигается, возвращается ответ по умолчанию.

1. Используйте следующую команду cURL, заменив информацию своим собственным именем ресурса, идентификатором базы знаний и ключом конечной точки, чтобы запросить ответ на `size` с пороговым значением 80 % или выше. База знаний не должна найти этот ответ, так как оценка вопроса составляет всего 71 %, поэтому вместо него возвращается ответ по умолчанию, указанный при создании базы знаний.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Выполните команду cURL и получите ответ JSON, включающий оценку и ответ.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker возвращает оценку `0`. Это означает, что достоверного ответа нет, но также возвращается ответ по умолчанию.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Так как вопрос `Thank you` точно соответствует вопросу в беседе, достоверность QnA Maker составляет 100 %. QnA Maker также возвращает все связанные вопросы и свойство метаданных, содержащее сведения о теге метаданных беседы.

## <a name="use-curl-with-threshold-and-default-answer"></a>Использование cURL с пороговым значением и ответом по умолчанию

Вы можете запросить минимальный порог для возвращаемого ответа. Если указанное пороговое значение не достигается, возвращается ответ по умолчанию.

1. Добавьте свойство `threshold`, чтобы запросить ответ на `size` с пороговым значением 80 % или выше. База знаний не должна найти этот ответ, так как показатель вопроса равен 71 %. Результат возвращает ответ по умолчанию, указанный при создании базы знаний.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Выполните команду cURL и получите ответ JSON.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker возвращает оценку `0`. Это означает, что достоверного ответа нет, но также возвращается ответ по умолчанию.

1. Измените пороговое значение на 60 % и повторите запрос.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```

    Теперь JSON возвращает найденный ответ.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
