---
title: Распознавание печатного и рукописного текста. Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с распознаванием печатного и рукописного текста на изображениях с помощью API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313183"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Распознавание печатного и рукописного текста

Компьютерное зрение может обнаруживать и извлекать печатный и рукописный текст из изображений различных объектов с различными поверхностями и фонами, например из квитанций, плакатов, визитных карточек, писем и настенных досок.

Функция распознавания текста очень схожа с функцией [оптического распознавания символов (OCR)](concept-extracting-text-ocr.md), но в отличие от OCR выполняется асинхронно и использует обновленные модели распознавания.

> [!NOTE]
> Сейчас доступна предварительная версия этой технологии. Пока что она может распознавать тексты только на английском языке.

## <a name="text-recognition-requirements"></a>Требования для распознавания текста

Компьютерное зрение может распознавать печатный и рукописный текст на изображениях, которые отвечают следующим требованиям:

- Изображение должно быть представлено в формате JPEG, PNG или BMP.
- Размер файла изображения должен быть меньше 4 МБ.
- Размер изображения должен быть от 50 x 50 до 4200 x 4200 пикселей.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [справочной документации по распознаванию текста](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200).