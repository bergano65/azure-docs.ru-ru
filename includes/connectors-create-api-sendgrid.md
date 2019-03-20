---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b06c57f50f759677770839ae06cb05e4de36f84c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "58115415"
---
### <a name="prerequisites"></a>Технические условия
* Учетная запись [SendGrid](https://www.SendGrid.com/). 

Прежде чем использовать учетную запись SendGrid в приложении логики, необходимо авторизовать приложение логики для подключения к вашей учетной записи SendGrid. К счастью, это можно легко сделать из приложения логики на портале Azure. 

Ниже приведены указания по авторизации приложения логики для подключения к учетной записи SendGrid.

1. Чтобы создать подключение к SendGrid, в конструкторе приложений логики в раскрывающемся списке выберите параметр **Показать API, управляемые Майкрософт**, а затем введите в поле поиска *SendGrid*. Выберите триггер или действие, которые хотите использовать:   
   ![SendGrid — шаг 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Если вы ранее не создавали подключения к SendGrid, вам будет предложено ввести учетные данные SendGrid. Эти учетные данные будут использоваться для авторизации приложения логики, чтобы оно могло подключиться и получить доступ к данным вашей учетной записи SendGrid.  
   ![SendGrid — шаг 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Подключение создано, и теперь вы можете перейти к другим действиям в приложении логики:   
   ![SendGrid — шаг 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

