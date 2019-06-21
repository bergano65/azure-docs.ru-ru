---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185008"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановить, сброс или остановить сеанс

Чтобы остановить сеанс временно, можно вызвать `Stop()`. Это приведет к остановке любой наблюдателей и обработки в среде, даже если вы вызываете ProcessFrame(). После этого можно вызывать `Start()` для продолжения процесса. Во время возобновления работы сохраняется данные среды, уже собранных в сеансе.
