---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752850"
---
## <a name="create-a-spatial-anchors-resource"></a>Создание ресурса Пространственных привязок

1. Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>.

2. На портале Azure в меню слева выберите **Создать ресурс**.

3. Выполните поиск по запросу "Пространственные привязки" в строке поиска.

   ![Поиск Пространственных привязок](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Выберите пункт **Spatial Anchors (preview)** (Пространственные привязки (предварительная версия)), чтобы открыть диалоговое окно, а затем щелкните **Создать**.

5. В форме **Учетная запись Пространственных привязок**:

   1. Укажите уникальное имя ресурса.
   2. Выберите подписку, к которой нужно присоединить ресурс.
   3. Создайте группу ресурсов, выбрав **Создать**, и назовите ее **myResourceGroup**, а затем нажмите кнопку **ОК**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Выберите расположение (регион), где будет размещаться ресурс.
   5. Выберите **Создать**, чтобы начать создание ресурса.

   ![Создание ресурса](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. После успешного создания ресурса можно просмотреть его свойства. Запишите значение **идентификатора учетной записи** ресурса, так как оно понадобится позже.

   ![Просмотр свойств ресурса](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. В разделе **Параметры** выберите **Ключ** и запишите значение параметра **Первичный ключ**. Это ключ учетной записи (`Account Key`). Он будет использоваться позже.

   ![Просмотр ключа учетной записи](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
