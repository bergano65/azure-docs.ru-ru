---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907779"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановить, сброс или остановить сеанс

Чтобы остановить сеанс временно, можно вызвать Stop(). Это приведет к остановке любой наблюдателей и обработки в среде, даже если вы вызываете ProcessFrame(). Затем можно вызвать Start() для продолжения процесса. Во время возобновления работы сохраняется данные среды, уже собранных в сеансе.
