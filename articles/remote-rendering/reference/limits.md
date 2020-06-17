---
title: Ограничения
description: Ограничения для кода функций пакета SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759053"
---
# <a name="limitations"></a>Ограничения

Ряд функций имеет ограничения по размеру, количеству или другим параметрам.

## <a name="azure-frontend"></a>Интерфейсный сервер Azure

* Всего экземпляров AzureFrontend на процесс: 16.
* Всего экземпляров AzureSession на AzureFrontend: 16.

## <a name="objects"></a>Объекты

* Общее допустимое количество объектов одного типа (Entity, CutPlaneComponent и т. д.): 16 777 215
* Общее допустимое количество активных сечений: 8.

## <a name="materials"></a>Материалы

* Общее допустимое количество материалов в ресурсе: 65 535.

## <a name="overall-number-of-polygons"></a>Общее число многоугольников

Допустимое количество многоугольников для всех загруженных моделей зависит от размера виртуальной машины, обрабатывающей [REST API управления сеансами](../how-tos/session-rest-api.md#create-a-session).

| Размер виртуальной машины | Максимальное число многоугольников |
|:--------|:------------------|
|standard| 20 млн |
|Премиум| без ограничений |


## <a name="platform-limitations"></a>Ограничения платформы

**Windows 10 Desktop**

* Поддерживается только платформа UWP/x86. UWP/x64 не поддерживается.

**Hololens 2**

* Визуализация [данных с камеры](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) не поддерживается.
