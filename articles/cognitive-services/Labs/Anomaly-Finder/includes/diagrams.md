---
title: включение файла
description: включение файла
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228973"
---
Возвращенные данные с ожидаемым значением и верхней и нижней границами по умолчанию. На практике можно определить параметр [sensitivity], а затем использовать (ExpectedValue + sensitivity * UpperMargin) в качестве верхней границы, а (ExpectedValue - sensitivity * LowerMargin) — в качестве нижней границы для самостоятельной настройки точек аномалий. Значение [sensitivity] должно быть больше 1. Ниже приведены несколько диаграмм для настройки.

> [!NOTE]
> Этим диаграммы не создаются с помощью примера приложения. Они создаются с помощью отдельного средства, используемого с примером приложения.

![Настройка: sensitivity = 1.0](../media/sensitivity_1.png)
![Настройка: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Настройка: sensitivity = 2](../media/sensitivity_2.png)
![Настройка: sensitivity = 3.5](../media/sensitivity_3.5.png)
