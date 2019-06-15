---
title: Устаревшие когнитивные навыки (служба "Поиск Azure")
description: Эта страница содержит список навыков когнитивного поиска, которые считаются устаревшими и уже вскоре не будут поддерживаться.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a73c7e381cb6001b773251a1812466b3c82373f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541729"
---
# <a name="deprecated-cognitive-search-skills"></a>Навыки устаревшие когнитивный поиск

В этом документе описываются когнитивные навыки, которые считаются устаревшими. Ознакомьтесь со следующими понятиями:

* Имя навыка. Имя навыка, который станет устаревшим. Оно сопоставляется с атрибутом @odata.type.
* Последняя доступная версия API. Последняя версия общедоступного API службы поиска Azure, через которую наборы навыков, содержащие соответствующий устаревший навык, могут быть созданы или обновлены.
* Прекращение поддержки. Последний день, после которого соответствующий навык не будет поддерживаться. Ранее созданные наборы навыков по-прежнему будут работать, но пользователям рекомендуется прекратить их использование.
* Рекомендации Способ перехода для использования поддерживаемого навыка. Пользователям следует придерживаться этих рекомендаций, чтобы продолжать получать поддержку.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Последняя доступная версия API

2019-05-06-preview

### <a name="end-of-support"></a>Дата окончания поддержки

15 февраля 2019 г.

### <a name="recommendations"></a>Рекомендации 

Теперь следует использовать тип [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Он предоставляет большинство функций NamedEntityRecognitionSkill более высокого качества. Он также содержит более полные сведения в составных полях выходных данных.

Для перехода на [навык распознавания сущностей](cognitive-search-skill-entity-recognition.md) необходимо внести одно или несколько следующих изменений в определение навыка. Вы можете обновить определение навыков с помощью [API обновления набора навыков](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Сейчас оценка достоверности не поддерживается в качестве концепции. Параметр `minimumPrecision` содержится в `EntityRecognitionSkill` для использования в будущем, а также для обеспечения обратной совместимости.

1. *(Обязательно.)* Измените `@odata.type` с `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` на `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Необязательно.)* Если вы используете выходные данные `entities`, лучше укажите сложные выходные данные коллекции `namedEntities` из `EntityRecognitionSkill`. В определении навыка можно задать `targetName`, чтобы сопоставить его с заметкой с именем `entities`.

3. *(Необязательно.)* Если вы явно не укажете `categories`, `EntityRecognitionSkill` может возвратить другой тип категорий, кроме тех, которые поддерживались `NamedEntityRecognitionSkill`. Если такое поведение нежелательно, явно задайте для параметра `categories` значение `["Person", "Location", "Organization"]`.

    _Примеры изменения определений_

    * Простое изменение

        _Раньше — определение навыка NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _Теперь — определение навыка EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Немного сложное изменение

        _Раньше — определение навыка NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _Теперь — определение навыка EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Навык распознавания сущностей](cognitive-search-skill-entity-recognition.md)
