---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305113"
---
## <a name="create-a-spatial-anchors-resource"></a>Создание ресурса Пространственных привязок

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>.

В левой области навигации портала Azure нажмите **Создать ресурс**.

Выполните поиск по запросу **Пространственные привязки** с помощью поля поиска.

   ![Поиск Пространственных привязок](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Нажмите **Пространственные привязки**. В диалоговом окне выберите **Создать**.

В диалоговом окне **Учетная запись Пространственных привязок**:

- Введите уникальное имя ресурса.
- Выберите подписку, к которой нужно присоединить ресурс.
- Создайте группу ресурсов, нажав **Создать**. Назовите ее **myResourceGroup** и нажмите **ОК**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Выберите расположение (регион), в котором будет размещен ресурс.
- Выберите **Создать**, чтобы начать создание ресурса.

   ![Создание ресурса](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

После создания ресурса вы можете просмотреть его свойства. Скопируйте значение **идентификатора учетной записи** ресурса в текстовый редактор, так как оно понадобится позднее.

   ![Свойства ресурса](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

В меню **Параметры** выберите **Ключ**. Скопируйте значение **первичного ключа** в текстовый редактор. Это значение `Account Key`. Он понадобится вам позднее.

   ![Ключ учетной записи](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
