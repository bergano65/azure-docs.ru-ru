---
title: Руководство по Модерация содержимого Facebook с помощью Content Moderator
titlesuffix: Azure Cognitive Services
description: Из этого руководства вы узнаете, как использовать Content Moderator на основе машинного обучения для модерации комментариев и записей на Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: dd06330e82850cc44bc0f4d36ba7caf596ace939
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603515"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Руководство по Модерация записей и команд Facebook с помощью Azure Content Moderator

Из этого руководства вы узнаете, как использовать Azure Content Moderator для модерации комментариев и записей на странице Facebook. Facebook будет отправлять содержимое, опубликованное посетителями, в службу Content Moderator. Затем рабочие процессы Content Moderator будут публиковать это содержимое или создавать проверки в инструменте проверки. Это будет зависеть от пороговых значений и оценки содержимого. Действующий пример этого сценария см. в демо-ролике [Keeping “wolves” out of your platform](https://channel9.msdn.com/Events/Build/2017/T6033) (Не подпускайте "волков" к своей платформе).

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> * создание команды Content Moderator;
> * создание приложения-функции Azure, которое ожидает передачи событий HTTP из Content Moderator и Facebook.
> * связывание страницы Facebook и Content Moderator с помощью приложения Facebook.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Каждый из компонентов данного сценария отображен на этой схеме.

![Схема получения информации Content Moderator из Facebook с помощью FBListener и ее отправки с помощью CMListener](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> В 2018 году компания Facebook внедрила более строгий предварительный анализ приложений Facebook. Вы не сможете выполнить инструкции в этом учебнике, если ваше приложение не было проверено и одобрено командой проверяющих специалистов Facebook.

## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки Content Moderator. Следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку и ключ для службы Content Moderator.
- [Учетная запись Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Создание команды проверки

Изучите статью [Краткое руководство. Опробование Content Moderator в Интернете](quick-start.md), в которой предоставлены инструкции по регистрации для использования [средства проверки Content Moderator](https://contentmoderator.cognitive.microsoft.com/) и созданию команды проверки. Запишите значение **Идентификатор команды**, которое указано на странице **Учетные данные**.

## <a name="configure-image-moderation-workflow"></a>Настройка рабочего процесса модерации изображений

Чтобы создать пользовательский рабочий процесс для изображений, ознакомьтесь со статьей [Определение, тестирование и использование рабочих процессов](review-tool-user-guide/workflows.md). Content Moderator будет автоматически использовать этот рабочий процесс для проверки изображений на Facebook и отправлять некоторые из них в инструмент проверки. Обратите внимание на **имя** рабочего процесса.

## <a name="configure-text-moderation-workflow"></a>Настройка рабочего процесса модерации текста

Чтобы создать пользовательский рабочий процесс для текста, повторно обратитесь к статье [Define and use moderation workflows](review-tool-user-guide/workflows.md) (Определение и использование рабочих процессов). Content Moderator будет использовать этот рабочий процесс для автоматической проверки текстового содержимого. Обратите внимание на **имя** рабочего процесса.

![Настройка рабочего процесса для текста](images/text-workflow-configure.PNG)

Протестируйте рабочий процесс, нажав кнопку **Execute Workflow** (Выполнить рабочий процесс).

![Тестирование рабочего процесса для текста](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Создание приложения-функции Azure

Войдите на [портал Azure](https://portal.azure.com/) и выполните следующее.

1. Создайте приложение-функцию Azure, как показано на странице [Создание приложения-функции на портале Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Перейдите к только что созданному приложению-функции.
1. В приложении перейдите на вкладку **Функции платформы** и выберите **Конфигурация**. В разделе **Параметры приложения** на следующей странице выберите **Новый параметр приложения** и добавьте приведенные ниже пары "ключ-значение".
    
    | Имя параметра приложения | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Идентификатор команды Content Moderator.  | 
    | cm:SubscriptionKey | Ключ подписки Content Moderator (см. раздел [Учетные данные](review-tool-user-guide/credentials.md)). |
    | cm:Region | Имя региона Content Moderator без пробелов. |
    | cm:ImageWorkflow | Имя рабочего процесса для изображений. |
    | cm:TextWorkflow | Имя рабочего процесса для текста. |
    | cm:CallbackEndpoint | URL-адрес приложения-функции CMListener, которое будет создано позже в этом руководстве. |
    | fb:VerificationToken | Маркер создаваемого вами секрета, который используется для подписки на события веб-канала Facebook. |
    | fb:PageAccessToken | Срок действия маркера доступа API Graph Facebook не истекает, что позволяет функции скрывать или удалять записи от вашего имени. Вы получите его позже. |

    Нажмите кнопку **Сохранить** в верхней части страницы.

1. Вернитесь на вкладку **Функции платформы**. Чтобы открыть область **Новая функция**, в левой области нажмите кнопку **+** . Функция, которую вы собираетесь создать, будет получать события от Facebook.

    ![Панель "Функции Azure" с выделенной кнопкой "Добавить функцию".](images/new-function.png)

    1. Щелкните плитку **Триггер Http**.
    1. Введите имя **FBListener**. В поле **Уровень авторизации** нужно указать значение **Функция**.
    1. Нажмите кнопку **Создать**.
    1. Замените содержимое **run.csx** содержимым из **FbListener/run.csx**.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Создайте функцию **Триггер Http** с именем **CMListener**. Эта функция получает события от Content Moderator. Замените содержимое **run.csx** содержимым из **CMListener/run.csx**.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Настройка страницы и приложения Facebook

1. Создайте приложение Facebook.

    ![Страница разработчиков Facebook](images/facebook-developer-app.png)

    1. Перейдите на [сайт разработчиков Facebook](https://developers.facebook.com/).
    1. Щелкните **Мои приложения**.
    1. Добавьте новое приложение.
    1. Введите имя приложения.
    1. Выберите **Веб-перехватчики -> Настройка**.
    1. В раскрывающемся меню последовательно выберите **Страница**, а затем **Subscribe to this object** (Подписаться на этот объект).
    1. Укажите **URL-адрес FBListener** в качестве URL-адреса обратного вызова и введите **маркер проверки**, который был настроен в **параметрах функции-приложения**.
    1. Создав подписку, прокрутите страницу вниз до веб-канала и выберите **subscribe** (Подписаться).
    1. Нажмите кнопку **Test** (Тестировать) в строке **веб-канала**, чтобы отправить тестовое сообщения в функцию Azure FBListener, а затем нажмите кнопку **Send to My Server** (Отправить на мой сервер). Вы должны увидеть, что ваша функция FBListener получила запрос.

1. Создайте страницу Facebook.

    > [!IMPORTANT]
    > В 2018 году компания Facebook внедрила более строгий предварительный анализ приложений Facebook. Вы не сможете выполнить инструкции в разделах 2, 3 и 4, если ваше приложение не было проверено и одобрено командой проверяющих специалистов Facebook.

    1. Перейдите на сайт [Facebook](https://www.facebook.com/bookmarks/pages) и создайте **страницу Facebook**.
    1. Разрешите приложению Facebook доступ к этой странице, выполнив следующие действия.
        1. Перейдите к [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Выберите **Приложение**.
        1. Выберите **Page Access Token** (Маркер доступа к странице) и отправьте запрос **Get**.
        1. Щелкните **Page ID** (Идентификатор страницы) в ответе.
        1. Теперь добавьте **/subscribed_apps** в URL-адрес и отправьте запрос **Get** (пустой ответ).
        1. Отправьте запрос **Post**. Вы получите ответ **success: true**.

3. Создайте бессрочный маркер доступа API Graph.

    1. Перейдите к [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Выберите **Приложение**.
    3. Выберите **Get User Access Token** (Получить маркер доступа пользователя).
    4. В разделе **Select Permissions** (Выбор разрешений) выберите параметры **manage_pages** и **publish_pages**.
    5. Мы будем использовать **маркер доступа** (краткосрочный маркер) на следующем шаге.

4. На нескольких следующих шагах мы используем Postman.

    1. Откройте **Postman** (или получите его [здесь](https://www.getpostman.com/)).
    2. Импортируйте следующие два файла:
        1. [коллекция Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json);
        2. [среда Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json).       
    3. Обновите следующие переменные среды.
    
        | Ключ | Значение   | 
        | -------------------- |-------------|
        | appId   | Вставьте идентификатор приложения Facebook.  | 
        | appSecret | Вставьте секрет приложения Facebook. | 
        | short_lived_token | Вставьте краткосрочный маркер доступа пользователя, созданный на предыдущем шаге. |
    4. Теперь запустите 3 интерфейса API, перечисленные в коллекции. 
        1. Выберите **Generate Long-Lived Access Token** (Создать долгосрочный маркер доступа) и нажмите кнопку **Отправить**.
        2. Выберите **Get User ID** (Получить идентификатор пользователя) и нажмите кнопку **Отправить**.
        3. Выберите **Get Permanent Page Access Token** (Получить бессрочный маркер доступа к странице) и нажмите кнопку **Отправить**.
    5. Скопируйте значение **access_token** из ответа и присвойте его параметру приложения **fb:PageAccessToken**.

Решение отправляет все изображения и тексты, опубликованные на странице Facebook, в Content Moderator. При этом вызываются рабочие процессы, которые были заданы ранее. Содержимое, которое не соответствует критериям, определенным в рабочих процессах, передается на проверку в инструменте проверки. Остальное содержимое публикуется автоматически.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы настроили программу, которая анализирует изображения товаров и присваивает им теги с типами товаров, а затем передает изображения команде проверки для принятия обоснованных решений по модерации содержимого. Теперь переходите к подробному изучению модерации изображений.

> [!div class="nextstepaction"]
> [Модерация изображений](./image-moderation-api.md)
