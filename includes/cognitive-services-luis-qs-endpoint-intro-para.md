---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 00ffb8f4777fd458c92a9e996c18cd6bfadbc45e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740807"
---
Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. Вы отправите намерение пользователя в виде текста в конечную точку прогнозирования HTTP для общедоступного приложения. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения. 

В этом кратком руководстве используется REST API конечной точки. Дополнительные сведения см. в [документации по API конечной точки](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Для работы с этой статьей вам понадобится бесплатная учетная запись [LUIS](https://www.luis.ai). 
