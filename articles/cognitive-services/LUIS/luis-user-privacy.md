---
title: Экспортные & удалять данные - LUIS
titleSuffix: Azure Cognitive Services
description: Вы имеете полный контроль над просмотром, экспортом и удаляем их данными. Удаление данных клиентов для обеспечения конфиденциальности и соответствия требованиям.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273361"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Экспорт и удаление данных клиентов в Интеллектуальной службе распознавания речи (LUIS) в Cognitive Services

Удаление данных клиентов для обеспечения конфиденциальности и соответствия требованиям.

## <a name="summary-of-customer-data-request-features"></a>Сводка о возможностях запроса данных клиента
Интеллектуальная служба распознавания речи (LUIS) сохраняет клиентское содержимое для своей работы. При этом пользователи LUIS могут полностью управлять просмотром, экспортом и удалением своих данных. Это можно сделать через [веб-портал](luis-reference-regions.md) LUIS или [LUIS Авторинг (также известный как программные) AIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Клиентское содержимое хранится в зашифрованном виде в региональном хранилище Microsoft Azure и включает в себя следующее:

- Содержимое учетных записей пользователей, собранное при регистрации.
- Учебные данные, необходимые для построения моделей
- Зарегистрированные пользовательские запросы, используемые [активным обучением,](luis-concept-review-endpoint-utterances.md) чтобы помочь улучшить модель
  - Пользователи могут отключить ведение журнала запросов, добавив `&log=false` к запросу. Дополнительные сведения см. [здесь](troubleshooting.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Удаление данных клиента
Пользователи LUIS имеют полный контроль для удаления любого пользовательского контента, либо через веб-портал LUIS, либо на apis LUIS Authoring (также известный как Программные). В следующей таблице представлены ссылки, помогающие освоить оба способа:

| | **Учетная запись пользователя** | **Приложение** | **Пример высказывания (ы)** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Портал** | [Ссылку](luis-concept-data-storage.md#delete-an-account) | [Ссылку](luis-how-to-start-new-app.md#delete-app) | [Ссылку](luis-concept-data-storage.md#utterances-in-an-intent) | [Активное обучение высказывания](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Зарегистрированные высказывания](luis-concept-data-storage.md#disable-logging-utterances) |
| **Api** | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Экспорт данных клиента
Пользователи LUIS имеют полный контроль для просмотра данных на портале, однако они должны экспортироваться через APIs LUIS Authoring (также известный как Программные). В следующей таблице отображаются ссылки, помогающие экспорту данных через APIs LUIS Authoring (также известный как Программные):

| | **Учетная запись пользователя** | **Приложение** | **Высказывание (ы)** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Api** | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ссылку](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Расположение активного обучения

Для обеспечения [активного обучения,](luis-how-to-review-endpoint-utterances.md#enable-active-learning)зарегистрированные высказывания пользователей, полученные в опубликованных конечных точках LUIS, хранятся в следующих географических регионах Azure:

* [Европа](#europe)
* [Австралия](#australia)
* [США](#united-states)

За исключением данных об активном обучении (подробно описано ниже), LUIS следует [практике хранения данных для региональных служб.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Европа

Портал [eu.luis.ai](https://eu.luis.ai) и Авторинг в Европе (также известный как программные AIS) размещаются в географии Azure в Европе. Портал eu.luis.ai и авторство Европы (также известный как программные AIS) поддерживают развертывание конечных точек в следующих географических регионах Azure:

* Европа
* Франция
* United Kingdom

При развертывании в этих географических регионах Azure высказывания, полученные конечными пользователями приложения, будут храниться в географии Azure в Европе для активного обучения. Вы можете отключить активное обучение, увидеть [отключить активное обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления сохраненные высказывания, см [Удалить высказывание](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Австралия

Портал [au.luis.ai](https://au.luis.ai) и Австралия Авторинг (также известный как программные AIS) размещаются в географии Azure в Австралии. Портал au.luis.ai и Австралия Авторинг (также известный как программные AIS) поддерживают развертывание конечных точек для следующих географических регионов Azure:

* Австралия

При развертывании в этих географических регионах Azure высказывания, полученные конечными пользователями приложения, будут храниться в австралийской географии Azure для активного обучения. Вы можете отключить активное обучение, увидеть [отключить активное обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления сохраненные высказывания, см [Удалить высказывание](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>США

Портал [luis.ai](https://www.luis.ai) и американские авторские (также известные как программные AIS) размещаются в географии Azure в США. Портал luis.ai и авторство США (также известный как программные AIS) поддерживают развертывание конечных точек в следующих географических регионах Azure:

* Географические регионы Лазурного региона, не поддерживаемые регионами, авторами которых являются Европа или Австралия

При развертывании в этих географических регионах Azure высказывания, полученные конечными пользователями приложения, будут храниться в американской географии Azure для активного обучения. Вы можете отключить активное обучение, увидеть [отключить активное обучение](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Для управления сохраненные высказывания, см [Удалить высказывание](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по регионам LUIS](./luis-reference-regions.md)
