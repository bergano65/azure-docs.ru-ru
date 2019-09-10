---
title: Руководство по Создание одностраничного веб-приложения службы "Аналитика временных рядов Azure" | Документация Майкрософт
description: Описание способов создания одностраничного веб-приложения, которое запрашивает и преобразовывает для просмотра данные из среды Аналитики временных рядов Azure.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194212"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Руководство по Создание одностраничного веб-приложения службы "Аналитика временных рядов Azure"

Этот учебник поможет выполнить процесс создания собственного одностраничного веб-приложения (SPA) для доступа к данным Аналитики временных рядов Azure.

В этом руководстве описывается, как выполнять следующие задачи.

> [!div class="checklist"]
> * Как спроектировать приложение.
> * Как зарегистрировать приложение с помощью Azure Active Directory (Azure AD)
> * Как построить, опубликовать и протестировать веб-приложение.

> [!NOTE]
> * Исходный код этого руководства приведен на [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * [Пример клиентского приложения](https://insights.timeseries.azure.com/clientsample) Аналитики временных рядов размещено, чтобы показать готовое приложение, используемое в этом руководстве.

Зарегистрируйтесь для [бесплатной подписки Azure](https://azure.microsoft.com/free/), если у вас ее еще нет.

## <a name="prerequisites"></a>Предварительные требования

* Бесплатная копия Visual Studio. Скачайте [версии 2017 или 2019 Community](https://www.visualstudio.com/downloads/), чтобы приступить к работе.

* Компоненты основных инструментов IIS Express, веб-развертывания и облачных служб Azure для Visual Studio. Добавьте компоненты, изменив установку Visual Studio.

## <a name="understand-application-design"></a>Проектирование приложений

Пример одностраничного приложения Аналитики временных рядов является основой для разработки и кода, используемом в этом учебнике. Для кода используется клиентская библиотека JavaScript для службы "Аналитика временных рядов Azure". Клиентская библиотека Аналитики временных рядов предоставляет абстракцию для двух основных категорий API.

- **Методы создания программы-оболочки для вызова API запросов Аналитики временных рядов**. Интерфейсы REST API можно использовать, чтобы запрашивать данные Аналитики временных рядов, используя выражения на основе JSON. Методы организованы в пространстве имен TsiClient.server библиотеки.

- **Методы создания и заполнения нескольких типов элементов управления диаграммами**. Методы можно использовать для визуализации данных Аналитики временных рядов на веб-странице. Методы организованы в пространстве имен TsiClient.ux библиотеки.

В этом учебнике также используются данные из среды Аналитики временных рядов для примера приложения. Дополнительные сведения о структуре примера приложения Аналитики временных рядов и о том, как она использует клиентскую библиотеку Аналитики временных рядов, см. в статье [Руководство. Изучение клиентской библиотеки JavaScript для службы "Аналитика временных рядов Azure"](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Регистрация с помощью Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Сборка и публикация

1. Создайте каталог для сохранения файлов проекта приложения. Затем перейдите к каждому из следующих URL-адресов. Щелкните правой кнопкой мыши ссылку **Необработанный** в правом верхнем углу страницы, а затем выберите **Сохранить как**, чтобы сохранить файлы в каталоге проекта.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): код HTML и JavaScript для страницы
   - [*sampleStyles.css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): таблицы стилей CSS

   > [!NOTE]
   > В зависимости от браузера перед сохранением файла может потребоваться изменить расширения файла на HTML или CSS.

1. Убедитесь, что требуемые компоненты установлены в Visual Studio. Для Visual Studio должны быть установлены компоненты основных инструментов IIS Express, веб-развертывания и облачных служб Azure.

    [![Изменение установленных компонентов в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Работа с Visual Studio может незначительно отличаться от приведенных примеров в зависимости от версии и параметров конфигурации.

1. Откройте Visual Studio и выполните вход. Чтобы создать проект для веб-приложения, в меню **Файл** выберите **Открыть** > **Веб-сайт**.

    [![Создание решения в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. В области **Открыть веб-сайт** выберите рабочий каталог, в котором сохранены файлы HTML и CSS, затем выберите **Открыть**.

   [![Меню "Файл" с параметрами "Открыть" и "Веб-сайт" в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. В меню **Представление** в Visual Studio выберите **Обозреватель решений**. Откроется новое решение. Оно содержит проект веб-сайта (значок глобуса) с файлами HTML и CSS.

   [![Новое решение в обозревателе решений в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Перед публикацией приложения необходимо изменить параметры конфигурации в *index.html*.

   1. Раскомментируйте три строки в комментарии `"PROD RESOURCE LINKS"`, чтобы переключить зависимости из DEVELOPMENT на PRODUCTION. Закомментируйте три строки в комментарии `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Ваши зависимости должны быть закомментированы, как показано в следующем примере:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Чтобы настроить приложение для использования идентификатора регистрации приложения Azure AD, измените значение `clientID`, чтобы использовать **идентификатор приложения**, скопированный на **шаге 3** при [регистрации приложения с помощью Azure AD](#register-with-azure-ad). Если вы создали **URL-адрес выхода** в Azure AD, установите его в качестве значения `postLogoutRedirectUri`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Например:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Закончив вносить изменения, сохраните *index.html*.

1. Опубликуйте веб-приложение в подписке Azure в качестве службы приложений Azure.  

   > [!NOTE]
   > Несколько параметров, показанных на снимках экрана на следующих шагах, автоматически заполняются данными из подписки Azure. Полная загрузка каждой области может занять несколько секунд.  

   1. В обозревателе решений щелкните правой кнопкой мыши узел проекта веб-сайта, а затем выберите **Опубликовать веб-приложение**.  

      [![Выбор параметра "Опубликовать веб-приложение" в обозревателе решений в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Выберите **Начало**, чтобы начать публикацию приложения.

      [![Область "Профиль публикации" в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Опубликуйте новый экземпляр Службы приложений Azure или выберите существующий.

      [![Выбор экземпляра Службы приложений Azure](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Выберите подписку, которую вы хотите использовать для публикации приложения. Выберите проект **TsiSpaApp**. Нажмите кнопку **ОК**.

      [![Область "Профиль публикации" службы приложений в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Выберите **Опубликовать**, чтобы развернуть веб-приложение.

      [![Параметр "Опубликовать" и выходные данные журнала публикации в Visual Studio](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Журнал успешной публикации отображается в области **Выходные данные** в Visual Studio. После завершения развертывания на вкладке браузера откроется запрос для входа в веб-приложение. После успешного входа элементы управления Аналитики временных рядов заполняются данными.

   1. Войдите в веб-приложение, чтобы просмотреть отображаемые визуальные данные Аналитики временных рядов.

      [![Проверка размещенного веб-приложения](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Устранение неполадок  

Код ошибки и состояние | ОПИСАНИЕ
---------------------| -----------
*AADSTS50011. Для приложения не зарегистрирован адрес ответа* | В регистрации Azure AD отсутствует свойство **URL-адрес перенаправления**. Перейдите на страницу **Аутентификация** > **URL-адреса перенаправления** для регистрации приложения Azure AD. Убедитесь в наличии **URI перенаправления**, который вы могли указать на **шаге 2** или **шаге 4** при [регистрации приложения для использования Azure AD](#register-with-azure-ad).
*AADSTS50011. The reply url specified in the request does not match the reply urls configured for the application: '\<Application ID GUID>'.* (Указанный в запросе URL-адрес ответа не соответствует URL-адресам ответа, настроенным для приложения: <глобальный уникальный идентификатор приложения>.) | Значение `postLogoutRedirectUri`, указанное на **шаге 6.б** в разделе [Создание и публикация веб-приложения](#build-and-publish), должно соответствовать значению, указанному в разделе **Аутентификация** > **URL-адреса перенаправления** в регистрации приложения Azure AD. |
Веб-приложение загружается, но на странице входа есть только текст и белый фон без стилей. | Убедитесь, что пути, описанные на **шаге 6** в разделе [Создание и публикация веб-приложения](#build-and-publish), заданы правильно. Если веб-приложение не может найти файлы CSS, страница не будет правильно отображаться.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этом руководстве создано несколько запущенных служб Azure. Если вы не планируете изучать эту серию учебников, мы советуем удалить все ресурсы, чтобы избежать лишних расходов.

В меню слева на портале Azure:

1. Щелкните **Группы ресурсов** и выберите группу ресурсов, которую вы создали для среды Аналитики временных рядов. В верхней части страницы выберите **Удалить группу ресурсов**, введите имя группы ресурсов и щелкните **Удалить**.
1. Щелкните **Группы ресурсов**, затем выберите группу ресурсов, автоматически созданную акселератором решений для имитации устройств. В верхней части страницы выберите **Удалить группу ресурсов**, введите имя группы ресурсов и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Из этого учебника вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Как спроектировать приложение.
> * Как зарегистрировать приложение в Azure AD.
> * Как построить, опубликовать и протестировать веб-приложение.

Этот учебник интегрируется с Azure AD и использует удостоверение пользователя, вошедшего в систему для получения маркера доступа. Дополнительные сведения о том, как получить доступ к API Аналитики временных рядов, используя удостоверение приложения службы или управляющей программы, см. в следующей статье:

> [!div class="nextstepaction"]
> [Проверка подлинности и авторизация для API службы "Аналитика временных рядов Azure"](time-series-insights-authentication-and-authorization.md)
