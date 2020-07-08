---
title: Ограничения
description: Ограничения для кода функций пакета SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 17f98f452764abdc8458cdc38661d464ecb0a60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808550"
---
# <a name="limitations"></a>Ограничения

Ряд функций имеет ограничения по размеру, количеству или другим параметрам.

## <a name="azure-frontend"></a>Интерфейсный сервер Azure

* Всего экземпляров AzureFrontend на процесс: 16.
* Всего экземпляров AzureSession на AzureFrontend: 16.

## <a name="objects"></a>Объекты

* Общее допустимое количество объектов одного типа (Entity, CutPlaneComponent и т. д.): 16 777 215
* Общее допустимое количество активных сечений: 8.

## <a name="geometry"></a>Geometry

* Общее допустимое количество материалов в ресурсе: 65 535.
* Максимальная размерность одной текстуры: 16 384 x 16 384. Более крупные исходные текстуры будут масштабироваться по процессу преобразования.

## <a name="overall-number-of-polygons"></a>Общее число многоугольников

Допустимое количество многоугольников для всех загруженных моделей зависит от размера виртуальной машины, обрабатывающей [REST API управления сеансами](../how-tos/session-rest-api.md#create-a-session).

| Размер виртуальной машины | Максимальное число многоугольников |
|:--------|:------------------|
|standard| 20 млн |
|Премиум| без ограничений |

Более подробные сведения об этом ограничении см. в разделе [Размер виртуальной машины](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Ограничения платформы

**Windows 10 Desktop**

* Поддерживается только платформа UWP/x86. UWP/x64 не поддерживается.
* Win32/x64 является единственной поддерживаемой платформой Win32. Win32/x86 не поддерживаются.

**Hololens 2**

* Визуализация [данных с камеры](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) не поддерживается.
