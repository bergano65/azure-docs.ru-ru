---
title: Подключитесь к Dropbox — Azure Logic Apps
description: Передавайте файлы и управляйте ими с помощью интерфейсов REST API Dropbox и Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314422"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Отправка файлов и управления ими в Dropbox с помощью Azure Logic Apps

Соединитель Dropbox и Azure Logic Apps можно создать автоматизированные рабочие процессы, отправка файлов и управления ими в вашей учетной записи Dropbox. 

В этой статье показано, как подключиться к Dropbox из приложения логики, а затем добавьте Dropbox **при создании файла** триггера и Dropbox **получить содержимое файла по пути** действие.

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

* Объект [учетной записи Dropbox](https://www.dropbox.com/), который вы можете зарегистрироваться бесплатно. Учетные данные учетной записи необходимы для создания подключения между приложением логики и учетной записи Dropbox.

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Для этого примера требуется пустое приложение логики.

## <a name="add-trigger"></a>Добавление триггера

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Под полем поиска выберите **Все**. В поле поиска введите слово dropbox в качестве фильтра.
В списке триггеров выберите триггер: **При создании файла**

   ![Выбор триггера Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Войдите с учетными данными учетной записи Dropbox и авторизуйте доступ к данным Dropbox для Azure Logic Apps.

1. Укажите необходимые сведения для триггера. 

   В этом примере выберите папку для отслеживания создания файла. Чтобы перейти к папкам, щелкните значок папки рядом с полем **папку** поле.

## <a name="add-action"></a>Добавление действия

Теперь добавьте действие, которое получает содержимое из любого нового файла.

1. В разделе триггера выберите **Следующий шаг**. 

1. Под полем поиска выберите **Все**. В поле поиска введите слово dropbox в качестве фильтра.
В списке действий выберите действие **Получение содержимого файла с помощью пути**

1. Если вы еще не уже имеет право Azure Logic Apps для доступа к Dropbox, теперь авторизации доступа.

1. Чтобы найти путь к файлу, вы хотите использовать, рядом с полем **путь к файлу** , нажмите кнопку с многоточием (**...** ) кнопку. 

   Можно также щелкнуть внутри **путь к файлу** из списка динамического содержимого выберите **путь к файлу**, значение которого доступен в виде выходных данных из триггера, добавленный в предыдущем разделе.

1. Сохраните приложение логики, когда закончите.

1. Чтобы вызвать приложение логики, создайте новый файл в Dropbox.

## <a name="connector-reference"></a>Справочник по соединителям

Технические сведения, такие как триггеры, действия и ограничения, как определено соединителя OpenAPI (прежнее название — Swagger) файл, см. в разделе [соединителя справочной странице](/connectors/dropbox/).

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).
