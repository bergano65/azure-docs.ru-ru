---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505330"
---
Чтобы получить URL-адрес SAS для обучающих данных пользовательской модели, перейдите к ресурсу хранилища на портале Azure и выберите вкладку **Обозреватель службы хранилища**. Перейдите к контейнеру, щелкните правой кнопкой мыши и выберите элемент **Получить подписанный URL-адрес**. Важно получить SAS для вашего контейнера, а не для самой учетной записи хранения. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте во временное расположение значение из раздела **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![замещающий текст](../media/quickstarts/get-sas-url.png)