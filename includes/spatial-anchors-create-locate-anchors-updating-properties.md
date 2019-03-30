---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632476"
---
## <a name="update-properties"></a>Обновить свойства

Для обновления свойства привязки, можно использовать `UpdateAnchorProperties()` метод. Если два или несколько устройств для обновления свойств для одной привязкой, в то же время, мы используем модель оптимистичного параллелизма. Это означает, что первой записи будет предлагаться по умолчанию.  Все операции записи будут получать ошибки «Concurrency»: обновление свойства, которое необходимо перед повторной попыткой.
