---
title: Определение схемы приложения
description: Приложение LUIS представляется в `.json` или, а также `.lu` включает все объекты, сущности, пример фразы продолжительностью, функции и параметры.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 21b58f79ffd2baf553c6f8b07daa84473e620f77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599373"
---
# <a name="app-schema-definition"></a>Определение схемы приложения

Приложение LUIS представляется в `.json` или, а также `.lu` включает все объекты, сущности, пример фразы продолжительностью, функции и параметры.

## <a name="format"></a>Формат

При импорте и экспорте приложения выберите либо `.json` `.lu` .

|Формат|Сведения|
|--|--|
|`.json`| Стандартный формат программирования|
|`.lu`|Поддерживается [инструментами Bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)платформы Bot.|

## <a name="version-7x"></a>Версия 7. x

* При переходе к версии 7. x сущности представляются как вложенные сущности, изученные компьютером.
* Поддержка создания вложенных объектов, занятых компьютером, с помощью `enableNestedChildren` свойства для следующих API-интерфейсов разработки:
    * [Добавление метки](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Добавить метку пакета](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Метки проверки](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Предложить запросы к конечной точке для сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Предложить запросы к конечной точке для целей](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>Версия 6. x

* При переходе к версии 6. x для представления сущностей используется новая [сущность](reference-entity-machine-learned-entity.md) , предназначенная для компьютера.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Версия 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Переход к [API-интерфейсам разработки версии 3](luis-migration-authoring-entities.md)