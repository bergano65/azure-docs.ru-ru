---
title: Подключение к конечным точкам REST из приложений логики Azure
description: Мониторинг конечных точек REST в автоматизированных задач, процессы и рабочие процессы с помощью Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541572"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Вызов конечных точек REST с помощью Azure Logic Apps

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и встроенные HTTP + Swagger соединителя, вы можете автоматизировать рабочие процессы, которые регулярно вызова любой конечной точки REST через [файл Swagger](https://swagger.io) с помощью приложений логики. HTTP + Swagger триггера и действие работать так же, как [триггер HTTP и действие](connectors-native-http.md) но повышения удобства работы в конструкторе приложений логики, предоставляя структура API и выходные данные, описанная в файле Swagger. Чтобы реализовать опрашивающий триггер, используйте шаблон опроса, описанный в [Создание пользовательских API для вызова других API-интерфейсы, службам и системам из приложений логики](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* URL-адрес файла Swagger, который описывает целевая конечная точка REST

  Как правило конечная точка REST соответствует этому критерию, для работы соединителя:

  * Файл Swagger должна быть размещена на URL-адрес HTTPS, которая является общедоступной.

  * Файл Swagger должен иметь [совместное использование ресурсов независимо от источника (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) включена.

  Для ссылки на файл Swagger, который не размещен или, не соответствующий требованиям независимо от источника к безопасности и, вы можете [передайте файл Swagger в контейнер больших двоичных объектов в учетную запись хранения](#host-swagger)и поэтому включить CORS для этой учетной записи хранения что можно ссылаться на файл.

  В примерах в этом разделе используется [API распознавания лиц Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/face/overview), которая требует [Cognitive Services учетной записи и ключ доступа](../cognitive-services/cognitive-services-apis-create-account.md).

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Приложение логики, из которой необходимо вызвать целевой конечной точки. Для начала активации HTTP + Swagger, [создать пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Чтобы использовать HTTP + Swagger действие запуска приложения логики с любой триггер, который вы хотите. Этот пример использует HTTP + Swagger триггер на первом этапе.

## <a name="add-an-http--swagger-trigger"></a>Добавить HTTP + Swagger триггера

Этот триггер встроенные отправляет HTTP-запрос на URL-адрес для файла Swagger, который описывает REST API и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портале Azure](https://portal.azure.com). Откройте ваш пустое приложение логики в конструкторе приложений логики.

1. В конструкторе, в поле поиска введите «swagger» в качестве фильтра. Из **триггеры** выберите **HTTP + Swagger** триггера.

   ![Выберите HTTP + Swagger триггера](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. В **URL-адрес конечной ТОЧКИ SWAGGER** введите URL-адрес для файла Swagger и выберите **Далее**.

   В этом примере используется URL-адрес Swagger, который находится в регионе "Западная часть США" для [API распознавания лиц Cognitive Services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Если конструктор отображает операции, описанные в файле Swagger, выберите операцию, которая будет использоваться.

   ![Операции в файл Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Укажите значения для триггера параметры, которые различаются в зависимости от выбранной операции, который требуется включить в вызове конечной точки. Настройте для интервала триггера повторения для вызова конечной точки.

   В этом примере переименовывает триггера «HTTP + Swagger триггера: Лиц — обнаружение», чтобы этот шаг имеет более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте **добавьте параметр** и выберите нужные параметры.

   Дополнительные сведения о доступных типах проверки подлинности для HTTP + Swagger, см. в разделе [проверки подлинности HTTP триггеры и действия](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Продолжите создание рабочего процесса приложения логики с действиями, выполняемыми при срабатывании триггера.

1. Когда закончите, сохраните приложение логики. На панели инструментов конструктора выберите **Сохранить**.

## <a name="add-an-http--swagger-action"></a>Добавить HTTP + Swagger действие

Это встроенные действие отправляет запрос HTTP на URL-адрес для файла Swagger, который описывает REST API и возвращает ответ, содержащий содержимое этого файла.

1. Войдите на [портале Azure](https://portal.azure.com). Создание приложения логики в Конструкторе приложений логики.

1. В шаге, где вы хотите добавить HTTP + Swagger действие, выберите **новый шаг**.

   Чтобы добавить действие между шагами, переместите указатель на стрелку между шагами. Выберите знак «плюс» ( **+** ), появится, а затем выберите **добавить действие**.

1. В конструкторе, в поле поиска введите «swagger» в качестве фильтра. Из **действия** выберите **HTTP + Swagger** действие.

    ![Выбор действия "HTTP + Swagger"](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. В **URL-адрес конечной ТОЧКИ SWAGGER** введите URL-адрес для файла Swagger и выберите **Далее**.

   В этом примере используется URL-адрес Swagger, который находится в регионе "Западная часть США" для [API распознавания лиц Cognitive Services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Введите URL-адрес для конечной точки Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Если конструктор отображает операции, описанные в файле Swagger, выберите операцию, которая будет использоваться.

   ![Операции в файл Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Укажите значения для параметров действия, которые зависят зависимости от выбранной операции, который требуется включить в вызове конечной точки.

   В этом примере не имеет параметров, но переименовывает действие «HTTP + Swagger действия: Сталкиваются - идентификация», чтобы этот шаг имеет более описательное имя.

   ![Сведения об операции](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Чтобы добавить другие доступные параметры, откройте **добавьте параметр** и выберите нужные параметры.

   Дополнительные сведения о доступных типах проверки подлинности для HTTP + Swagger, см. в разделе [проверки подлинности HTTP триггеры и действия](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Когда закончите, сохраните приложение логики. На панели инструментов конструктора выберите **Сохранить**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Размещение Swagger в службе хранилища Azure

Можно ссылаться на файл Swagger, который не размещен или, не соответствующий требованиям независимо от источника к безопасности и отправить его в контейнер больших двоичных объектов в учетной записи хранения Azure и включение CORS для этой учетной записи хранения. Для создания, настройки и хранения файлов Swagger в службе хранилища Azure, выполните следующие действия.

1. [Создайте учетную запись хранения Azure](../storage/common/storage-create-storage-account.md).

1. Теперь можно включите CORS для большого двоичного объекта. В меню учетной записи хранения выберите **CORS**. На **служба BLOB-объектов** вкладке, указывать эти значения, а затем выберите **Сохранить**.

   | Свойство | Значение |
   |----------|-------|
   | **Разрешенные источники** | `*` |
   | **Разрешенные методы** | `GET`, `HEAD`, `PUT` |
   | **Допустимые заголовки** | `*` |
   | **Доступные заголовки** | `*` |
   | **Максимальный возраст** (в секундах) | `200` |
   |||

   Несмотря на то, что в этом примере используется [портала Azure](https://portal.azure.com), можно использовать это средство, например [обозреватель службы хранилища Azure](https://storageexplorer.com/), или автоматической настройки данного параметра с помощью этого примера [сценарий PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Создайте контейнер больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md). В контейнере **Обзор** области выберите **изменение уровня доступа**. Из **уровня общего доступа** выберите **большой двоичный объект (анонимный доступ на чтение для больших двоичных объектов только)** и выберите **ОК**.

1. [Передайте файл Swagger в контейнер больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), либо через [портала Azure](https://portal.azure.com) или [обозреватель службы хранилища Azure](https://storageexplorer.com/).

1. Чтобы ссылаться на файл в контейнер больших двоичных объектов, используйте HTTPS-канала, который имеет следующий формат, учета регистра:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Справочник по соединителям

Здесь находятся дополнительные сведения о выходных данных из HTTP + Swagger триггера или действия. HTTP + Swagger вызов возвращает эту информацию:

| Имя свойства | type | Описание |
|---------------|------|-------------|
| header | object | Заголовки из запроса |
| body | object | Объект JSON | Объект с содержимым текста из запроса |
| status code | int | Код состояния из запроса |
|||

| status code | Описание |
|-------------|-------------|
| 200 | OК |
| 202 | Принято |
| 400 | Ошибочный запрос |
| 401 | Не авторизовано |
| 403 | Запрещено |
| 404 | Не найдено |
| 500 | Внутренняя ошибка сервера. Произошла неизвестная ошибка. |
|||

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).