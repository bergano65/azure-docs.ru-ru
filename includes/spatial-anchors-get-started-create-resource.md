---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "83638987"
---
## <a name="create-a-spatial-anchors-resource"></a>Создание ресурса Пространственных привязок

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>.

В левой области навигации портала Azure нажмите **Создать ресурс**.

Выполните поиск по запросу **Пространственные привязки** с помощью поля поиска.

   ![Поиск Пространственных привязок](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Нажмите **Пространственные привязки**. В диалоговом окне выберите **Создать**.

В диалоговом окне **Учетная запись Пространственных привязок**:

- Введите уникальное имя ресурса, используя обычные буквенно-цифровые символы.
- Выберите подписку, к которой нужно присоединить ресурс.
- Создайте группу ресурсов, нажав **Создать**. Назовите ее **myResourceGroup** и нажмите **ОК**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Выберите расположение (регион), в котором будет размещен ресурс.
- Выберите **Создать**, чтобы начать создание ресурса.

   ![Создание ресурса](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

После создания ресурса на портале Azure отобразится оповещение о завершении развертывания. Щелкните **Перейти к ресурсу**.

![Развертывание завершено](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Теперь можно просмотреть свойства ресурса. Скопируйте значение **идентификатора учетной записи** ресурса в текстовый редактор, так как оно понадобится позднее.

   ![Свойства ресурса](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Скопируйте также значение **домена учетной записи** ресурса в текстовый редактор, так как оно понадобится позднее.

   ![Домен учетной записи](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

В меню **Параметры** выберите **Ключ**. Скопируйте значение **первичного ключа** в текстовый редактор. Это значение `Account Key`. Он понадобится вам позднее.

   ![Ключ учетной записи](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
