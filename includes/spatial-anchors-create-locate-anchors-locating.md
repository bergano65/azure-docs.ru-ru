---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907814"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Найдите пространственных привязку в облаке

Чтобы найти пространственных привязки облака, необходимо знать их идентификаторы. Идентификатор привязки может быть, хранимых в службе серверной части приложения и доступны для всех устройств, которые могут неправильно проходить его. Пример см. в разделе [руководства: Совместное использование пространственных привязки между устройствами](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Создать экземпляр объекта AnchorLocateCriteria, идентификаторы, которые вы ищете и вызвать метод CreateWatcher в сеансе, предоставляя вашей AnchorLocateCriteria наборов.
