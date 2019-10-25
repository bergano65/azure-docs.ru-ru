---
title: Подключение к конечным точкам RESTFUL из Azure Logic Apps
description: Мониторинг конечных точек RESTFUL в автоматизированных задачах, процессах и рабочих процессах с помощью Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 663ef16511269dd61a6567d6570f3445b7da6447
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804240"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Вызов конечных точек RESTFUL с помощью Azure Logic Apps

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и встроенного соединителя HTTP + Swagger можно автоматизировать рабочие процессы, которые регулярно вызывают любую конечную точку RESTful через [файл Swagger](https://swagger.io) , создав логику приложений. Триггер и действие HTTP + Swagger работают так же, как [триггер и действие HTTP](connectors-native-http.md) , но предоставляют более высокую производительность в конструкторе приложений логики, предоставляя структуру API и выходные данные, описанные в файле Swagger. Для реализации триггера опроса следуйте шаблону опроса, описанному в статье [Создание настраиваемых API для вызова других API-интерфейсов, служб и систем из приложений логики](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* URL-адрес файла Swagger, который описывает целевую конечную точку RESTFUL

  Как правило, конечная точка RESTFUL должна соответствовать этим критериям, чтобы соединитель работал:

  * Файл Swagger должен размещаться на общедоступном URL-адресе HTTPS.

  * В файле Swagger должен быть включен [общий доступ к ресурсам между источниками (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Для ссылки на файл Swagger, который не размещается или не соответствует требованиям безопасности и перекрестного происхождения, можно [передать файл Swagger в контейнер больших двоичных объектов в учетной записи хранения Azure](#host-swagger)и включить CORS в этой учетной записи хранения, чтобы можно было ссылаться на файл.

  В примерах этого раздела используется [API распознавания лиц Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/face/overview), для которого требуется [учетная запись Cognitive Services и ключ доступа](../cognitive-services/cognitive-services-apis-create-account.md).

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Приложение логики, из которого нужно вызвать целевую конечную точку. Чтобы начать с триггера HTTP + Swagger, [создайте пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Чтобы использовать действие "HTTP + Swagger", запустите приложение логики с любым триггером. В этом примере в качестве первого шага используется триггер HTTP + Swagger.

## <a name="add-an-http--swagger-trigger"></a>Добавление триггера HTTP + Swagger

Этот встроенный триггер отправляет запрос HTTP в URL-адрес файла Swagger, который описывает REST API и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портале Azure](https://portal.azure.com). Откройте пустое приложение логики в конструкторе приложений логики.

1. В конструкторе в поле поиска введите "Swagger" в качестве фильтра. В списке **триггеры** Выберите триггер **http + Swagger** .

   ![Выбор триггера HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. В поле **URL-адрес КОНЕЧНОЙ точки SWAGGER** введите URL-адрес файла SWAGGER и нажмите кнопку **Далее**.

   В этом примере используется URL-адрес Swagger, расположенный в регионе "Западная часть США" для [Cognitive Services API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Когда в конструкторе отображаются операции, описанные в файле Swagger, выберите операцию, которую необходимо использовать.

   ![Операции в файле Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Укажите значения параметров триггера, которые зависят от выбранной операции, которую необходимо включить в вызов конечной точки. Настройте периодичность, с которой триггер должен вызывать конечную точку.

   В этом примере триггер переименовывается в "HTTP + Swagger Trigger: распознавания лиц", чтобы у шага было более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте список **Добавить новый параметр** и выберите нужные параметры.

   Дополнительные сведения о типах проверки подлинности, доступных для HTTP + Swagger, см. [в разделе Добавление проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Продолжите создание рабочего процесса приложения логики с действиями, выполняемыми при срабатывании триггера.

1. Закончив, не забудьте сохранить приложение логики. На панели инструментов конструктора щелкните **Сохранить**.

## <a name="add-an-http--swagger-action"></a>Добавление действия HTTP + Swagger

Это встроенное действие выполняет запрос HTTP к URL-адресу файла Swagger, который описывает REST API и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портале Azure](https://portal.azure.com). Создание приложения логики в Конструкторе приложений логики.

1. На шаге, куда нужно добавить действие HTTP + Swagger, выберите **новый шаг**.

   Чтобы добавить действие между шагами, переместите указатель на стрелку между шагами. Выберите отображаемый знак «плюс» ( **+** ), а затем щелкните **Добавить действие**.

1. В конструкторе в поле поиска введите "Swagger" в качестве фильтра. В списке **действия** выберите действие **http + Swagger** .

    ![Выбор действия "HTTP + Swagger"](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. В поле **URL-адрес КОНЕЧНОЙ точки SWAGGER** введите URL-адрес файла SWAGGER и нажмите кнопку **Далее**.

   В этом примере используется URL-адрес Swagger, расположенный в регионе "Западная часть США" для [Cognitive Services API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Когда в конструкторе отображаются операции, описанные в файле Swagger, выберите операцию, которую необходимо использовать.

   ![Операции в файле Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Укажите значения для параметров действия, которые зависят от выбранной операции, которую необходимо включить в вызов конечной точки.

   Этот пример не имеет параметров, но переименовывает действие в "HTTP + Swagger Action: Face-Identify", чтобы у шага было более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте список **Добавить новый параметр** и выберите нужные параметры.

   Дополнительные сведения о типах проверки подлинности, доступных для HTTP + Swagger, см. [в разделе Добавление проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Закончив, не забудьте сохранить приложение логики. На панели инструментов конструктора щелкните **Сохранить**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Размещение Swagger в службе хранилища Azure

Вы можете ссылаться на файл Swagger, который не размещен или не соответствует требованиям безопасности и перекрестному источнику, загружая этот файл в контейнер больших двоичных объектов в учетной записи хранения Azure и включив CORS для этой учетной записи хранения. Чтобы создать, настроить и сохранить файлы Swagger в службе хранилища Azure, выполните следующие действия.

1. [Создайте учетную запись хранения Azure](../storage/common/storage-create-storage-account.md).

1. Теперь включите CORS для большого двоичного объекта. В меню учетной записи хранения выберите **CORS**. На вкладке **Служба BLOB-объектов** укажите эти значения и нажмите кнопку **сохранить**.

   | Свойство | Value |
   |----------|-------|
   | **Разрешенные источники** | `*` |
   | **Допустимые методы** | `GET`, `HEAD`, `PUT` |
   | **Разрешенные заголовки** | `*` |
   | **Представляемые заголовки** | `*` |
   | **Максимальный возраст** (в секундах) | `200` |
   |||

   Хотя в этом примере используется [портал Azure](https://portal.azure.com), можно использовать такое средство, как [Обозреватель службы хранилища Azure](https://storageexplorer.com/), или автоматически настроить этот параметр с помощью этого примера [скрипта PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Создайте контейнер больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md). На панели **Обзор** контейнера выберите **изменить уровень доступа**. В списке **общедоступный уровень доступа** выберите **BLOB-объект (анонимный доступ на чтение только для больших двоичных объектов)** и нажмите кнопку **ОК**.

1. [Передайте файл Swagger в контейнер больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)с помощью [портал Azure](https://portal.azure.com) или [Обозреватель службы хранилища Azure](https://storageexplorer.com/).

1. Чтобы сослаться на файл в контейнере больших двоичных объектов, используйте HTTPS-ссылку, которая соответствует этому формату с учетом регистра:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Справочник по соединителям

Ниже приведены дополнительные сведения о выходных данных триггера HTTP + Swagger или действия. Вызов HTTP + Swagger возвращает следующие сведения:

| Имя свойства | Тип | Описание |
|---------------|------|-------------|
| Заголовки | object | Заголовки из запроса |
| текст | object | Объект JSON | Объект с содержимым текста из запроса |
| Код состояния | int | Код состояния из запроса |
|||

| Код состояния | Описание |
|-------------|-------------|
| 200 | ОК |
| 202 | Принято |
| 400 | Недопустимый запрос |
| 401 | Не авторизовано |
| 403 | Запрещено |
| 404 | Не найдено |
| 500 | Внутренняя ошибка сервера. Произошла неизвестная ошибка. |
|||

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).