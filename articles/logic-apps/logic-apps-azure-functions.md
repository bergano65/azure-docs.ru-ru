---
title: Добавление и вызов функций Azure из Azure Logic Apps
description: Вызывайте и запустите пользовательский код в функциях Azure из автоматизированных задач и рабочих процессов в Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 2525ca681d805a3b6f086335531a4beaeb9c4e51
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453470"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Вызов функций Azure из Azure Logic Apps

Если требуется выполнить код, выполняющий определенное задание в приложениях логики, можно создать собственную функцию с помощью [функций Azure](../azure-functions/functions-overview.md). Эта служба помогает создавать функции node. js, C#и F# , чтобы не создавать полноценное приложение или инфраструктуру для выполнения кода. Также можно [вызывать приложения логики из функций](#call-logic-app). Служба "Функции Azure" обеспечивает бессерверные вычисления в облаке и является полезной при выполнении задач, приведенных ниже.

* Расширение режимов работы приложений логики с помощью функций в Node.js или C#.
* Выполнение вычислений в рабочем процессе приложения логики.
* Применение расширенных функции форматирования или вычисления полей в приложениях логики.

Чтобы выполнить фрагменты кода без создания функций Azure, Узнайте, как [Добавить и запустить встроенный код](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Интеграция функций Logic Apps и Azure в настоящее время не работает с включенными слотами.

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Приложение-функция Azure, представляющее собой контейнер для функций Azure, а также функцию Azure. При отсутствии приложения-функции [создайте его](../azure-functions/functions-create-first-azure-function.md). Затем можно создать функцию вне приложения логики в портал Azure или [внутри приложения логики](#create-function-designer) в конструкторе приложений логики.

* При работе с приложениями логики те же требования применяются к приложениям функций и функциям независимо от того, являются ли они существующими или новыми:

  * Приложение функции и приложение логики должны использовать одну и ту же подписку Azure.

  * Новые приложения функции должны использовать .NET или JavaScript в качестве стека среды выполнения. При добавлении новой функции в существующие приложения-функции можно выбрать любой из C# них или JavaScript.

  * Функция использует шаблон **триггера HTTP** .

    Этот шаблон триггера HTTP поддерживает содержимое приложения логики с типом `application/json`. При добавлении функции Azure в приложение логики в конструкторе приложений логики отображаются пользовательские функции, созданные на основе этого шаблона в подписке Azure.

  * Функция не использует настраиваемые маршруты, если не определено [Определение OpenAPI](../azure-functions/functions-openapi-definition.md) (прежнее название — [файл Swagger](https://swagger.io/)).

  * При наличии определения OpenAPI для функции конструктор Logic Apps предоставляет более широкие возможности при работе с параметрами функций. Прежде чем приложение логики сможет найти и получить доступ к функции с определениями OpenAPI, необходимо [настроить приложение-функцию, выполнив следующие действия](#function-swagger).

* Приложение логики, в которое необходимо добавить функцию, включая [триггер](../logic-apps/logic-apps-overview.md#logic-app-concepts) в качестве первого шага.

  Перед добавлением действий, запускающих функции, приложение логики должно начинаться с триггера. Если вы не работали с приложениями логики, см. руководства по [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и [созданию первого приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Поиск функций, имеющих описания OpenAPI

Чтобы расширить возможности при работе с параметрами функций в конструкторе Logic Apps, [Создайте определение OpenAPI](../azure-functions/functions-openapi-definition.md), которое ранее называлось [файлом Swagger](https://swagger.io/)для вашей функции. Чтобы настроить приложение-функцию, которое позволит приложению логики находить и использовать функции, которые обладают описанием Swagger, выполните следующие действия:

1. Убедитесь, что приложение-функция активно работает.

1. В приложении-функции настройте [общий доступ к ресурсам между источниками (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) , чтобы разрешить все источники, выполнив следующие действия.

   1. В списке **приложения функции** выберите приложение функции. На правой панели выберите **функции платформы** > **CORS**.

      ![Выберите приложение-функция > "Функции платформы" > CORS](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. В разделе **CORS**добавьте подстановочный знак звездочки ( **`*`** ), но удалите все остальные источники из списка и нажмите кнопку **сохранить**.

      ![Добавление подстановочного знака "*" в разделе CORS*](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Доступ к значениям свойств внутри HTTP-запросов

Функции веб-перехватчика могут принимать HTTP-запросы в качестве входных и передавать эти запросы другим функциям. Например, несмотря на то, что служба Logic Apps имеет [функции преобразования значений DateTime](../logic-apps/workflow-definition-language-functions-reference.md), в этом базовом примере функции JavaScript отображается доступ к свойству внутри запрашиваемого объекта, который передается функции и выполняет операции над значением свойства. Чтобы получить доступ к свойствам, которые находятся внутри объекта, в этом примере используется [оператор точка (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors).

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Ниже приведены процессы, которые выполняются внутри функции.

1. Функция создает переменную `data` и присваивает ей объект `body`, который находится внутри объекта `request`. Чтобы ссылаться на объект `body` внутри объекта `request`, функция использует оператор точку (.).

   ```javascript
   var data = request.body;
   ```

1. Теперь функция может получить доступ к свойству `date` через переменную `data` и изменить значение свойства с типа DateTime на тип DateString с помощью вызова функции `ToDateString()`. Также в ответе функции она возвращает результат через свойство `body`.

   ```javascript
   body: data.date.ToDateString();
   ```

После создания функции Azure можно выполнить шаги из раздела [Добавление существующей функций в приложения логики](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Создание встроенных функций приложений логики

Перед созданием функции Azure, запускаемой из приложения логики с помощью конструктора приложений логики, необходимо сначала создать приложение-функцию Azure, которое является контейнером для ваших функций. При отсутствии приложения-функции создайте его. См. статью [Создание первой функции на портале Azure](../azure-functions/functions-create-first-azure-function.md).

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com).

1. Чтобы создать и добавить функцию, выполните шаг, который применим к вашему сценарию.

   * На последнем шаге рабочего процесса приложения логики выберите **новый шаг**.

   * Между существующими шагами в рабочем процессе приложения логики наведите указатель мыши на стрелку, щелкните знак плюса (+), а затем выберите **Добавить действие**.

1. В поле поиска введите "Функции Azure" в качестве условия фильтра. В списке действия выберите действие " **Выбор функции Azure** ", например:

   ![Поиск "Функции Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Выберите приложение-функцию из списка приложений-функций. После открытия списка действий выберите действие **создать новую функцию** .

   ![Выбор приложения-функции](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Определите функцию в редакторе определения функции.

   1. Введите имя функции в поле **Имя функции**.

   1. В поле **код** добавьте код в шаблон функции, включая ответ и полезные данные, которые необходимо вернуть в приложение логики после завершения выполнения функции. Когда все будет готово, выберите **Создать**.

   Пример.

   ![Определение функции](./media/logic-apps-azure-functions/add-code-function-definition.png)

   В коде шаблона *объект `context`* относится к сообщению, которое приложение логики отправляет через поле **Текст запроса** на более позднем этапе. Чтобы получить доступ к свойствам объекта `context` изнутри вашей функции, используйте этот синтаксис:

   `context.body.<property-name>`

   Например, чтобы указать ссылку на свойство `content` внутри объекта `context`, используйте этот синтаксис:

   `context.body.content`

   Код шаблона также включает переменную `input`, которая сохраняет значение из параметра `data`, чтобы ваша функция могла выполнять с ним операции. Внутри функций JavaScript переменная `data` также является ярлыком для `context.body`.

   > [!NOTE]
   > Свойство `body` здесь применяется к объекту `context` и не совпадает с токеном **Текст** из выходных данных действия, который вы также можете передать своей функции.

1. В поле **Текст запроса** укажите входные данные функции, которые должны быть отформатированы как объект JSON (нотация объектов JavaScript).

   Эти входные данные представляют собой *объект контекста* или сообщение, которое приложение логики отправляет в функцию. Если щелкнуть поле **Текст запроса**, откроется список динамического содержимого, что позволит выбрать токены для выходных данных с предыдущих шагов. В этом примере указывается, что полезные данные контекста содержат свойство с именем `content`, имеющее значение **из** токена по электронной почте.

   ![Пример "Текста запроса": полезные данные объекта контекста](./media/logic-apps-azure-functions/function-request-body-example.png)

   Здесь объект контекста не приводится как строка, что позволяет добавить содержимое объекта напрямую в полезные данные JSON. Тем не менее, если объект контекста не является токеном JSON, который передает строку, либо объектом или массивом JSON, появится сообщение об ошибке. Поэтому, если в этом примере использовался **полученный маркер времени** , можно привести объект контекста в виде строки, добавив двойные кавычки.

   ![Приведение объекта контекста к состоянию строки](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Чтобы указать другие сведения, например метод для использования, заголовки запроса или параметры запроса или проверку подлинности, откройте список **Добавить новый параметр** и выберите нужные параметры. Для проверки подлинности параметры различаются в зависимости от выбранной функции. См. раздел [Включение проверки подлинности для функций Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Добавление существующих функций в приложение логики

Чтобы вызвать существующие функции Azure из приложений логики, можно добавить функции Azure, например, любые другие действия в конструкторе приложений логики.

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com).

1. На шаге, где нужно добавить функцию, выберите **новый шаг**.

1. В разделе **Выбор действия**в поле поиска введите "функции Azure" в качестве фильтра. В списке действия выберите действие **Выбор функции Azure** .

   ![Поиск "Функции Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Выберите приложение-функцию из списка приложений-функций. После появления функции выберите ее.

   ![Выберите приложение-функцию и функцию Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Для функций, имеющих определения API (описания Swagger) и [настроенных таким образом, чтобы приложение логики мог находить и получать доступ к этим функциям](#function-swagger), можно выбрать **действия Swagger**.

   ![Выберите приложение функции, "действия Swagger" и функцию Azure.](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. В поле **Текст запроса** укажите входные данные функции, которые должны быть отформатированы как объект JSON (нотация объектов JavaScript).

   Эти входные данные представляют собой *объект контекста* или сообщение, которое приложение логики отправляет в функцию. Если щелкнуть в поле **текст запроса** , появится список динамического содержимого, в котором можно выбрать маркеры для выходных данных из предыдущих шагов. В этом примере указывается, что полезные данные контекста содержат свойство с именем `content`, имеющее значение **из** токена по электронной почте.

   ![Пример "Текста запроса": полезные данные объекта контекста](./media/logic-apps-azure-functions/function-request-body-example.png)

   Здесь объект контекста не приводится как строка, что позволяет добавить содержимое объекта напрямую в полезные данные JSON. Тем не менее, если объект контекста не является токеном JSON, который передает строку, либо объектом или массивом JSON, появится сообщение об ошибке. Таким образом, если в этом примере использовался токен **Время получения**, вы можете привести объект контекста к состоянию строки, добавив двойные кавычки:

   ![Приведение объекта контекста к состоянию строки](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Чтобы указать другие сведения, такие как используемый метод, заголовков запроса, параметров запроса или проверки подлинности, откройте список **Добавить новый параметр** и выберите нужные параметры. Для проверки подлинности параметры различаются в зависимости от выбранной функции. См. раздел [Включение проверки подлинности в функциях Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Вызов приложений логики из функций Azure

Если вы хотите вызвать приложение логики изнутри функции Azure, оно должно запускаться триггером, который предоставляет вызываемую конечную точку. Например, вы можете запустить приложение логики с помощью триггера **HTTP**, **Запрос**, **Очереди Azure** или **Сетка событий**. Внутри функции отправьте HTTP-запрос POST на URL-адрес триггера и включите полезные данные, необходимые для обработки приложения логики. Дополнительные сведения см. в разделе [Вызов, активация и вложение приложений логики](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Включение проверки подлинности для функций Azure

Чтобы проверить подлинность доступа к ресурсам в других клиентах Azure Active Directory (Azure AD) без входа в систему и предоставления учетных данных или секретов, приложение логики может использовать [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md) (прежнее название — управляемое удостоверение службы или MSI). Azure управляет этим удостоверением за вас и помогает защитить учетные данные, потому что вам не нужно предоставлять или сменять секреты. Узнайте больше о [службах Azure, поддерживающих управляемые удостоверения для аутентификации Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Если настроить приложение логики для использования управляемого удостоверения, назначенного системой, функции Azure в приложении логики также могут использовать такое же удостоверение для проверки подлинности. Дополнительные сведения о поддержке проверки подлинности для функций Azure в Logic Apps см. в разделе [Добавление проверки подлинности для исходящих вызовов](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Чтобы настроить и использовать назначенное системой удостоверение для функции, выполните следующие действия.

1. Включите назначенное системой удостоверение в приложении логики и настройте доступ этого удостоверения к целевому ресурсу. См. статью [Проверка подлинности доступа к ресурсам Azure с помощью управляемых удостоверений в Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Включите проверку подлинности в функции Azure и приложении функции, выполнив следующие действия.

   * [Настройка анонимной проверки подлинности в функции](#set-authentication-function-app)
   * [Настройка проверки подлинности Azure AD в приложении функции](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Настройка анонимной проверки подлинности в функции

Чтобы использовать в функции Azure удостоверение, назначенное системой приложения логики, установите для функции уровень проверки подлинности значение Anonymous. В противном случае приложение логики выдает ошибку "BadRequest".

1. В [портал Azure](https://portal.azure.com)найдите и выберите свое приложение функции. В этих шагах в качестве примера приложения функции используется "Фабрикамфунктионапп".

1. В области приложение функции выберите **функции платформы**. В разделе **средства разработки**выберите **Дополнительные инструменты (KUDU)** .

   ![Открыть дополнительные инструменты для KUDU](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. В заголовке веб-сайта KUDU в меню **консоль отладки** выберите **cmd**.

   ![В меню консоли отладки выберите параметр "CMD".](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. После появления следующей страницы в списке Папка выберите **site** > **wwwroot** > *функции*. В этих шагах используется "Фабрикамазурефунктион" в качестве примера функции.

   ![Выберите "site" > "wwwroot" > функции](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Откройте файл `function.json` для редактирования.

   ![Щелкните "Изменить" для файла "Function. JSON"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. В объекте `bindings` проверьте, существует ли свойство `authLevel`. Если свойство существует, присвойте свойству значение `anonymous`. В противном случае добавьте это свойство и задайте значение.

   ![Добавьте свойство "authLevel" и установите для него значение "Anonymous".](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Когда все будет готово, сохраните параметры и перейдите к следующему разделу.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Настройка аутентификации Azure AD для приложения функции

Прежде чем начать эту задачу, найдите и вставьте эти значения для последующего использования:

* Идентификатор объекта, который создается для назначенного системой удостоверения, представляющего ваше приложение логики.

  * Чтобы создать этот идентификатор объекта, [включите назначенное системой удостоверение приложения логики](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * В противном случае, чтобы найти этот идентификатор объекта, откройте приложение логики в конструкторе приложений логики. В меню приложения логики в разделе **Параметры**выберите **удостоверение** > **системой назначено**.

* Идентификатор каталога для вашего клиента в Azure Active Directory (Azure AD)

  Чтобы получить идентификатор каталога клиента, можно выполнить команду [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) PowerShell. Или в портал Azure выполните следующие действия.

  1. В [портал Azure](https://portal.azure.com)найдите и выберите свое приложение функции.

  1. Найдите и выберите свой клиент Azure AD. В качестве примера клиента в этих шагах используется Fabrikam.

  1. В меню клиента в разделе **Управление**выберите **свойства**.

  1. Скопируйте идентификатор каталога клиента, например, и сохраните этот идентификатор для последующего использования.

     ![Поиск и копирование идентификатора каталога клиента Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Идентификатор ресурса для целевого ресурса, к которому требуется получить доступ

  * Чтобы найти эти идентификаторы ресурсов, ознакомьтесь со [службами Azure, которые поддерживают Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Этот идентификатор ресурса должен точно соответствовать значению, которое требуется Azure AD, включая все обязательные символы косой черты.

  Этот идентификатор ресурса также аналогичен значению, который впоследствии используется в свойстве **аудитории** при [настройке действия функции для использования назначенного системой удостоверения](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Теперь вы можете настроить аутентификацию Azure AD для приложения функции.

1. В [портал Azure](https://portal.azure.com)найдите и выберите свое приложение функции.

1. В области приложение функции выберите **функции платформы**. В разделе **Сетевые подключения**выберите **Проверка подлинности и авторизация**.

   ![Просмотр параметров проверки подлинности и авторизации](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Измените значение параметра **Проверка подлинности службы приложений** **на вкл**. В списке **действие, выполняемое, если запрос не прошел проверку подлинности** выберите **вход с помощью Azure Active Directory**. В разделе **Поставщики проверки подлинности** выберите **Azure Active Directory**.

   ![Включение проверки подлинности в Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. В области **параметры Azure Active Directory** выполните следующие действия.

   1. Установите для **режима управления** значение **Advanced**.

   1. В свойстве **идентификатор клиента** введите идентификатор объекта для назначенного системой удостоверения приложения логики.

   1. В свойстве **URL-адрес издателя** введите URL-адрес `https://sts.windows.net/` и добавьте идентификатор каталога клиента Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. В свойстве **Разрешенные аудитории токена** введите идентификатор ресурса для целевого ресурса, к которому требуется получить доступ.

      Этот идентификатор ресурса — это то же значение, которое вы позже используете в свойстве **аудитории** при [настройке действия функции для использования назначенного системой удостоверения](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   На этом этапе версия будет выглядеть примерно так:

   ![Параметры проверки подлинности Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Закончив, нажмите кнопку **OK**.

1. Вернитесь в конструктор приложений логики и выполните [действия по проверке подлинности доступа с помощью управляемого удостоверения](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [соединителях Logic Apps](../connectors/apis-list.md).
