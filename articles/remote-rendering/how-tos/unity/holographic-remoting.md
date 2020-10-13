---
title: Использование голографического удаленного взаимодействия и Удаленной отрисовки в Unity
description: Использование предварительной версии holographic для удаленного взаимодействия с удаленной визуализацией Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802180"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Использование голографического удаленного взаимодействия и Удаленной отрисовки в Unity

[Holographic удаленного взаимодействия](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) и удаленная визуализация Azure являются взаимоисключающими в рамках одного приложения. Таким образом, [режим воспроизведения Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) также недоступен.

Для каждого запуска редактора Unity можно использовать только одно из двух. Чтобы использовать другой, сначала перезапустите Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Использование режима воспроизведения Unity для предварительного просмотра в HoloLens 2

 Режим воспроизведения Unity по-прежнему можно использовать, например, для тестирования пользовательского интерфейса приложения. Однако крайне важно, чтобы ARR никогда не инициализирулись. В противном случае произойдет сбой.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Использование гарнитуры ВМР VR для предварительного просмотра на рабочем столе

Если имеется гарнитура VR Windows Mixed Reality, ее можно использовать для предварительного просмотра внутри Unity. В этом случае можно инициализировать ARR, однако при использовании гарнитуры ВМР подключение к сеансу будет невозможно.
