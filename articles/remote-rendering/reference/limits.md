---
title: Ограничения
description: Ограничения кода для функций SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417697"
---
# <a name="limitations"></a>Ограничения

Ряд функций имеют размер, количество или другие ограничения.

## <a name="azure-frontend"></a>Лазурный фронтенд

* Всего экземпляров AzureFrontend за процесс: 16.
* Всего экземпляров AzureSession в AzureFrontend: 16.

## <a name="objects"></a>Объекты

* Всего допустимых объектов одного типа (Entity, CutPlaneComponent и т.д.): 16 777 215.
* Всего допустимых активных плоскостей разреза: 8.

## <a name="materials"></a>Материалы

* Всего допустимых материалов в активе: 65 535.

## <a name="overall-number-of-polygons"></a>Общее количество полигонов

Допустимое количество полигонов для всех загруженных моделей зависит от размера VM, переданного в [управление сеансами REST API:](../how-tos/session-rest-api.md#create-a-session)

| Размер виртуальной машины | Максимальное количество полигонов |
|:--------|:------------------|
|standard| 20 миллионов |
|Премиум| без ограничений |


## <a name="platform-limitations"></a>Ограничения платформы

**Windows 10 настольный компьютер**

* Развертывание "PC Standalone" от Unity не поддерживается. Вместо этого используйте UWP.
* UWP/x86 является единственной поддерживаемой платформой UWP. UWP/x64 не поддерживается.

**Голеленс 2**

* [Рендер с функции фотокамеры](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) не поддерживается.
