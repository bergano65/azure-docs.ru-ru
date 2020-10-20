---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971395"
---
## <a name="create-a-spatial-anchors-resource"></a>Создание ресурса Пространственных привязок

Перейдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a>.

В области слева выберите **Создать ресурс**.

Выполните поиск по запросу **Пространственные привязки** с помощью поля поиска.

![Снимок экрана, показывающий результаты поиска по запросу "Пространственные привязки".](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Выберите **Пространственные привязки**, а затем щелкните **Создать**.

В области **Учетная запись Пространственных привязок** выполните следующие действия.

* Введите уникальное имя ресурса, используя обычные буквенно-цифровые символы.  
* Выберите подписку, к которой нужно присоединить ресурс.  
* Создайте группу ресурсов, нажав **Создать**. Назовите ее **myResourceGroup** и нажмите **ОК**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Выберите расположение (регион), в котором будет размещен ресурс.  
* Выберите **Создать**, чтобы начать создание ресурса.

![Снимок экрана: область Пространственных привязок для создания ресурса.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

После создания ресурса на портале Azure отобразится оповещение о завершении развертывания. 
   
![Снимок экрана, показывающий, что развертывание ресурса завершено.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Выберите **Перейти к ресурсу**. Теперь можно просмотреть свойства ресурса. 
   
Скопируйте значение **идентификатора учетной записи** ресурса в текстовый редактор для дальнейшего использования.

![Снимок экрана: область свойств ресурса](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Скопируйте также значение **домена учетной записи** ресурса в текстовый редактор для дальнейшего использования.

![Снимок экрана, показывающий значение домена учетной записи ресурса.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

В меню **Параметры** выберите **Ключ**. Скопируйте значение **первичного ключа** (**ключа учетной записи**) в текстовый редактор для дальнейшего использования.

![Снимок экрана: область "Ключи" для учетной записи.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
