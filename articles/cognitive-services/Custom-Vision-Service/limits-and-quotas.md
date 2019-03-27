---
title: Ограничения и квоты. Пользовательская служба визуального распознавания
titlesuffix: Azure Cognitive Services
description: Дополнительные сведения об ограничениях и квотах в пользовательской службе визуального распознавания.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472446"
---
# <a name="limits-and-quotas"></a>Ограничения и квоты

Существует два уровня ключей для компьютерного зрения пользовательские службы. Вы можете зарегистрировать подписку F0 (бесплатная) или S0 (стандартная) на портале Azure. Дополнительные сведения о ценах и транзакциях см. на соответствующей [странице с ценами на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).

Число учебных изображений на проект и число тегов на проект для проектов категории S0 со временем будет увеличено.

||**F0**|**S0**|
|-----|-----|-----|
|Проекты|2|100|
|Число учебных изображений на проект |5 000|100 000|
|Число прогнозов в месяц|10 000 |Без ограничений|
|Число тегов на проект|50|500|
|Количество итераций |10|10|
|Min с меткой изображений на тег, классификации (рекомендуется в 50 +) |5|5|
|Min с меткой изображений на тег, обнаружения объекта (рекомендуется в 50 +)|15|15|
|Срок хранения прогнозных изображений|30 дней|30 дней|
|Число операций [Prediction](https://go.microsoft.com/fwlink/?linkid=865445) с хранилищем (транзакций в секунду)|2|10|
|Число операций [Prediction](https://go.microsoft.com/fwlink/?linkid=865445) без хранилища (транзакций в секунду)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (число вызовов API в секунду)|2|10|
|[Другие вызовы API](https://go.microsoft.com/fwlink/?linkid=865446) (число транзакций в секунду)|10|10|
|Максимальный размер изображения (при передаче учебного изображения) |6 МБ|6 МБ|
|Максимальный размер изображения (для прогнозирования)|4 МБ|4 МБ|
