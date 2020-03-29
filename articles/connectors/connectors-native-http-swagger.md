---
title: Подключение к конечным точкам REST из приложений логики Azure
description: Мониторинг конечных точек REST в автоматизированных задачах, процессах и рабочих процессах с помощью приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787375"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Вызов исчерпываетконечные точки REST с помощью приложений логики Azure

С [помощью приложений Azure Logic Apps](../logic-apps/logic-apps-overview.md) и встроенного разъема HTTP и Swagger можно автоматизировать рабочие процессы, которые регулярно вызывают любую конечную точку REST через файл [Swagger,](https://swagger.io) создавая логические приложения. Триггер и действия HTTP и Swagger работают так же, как [триггер и действия HTTP,](connectors-native-http.md) но обеспечивают лучший опыт в Logic App Designer, подвергая структуру API и выходы, описанные файлом Swagger. Чтобы реализовать триггер опроса, следуйте шаблону опроса, описанному в [пользовательских AI для вызова других AI, служб и систем из логических приложений.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* URL-адрес файла Swagger (не OpenAPI), описывающий конечную точку REST

  Как правило, конечная точка REST должна соответствовать этому критерию для работы разъема:

  * Файл Swagger должен быть размещен на URL HTTPS, который является общедоступным.

  * Файл Swagger должен иметь возможность [совместного использования ресурсов Cross-Origin (CORS).](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  Для ссылки на файл Swagger, который не размещен или не соответствует требованиям безопасности и кросс-происхождения, можно [загрузить файл Swagger в контейнер с blob в учетную запись хранения Azure](#host-swagger)и включить CORS в этой учетной записи хранения, чтобы можно было ссылаться на файл.

  Примеры в этой теме используют [API когнитивных услуг,](https://docs.microsoft.com/azure/cognitive-services/face/overview)который требует [учетной записи Когнитивных Служб и ключа доступа.](../cognitive-services/cognitive-services-apis-create-account.md)

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Если вы новичок в логических приложениях, просмотрите [Что такое приложения логики Azure?](../logic-apps/logic-apps-overview.md)

* Приложение логики, откуда вы хотите вызвать целевую конечную точку. Для начала с триггера HTTP и Swagger [создайте пустое приложение логики.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Чтобы использовать действие HTTP и Swagger, запустите логическое приложение с любым триггером, который вы хотите. В этом примере в качестве первого шага используется триггер HTTP и Swagger.

## <a name="add-an-http--swagger-trigger"></a>Добавить триггер HTTP и Swagger

Этот встроенный триггер отправляет запрос HTTP на URL-адрес файла Swagger, который описывает API REST и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портал Azure](https://portal.azure.com). Откройте свое пустое приложение логики в Logic App Designer.

1. На дизайнера, в поле поиска, введите "swagger", как ваш фильтр. Из списка **триггеров** выберите триггер **HTTP и Swagger.**

   ![Выберите триггер HTTP и Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. В поле **URL-адреса SWAGGER ENDPOINT** введите URL-адрес файла Swagger и выберите **следующий.**

   В этом примере используется URL-адрес Swagger, расположенный в западном регионе США для [API когнитивных услуг:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Когда дизайнер показывает операции, описанные файлом Swagger, выберите операцию, которую вы хотите использовать.

   ![Операции в файле Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Предоставьте значения для параметров триггера, которые варьируются в зависимости от выбранной операции, которые необходимо включить в конечный вызов. Настроили повторение частоты вызова триггера на конечную точку.

   Этот пример переименовывает триггер на "HTTP и Swagger триггер: Лицо - Обнаружить", так что шаг имеет более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте новый список **параметров** и выберите параметры, которые вы хотите.

   Для получения дополнительной информации о типах аутентификации, доступных для HTTP и Swagger, [см.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Продолжите создание рабочего процесса приложения логики с действиями, выполняемыми при срабатывании триггера.

1. Когда вы закончите, не забудьте сохранить свое приложение логики. На панели инструментов конструктора щелкните **Сохранить**.

## <a name="add-an-http--swagger-action"></a>Добавить действие HTTP и Swagger

Это встроенное действие делает запрос HTTP на URL-адрес файла Swagger, который описывает API REST и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портал Azure](https://portal.azure.com). Создание приложения логики в Конструкторе приложений логики.

1. Под шагом, где вы хотите добавить действие HTTP и Swagger, выберите **новый шаг.**

   Чтобы добавить действие между шагами, переместите указатель на стрелку между шагами. Выберите знак**+** плюс (), который появляется, а затем выберите **Добавить действие.**

1. На дизайнера, в поле поиска, введите "swagger", как ваш фильтр. Из списка **Действий** выберите действие **HTTP и Swagger.**

    ![Выбор действия "HTTP + Swagger"](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. В поле **URL-адреса SWAGGER ENDPOINT** введите URL-адрес файла Swagger и выберите **следующий.**

   В этом примере используется URL-адрес Swagger, расположенный в западном регионе США для [API когнитивных услуг:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Когда дизайнер показывает операции, описанные файлом Swagger, выберите операцию, которую вы хотите использовать.

   ![Операции в файле Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Укажите значения параметров действия, которые варьируются в зависимости от выбранной операции, которые необходимо включить в конечный вызов.

   Этот пример не имеет параметров, но переименовывает действие на "HTTP и Swagger действий: Лицо - Определить", так что шаг имеет более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте новый список **параметров** и выберите параметры, которые вы хотите.

   Для получения дополнительной информации о типах аутентификации, доступных для HTTP и Swagger, [см.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Когда вы закончите, не забудьте сохранить свое приложение логики. На панели инструментов конструктора щелкните **Сохранить**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Хозяин Сваггер в Хранилище Azure

Вы можете ссылаться на файл Swagger, который не размещен или не соответствует требованиям безопасности и кросс-происхождения, загрузив этот файл в контейнер с каплей в учетную запись хранения Azure и включив CORS в эту учетную запись хранения. Чтобы создать, настроить и хранить файлы Swagger в Azure Storage, выполните следующие действия:

1. [Создайте учетную запись хранения Azure.](../storage/common/storage-create-storage-account.md)

1. Теперь включите CORS для капли. В меню учетной записи хранилища выберите **CORS.** На вкладке **службы Blob** укажите эти значения, а затем выберите **Сохранить**.

   | Свойство | Значение |
   |----------|-------|
   | **Разрешенные источники** | `*` |
   | **Разрешенные методы** | `GET`, `HEAD`, `PUT` |
   | **Допустимые заголовки** | `*` |
   | **Предоставляемые заголовки** | `*` |
   | **Максимальный возраст** (в секундах) | `200` |
   |||

   Хотя в этом примере используется [портал Azure,](https://portal.azure.com)можно использовать такой инструмент, как [Azure Storage Explorer,](https://storageexplorer.com/)или автоматически настроить этот параметр с помощью этого [образца скрипта PowerShell.](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)

1. [Создайте контейнер с каплями.](../storage/blobs/storage-quickstart-blobs-portal.md) На панели **обзора** контейнера выберите **уровень доступа изменения.** Из **списка уровней общественного доступа** выберите **Blob (анонимный доступ читать только для капли)** и выберите **OK**.

1. [Загрузите файл Swagger в контейнер с каплей,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)либо через [портал Azure,](https://portal.azure.com) либо [через Azure Storage Explorer.](https://storageexplorer.com/)

1. Чтобы ссылаться на файл в контейнере blob, используйте ссылку HTTPS, которая следует за этим форматом, который является чувствительным к делу:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Справочник по соединителям

Вот более подробная информация о выходе из http и Swagger триггера или действий. Звонок HTTP и Swagger возвращает эту информацию:

| Имя свойства | Тип | Описание |
|---------------|------|-------------|
| Заголовки | объект | Заголовки из запроса |
| текст | объект | Объект JSON | Объект с содержанием тела из запроса |
| Код состояния | INT | Код состояния из запроса |
|||

| Код состояния | Описание |
|-------------|-------------|
| 200 | OK |
| 202 | Принято |
| 400 | Недопустимый запрос |
| 401 | Не авторизовано |
| 403 | Запрещено |
| 404 | Не найдено |
| 500 | Внутренняя ошибка сервера. Произошла неизвестная ошибка. |
|||

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).
