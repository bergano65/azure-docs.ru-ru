---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232322"
---
## <a name="pause-reset-or-stop-the-session"></a>Приостановить, сброс или остановить сеанс

Чтобы остановить сеанс временно, можно вызвать `Stop()`. Это приведет к остановке любой наблюдателей и обработки в среде, даже если вы вызываете ProcessFrame(). После этого можно вызывать `Start()` для продолжения процесса. Во время возобновления работы сохраняется данные среды, уже собранных в сеансе.
