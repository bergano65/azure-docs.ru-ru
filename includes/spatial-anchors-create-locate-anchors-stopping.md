---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110615"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановить, сброс или остановить сеанс

Чтобы остановить сеанс временно, можно вызвать `Stop()`. Это приведет к остановке любой наблюдателей и обработки в среде, даже если вы вызываете ProcessFrame(). После этого можно вызывать `Start()` для продолжения процесса. Во время возобновления работы сохраняется данные среды, уже собранных в сеансе.
