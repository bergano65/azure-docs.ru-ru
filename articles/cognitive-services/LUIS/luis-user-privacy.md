---
title: Экспорт и удаление данных
titleSuffix: Azure Cognitive Services
description: Удаление данных клиента для обеспечения конфиденциальности и соответствия требованиям.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: a82452f4b41aee9c4ea6f269d92fbc91a5697d16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64916939"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Экспорт и удаление данных клиентов в Интеллектуальной службе распознавания речи (LUIS) в Cognitive Services

Удаление данных клиента для обеспечения конфиденциальности и соответствия требованиям. 

## <a name="summary-of-customer-data-request-features"></a>Сводка о возможностях запроса данных клиента
Интеллектуальная служба распознавания речи (LUIS) сохраняет клиентское содержимое для своей работы. При этом пользователи LUIS могут полностью управлять просмотром, экспортом и удалением своих данных. Это можно сделать с помощью веб-LUIS [портала](luis-reference-regions.md) или [LUIS разработки (также известный как программные) API-интерфейсы](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Клиентское содержимое хранится в зашифрованном виде в региональном хранилище Microsoft Azure и включает в себя следующее:

- Содержимое учетных записей пользователей, собранное при регистрации.
- Обучающие данные, необходимые для создания моделей
- Журнал запросов пользователя, используемых [активное Обучение](luis-concept-review-endpoint-utterances.md) в целях улучшения модели
  - Пользователи могут отключить ведение журнала запросов, добавив `&log=false` к запросу. Дополнительные сведения см. [здесь](troubleshooting.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Удаление данных клиента
LUIS пользователи имеют полный доступ для удаления любой пользователь, содержимого, с помощью LUIS веб-портала или API-интерфейсы LUIS разработки (также известен как программным способом). В следующей таблице представлены ссылки, помогающие освоить оба способа:

| | **Учетная запись пользователя** | **Приложения** | **Пример Utterance(s)** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Портал** | [Ссылка](luis-concept-data-storage.md#delete-an-account) | [Ссылка](luis-how-to-start-new-app.md#delete-app) | [Ссылка](luis-concept-data-storage.md#utterances-in-an-intent) | [Фразы продолжительностью активное обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Журнал фразы](luis-concept-data-storage.md#disable-logging-utterances) |
| **Интерфейсы API** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Экспорт данных клиента
LUIS пользователи имеют полный доступ для просмотра данных на портале, однако он должен быть экспортирован через API LUIS разработки (также известный как программный). В следующей таблице приведены ссылки, помогая Экспорт данных с помощью LUIS разработки (также известный как программный) API-интерфейсы:

| | **Учетная запись пользователя** | **Приложения** | **Фразы** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Интерфейсы API** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Расположение активное обучение

Чтобы включить [активное Обучение](luis-how-to-review-endpoint-utterances.md#enable-active-learning), зарегистрированные фразы пользователей, полученных в опубликованных конечных точек LUIS, хранятся в следующих географических расположений Azure:

* [Европа](#europe)
* [Австралия](#australia)
* [США](#united-states)

За исключением активное обучение данных (см. ниже), следует за LUIS [по использованию хранилища данных для региональных служб](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Европа

[Eu.luis.ai](https://eu.luis.ai) портала и Европе разработки (также известный как программные API-интерфейсы) размещаются в географическом регионе Azure в Европе. Портал eu.luis.ai и Европе разработки (также известный как программные API-интерфейсы) поддерживает развертывание конечных точек для следующих географических расположений Azure:

* Европа
* Франция
* Соединенное королевство

При развертывании на этих географических расположений Azure, фразы, полученных от конечной точки, от конечных пользователей приложения будут храниться в регионе Azure по Европе для активное обучение. Вы можете отключить активное обучение, см. в разделе [отключить активное Обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления хранимых фразы, см. в разделе [удалить utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Австралия

[Au.luis.ai](https://au.luis.ai) портала и Австралии разработки (также известный как программные API-интерфейсы) размещаются в географическом регионе Azure в Австралии. Портал au.luis.ai и Австралии разработки (также известный как программные API-интерфейсы) поддерживает развертывание конечных точек для следующих географических расположений Azure:

* Австралия

При развертывании на этих географических расположений Azure, фразы, полученных от конечной точки, от конечных пользователей приложения будут храниться в регионе Azure в Австралии для активное обучение. Вы можете отключить активное обучение, см. в разделе [отключить активное Обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления хранимых фразы, см. в разделе [удалить utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>США

[Luis.ai](https://www.luis.ai) портала и United States разработки (также известный как программные API-интерфейсы) размещаются в географическом регионе США Azure. Портал luis.ai и United States разработки (также известный как программные API-интерфейсы) поддерживает развертывание конечных точек для следующих географических расположений Azure:

* Географических расположений Azure, не поддерживаемые Европа или создания регионах Австралии

При развертывании на этих географических расположений Azure, фразы, полученных от конечной точки, от конечных пользователей приложения будут храниться в географическом регионе США Azure для активное обучение. Вы можете отключить активное обучение, см. в разделе [отключить активное Обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления хранимых фразы, см. в разделе [удалить utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по регионам LUIS](./luis-reference-regions.md)
