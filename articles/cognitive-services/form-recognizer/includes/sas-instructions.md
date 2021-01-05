---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807813"
---
Чтобы получить URL-адрес SAS для обучающих данных пользовательской модели, перейдите к ресурсу хранилища на портале Azure и выберите вкладку **Обозреватель службы хранилища**. Перейдите к контейнеру, щелкните правой кнопкой мыши и выберите элемент **Получить подписанный URL-адрес**. Важно получить SAS для вашего контейнера, а не для самой учетной записи хранения. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте во временное расположение значение из раздела **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.