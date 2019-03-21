---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "58115961"
---
На [портале Azure](https://portal.azure.com) выберите **Группы ресурсов** > **cloud-shell-storage-\<ваш_регион>** > **\<имя_учетной_записи_хранения>**.

![Расположение учетной записи хранения Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

На странице **Обзор** для учетной записи хранения выберите **Файлы**.

Выберите автоматически созданный файловый ресурс и нажмите **Отправить**. Этот файловый ресурс подключен к Cloud Shell как `clouddrive`.

![Расположение кнопки "Отправить"](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Щелкните селектор файлов, выберите ZIP-файл и нажмите кнопку **Отправить**. 

В Cloud Shell используйте `ls`, чтобы убедиться, что загруженный ZIP-файл отображается в ресурсе по умолчанию `clouddrive`.

```azurecli-interactive
ls clouddrive
```
