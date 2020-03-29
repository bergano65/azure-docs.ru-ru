---
title: Экспорт и удаление данных — пользовательская служба визуального распознавания
titleSuffix: Azure Cognitive Services
description: Вы сохраняете полный контроль над своими данными. В этой статье объясняется, как можно просматривать, экспортировать или удалять данные в службе пользовательского зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718968"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Экспорт и удаление данных пользователя в Пользовательской службе визуального распознавания

Пользовательское зрение собирает пользовательские данные для работы службы, но клиенты имеют полный контроль над просмотром, экспортом и удаляем их данные с помощью AIS Custom Vision [Training.](https://go.microsoft.com/fwlink/?linkid=865446)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Чтобы узнать, как экспортировать и удалять пользовательские данные в Custom Vision, смотрите следующую таблицу.

| Данные | Операция экспорта | Операция удаления |
| ---- | ---------------- | ---------------- |
| Сведения об учетной записи (ключи подписки) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются с помощью портала Azure (подписки Azure) или с помощью кнопки "Удалить учетную запись" на странице параметров CustomVision.ai (подписки учетной записи Майкрософт) | 
| Сведения об итерациях | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Сведения о производительности итераций | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Список итераций | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Проекты и сведения о проектах | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) и [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Теги образов | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) и [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Образы | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) и [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Экспортированные модели | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются при удалении учетной записи |
