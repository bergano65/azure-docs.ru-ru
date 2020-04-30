---
title: Ограничения
description: Ограничения на код для функций пакета SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417697"
---
# <a name="limitations"></a>Ограничения

Ряд функций имеет размер, количество или другие ограничения.

## <a name="azure-frontend"></a>Интерфейсный сервер Azure

* Всего экземпляров Азурефронтенд на процесс: 16.
* Всего экземпляров Азуресессион на Азурефронтенд: 16.

## <a name="objects"></a>Объекты

* Общее количество допустимых объектов одного типа (сущность, Кутпланекомпонент и т. д.): 16 777 215.
* Общая допустимая активная вырезанная плоскости: 8.

## <a name="materials"></a>Материалы

* Общее количество допустимых материалов в ресурсе: 65 535.

## <a name="overall-number-of-polygons"></a>Общее число многоугольников

Допустимое количество многоугольников для всех загруженных моделей зависит от размера виртуальной машины, передаваемой [REST API управления сеансами](../how-tos/session-rest-api.md#create-a-session):

| Размер виртуальной машины | Максимальное число многоугольников |
|:--------|:------------------|
|standard| 20 000 000 |
|приоритет| без ограничений |


## <a name="platform-limitations"></a>Ограничения платформы

**Windows 10 Desktop**

* Развертывание "автономного ПК" из Unity не поддерживается. Вместо этого используйте UWP.
* UWP/x86 — единственная поддерживаемая платформа UWP. UWP/x64 не поддерживается.

**Hololens 2**

* Функция [рендеринга с камеры PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) не поддерживается.
