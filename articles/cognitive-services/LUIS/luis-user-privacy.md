---
title: Экспорт & удаление данных — LUIS
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
ms.openlocfilehash: e234f88d6e735f33be253cacb373baef63c605c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559990"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Экспорт и удаление данных клиентов в Интеллектуальной службе распознавания речи (LUIS) в Cognitive Services

Удаление данных клиента для обеспечения конфиденциальности и соответствия требованиям. 

## <a name="summary-of-customer-data-request-features"></a>Сводка о возможностях запроса данных клиента
Интеллектуальная служба распознавания речи (LUIS) сохраняет клиентское содержимое для своей работы. При этом пользователи LUIS могут полностью управлять просмотром, экспортом и удалением своих данных. Это можно сделать с помощью веб- [портала](luis-reference-regions.md) Luis или [API Luis (также называемого программным интерфейсом)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Клиентское содержимое хранится в зашифрованном виде в региональном хранилище Microsoft Azure и включает в себя следующее:

- Содержимое учетных записей пользователей, собранное при регистрации.
- Обучающие данные, необходимые для построения моделей
- Зарегистрированные пользовательские запросы, используемые в [активном обучении](luis-concept-review-endpoint-utterances.md) для улучшения модели
  - Пользователи могут отключить ведение журнала запросов, добавив `&log=false` к запросу. Дополнительные сведения см. [здесь](troubleshooting.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Удаление данных клиента
Пользователи LUIS имеют полный доступ для удаления любого содержимого пользователя с помощью веб-портала LUIS или LUIS разработки (также называемого программным интерфейсом). В следующей таблице представлены ссылки, помогающие освоить оба способа:

| | **Учетная запись пользователя** | **Приложения** | **Пример utterance (s)** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Портал** | [Ссылка](luis-concept-data-storage.md#delete-an-account) | [Ссылка](luis-how-to-start-new-app.md#delete-app) | [Ссылка](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning фразы продолжительностью](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Зарегистрированные фразы продолжительностью](luis-concept-data-storage.md#disable-logging-utterances) |
| **Программные интерфейсы** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Экспорт данных клиента
Пользователи LUIS имеют полный доступ для просмотра данных на портале, однако их необходимо экспортировать с помощью API-интерфейсов LUIS (также называемых программными средствами). В следующей таблице приведены ссылки, помогающие при экспорте данных с помощью API-интерфейсов LUIS Authoring (также известных как программные):

| | **Учетная запись пользователя** | **Приложения** | **Фразы** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Программные интерфейсы** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Расположение активного обучения

Чтобы включить [активное обучение](luis-how-to-review-endpoint-utterances.md#enable-active-learning), пользователи, выполнившие вход в систему фразы продолжительностью, полученные на опубликованных конечных точках Luis, хранятся в следующих географических регионах Azure:

* [Европа](#europe)
* [Австралия](#australia)
* [США](#united-states)

За исключением активных обучающих данных (подробное описание приведено ниже), LUIS следует рекомендациям по [хранению данных для региональных служб](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Европа

[EU.Luis.AI](https://eu.luis.ai) портал и разработка в Европе (также известные как программные интерфейсы API) размещаются в Европе Azure. Eu.luis.ai портал и разработка в Европе (также известные как программные интерфейсы API) поддерживают развертывание конечных точек в следующих географических регионах Azure:

* Европа
* Франция
* Соединенное Королевство

При развертывании в этих географических регионах Azure фразы продолжительностью, полученные конечной точкой от конечных пользователей вашего приложения, будут храниться в Европе Azure для активного обучения. Вы можете отключить активное обучение. см. раздел [Отключение активного обучения](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Сведения об управлении хранимыми фразы продолжительностью см. в разделе [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Австралия

[Au.Luis.AI](https://au.luis.ai) портал и создание Австралии (также известные как программные интерфейсы API) размещаются в географическом регионе Azure. Au.luis.ai портал и создание Австралии (также называемые программными интерфейсами API) поддерживают развертывание конечных точек в следующих географических регионах Azure:

* Австралия

При развертывании в этих географических регионах Azure фразы продолжительностью, полученные конечной точкой от конечных пользователей вашего приложения, будут храниться в географическом регионе Azure в Австралии для активного обучения. Вы можете отключить активное обучение. см. раздел [Отключение активного обучения](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Сведения об управлении хранимыми фразы продолжительностью см. в разделе [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>США

[Luis.AI](https://www.luis.ai) портал и США разработки (также известные как программные интерфейсы API) размещаются в США географии Azure. Luis.ai портал и США разработки (также известные как программные интерфейсы API) поддерживают развертывание конечных точек в следующих географических регионах Azure:

* Географические диаграммы Azure не поддерживаются в регионах разработки в Европе или Австралии

При развертывании в этих географических регионах Azure фразы продолжительностью, полученные конечной точкой от конечных пользователей вашего приложения, будут храниться в США географии Azure для активного обучения. Вы можете отключить активное обучение. см. раздел [Отключение активного обучения](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Сведения об управлении хранимыми фразы продолжительностью см. в разделе [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Справочник по регионам LUIS](./luis-reference-regions.md)
