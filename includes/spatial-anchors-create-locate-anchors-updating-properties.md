---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185000"
---
## <a name="update-properties"></a>Обновление свойств

Для обновления свойства привязки, можно использовать `UpdateAnchorProperties()` метод. Если два или несколько устройств для обновления свойств для одной привязкой, в то же время, мы используем модель оптимистичного параллелизма. Это означает, что первой записи будет предлагаться по умолчанию.  Все операции записи будут получать ошибки «Concurrency»: обновление свойства, которое необходимо перед повторной попыткой.
