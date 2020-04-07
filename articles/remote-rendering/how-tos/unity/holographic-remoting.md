---
title: Используйте голографические ремотивации и удаленного рендеринга в единстве
description: Как предварительный просмотр Holographic Remoting может быть использован в сочетании с удаленной визуализацией Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681211"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Используйте голографические ремотивации и удаленного рендеринга в единстве

[Голографические ремотирование](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) и удаленное рендеринг Azure являются взаимоисключающими в рамках одного приложения. Таким образом, [режим воспроизведения Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) также недоступен.

Для каждого запуска редактора Unity можно использовать только один из двух. Чтобы использовать другой, перезапустите Unity первым.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Используйте режим воспроизведения Unity для предварительного просмотра на Hololens 2

 Режим воспроизведения Unity все еще может использоваться, например, для тестирования единообразного взаимодействия приложения. Тем не менее, очень важно, чтобы ARR никогда не инициализировался. В противном случае он рухнет.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Используйте гарнитуру WMR VR для предварительного просмотра на рабочем столе

Если гарнитура Windows Mixed Reality VR присутствует, ее можно использовать для предварительного просмотра внутри Unity. В этом случае можно настроить инициализацию ARR, однако при использовании гарнитуры WMR невозможно подключиться к сеансу.
