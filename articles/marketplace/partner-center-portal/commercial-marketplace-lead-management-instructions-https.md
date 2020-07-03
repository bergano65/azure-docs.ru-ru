---
title: Управление интересами с помощью конечной точки HTTPS. коммерческий рынок корпорации Майкрософт
description: Узнайте, как использовать Power автоматизиру и конечную точку HTTPS для управления интересами из Microsoft AppSource и Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7a4fc57b3be8dd59997ef2bfc9624892cf726160
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790989"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Использование конечной точки HTTPS для управления интересами для коммерческого рынка

Если система управления отношениями с клиентами (CRM) не была явно поддерживается в центре партнеров для получения Microsoft AppSource и интересов Azure Marketplace, вы можете использовать конечную точку HTTPS в [Power автоматизирующих](https://powerapps.microsoft.com/automate-processes/) для обработки этих интересов. С конечной точкой HTTPS интересы коммерческих рынков можно отправить в виде уведомления по электронной почте, или они могут быть записаны в систему CRM, поддерживаемую в Power автоматизировать.

В этой статье объясняется, как создать новый поток в Power Автоматизация, чтобы создать URL-адрес HTTP POST, который будет использоваться для настройки интересов в центре партнеров. Он также включает шаги для тестирования последовательности с помощью [POST](https://www.getpostman.com/downloads/).

>[!NOTE]
>Соединитель Power автоматизиру, используемый в этих инструкциях, требует платной подписки на Power автоматизиру. Перед настройкой этого потока обязательно выполните учетную запись.

## <a name="create-a-flow-by-using-power-automate"></a>Создание последовательности с помощью Power автоматизиру

1. Откройте веб-страницу [Power автоматизирующие](https://flow.microsoft.com/) . Выберите **Войти**. Если у вас еще нет учетной записи, выберите **зарегистрироваться бесплатно** , чтобы создать бесплатную учетную запись Power автоматизировать.

1. Войдите и выберите в меню пункт **мои потоки** .

    ![Вход в мои потоки](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. В разделе **+ создать**выберите **+ мгновенно — с пустого**значения.

    ![Мои потоки + автоматизированное — из пустых](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Назовите последовательность, а затем в разделе **Выбор способа активации этого потока**выберите **время получения HTTP-запроса**.

    ![Создание кнопки "пропустить" окна автоматизированного потока](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Щелкните шаг потока, чтобы развернуть его.

    ![Развернуть шаг потока](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Чтобы настроить **схему JSON текста запроса**, используйте один из следующих методов:

    - Скопируйте схему JSON в текстовое поле текст **запроса схема JSON** .
    - Выберите **Использовать пример полезной нагрузки, чтобы создать схему**. В текстовом поле **Ввод или Вставка образца полезных данных JSON** вставьте пример JSON. Выберите **Готово**, чтобы создать схему.

    **Схема JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **Пример JSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>На этом этапе настройки можно выбрать подключение к системе CRM или Настройка уведомления по электронной почте. Следуйте оставшимся инструкциям в зависимости от вашего выбора.

### <a name="connect-to-a-crm-system"></a>Подключение к системе CRM

1. Выберите **+ новый шаг**.
1. Выберите систему CRM по своему усмотрению, выполнив поиск там, где она будет **Искать соединители и действия поиска**. Выберите его на вкладке **действия** с действием создать новую запись. На следующем экране показано **Создание новой записи (Dynamics 365)** в качестве примера.

    ![Создание записи](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Укажите **название организации** , связанное с системой CRM. Выберите **интересы** из раскрывающегося списка **имя сущности** .

    ![Выбор потенциальных клиентов](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power автоматизирует отображение формы для предоставления сведений о интересах. Вы можете сопоставить элементы из запроса ввода, выбрав динамическое содержимое. На следующем экране показаны **оффертитле** в качестве примера.

    ![Добавление динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Сопоставьте нужные поля и нажмите кнопку **сохранить** , чтобы сохранить последовательность. URL-адрес HTTP POST создается и доступен в окне **Получение HTTP-запроса** . Скопируйте этот URL-адрес с помощью элемента управления копированием, расположенного справа от URL-адреса HTTP POST. Использование элемента управления копированием важно, чтобы не пропустить любую часть URL-адреса. Сохраните этот URL-адрес, так как он понадобится при настройке управления интересами на портале публикации.

    ![при получении HTTP-запроса;](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Настройка уведомления по электронной почте

1. Теперь, когда вы закончите схему JSON, выберите **+ новый шаг**.
1. В разделе **Выберите действие** выберите **Действия**.
1. На вкладке **действия** выберите **Отправить сообщение электронной почты (Office 365 Outlook)**.

    >[!NOTE]
    >Если вы хотите использовать другого поставщика электронной почты, найдите и выберите Отправить по **электронной почте уведомление (mail)** в качестве действия.

    ![Добавление действия электронной почты](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. В окне **Отправить сообщение электронной почты** настройте следующие обязательные поля:

   - **Кому**: введите по крайней мере один допустимый адрес электронной почты, по которому будут отправляться интересы.
   - **Тема**: Power автоматизирует позволяет добавить динамическое содержимое, например **леадсаурце** , показанное на следующем экране. Начните с ввода имени поля. Затем выберите список выбора динамического содержимого во всплывающем окне. 

        >[!NOTE] 
        > При добавлении имен полей каждое имя можно использовать с двоеточием (:) а затем нажмите клавишу **Ввод** , чтобы создать новую строку. После добавления имен полей можно добавить каждый связанный параметр из списка динамического выбора.

        ![Добавление действия электронной почты с помощью динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Текст**: из списка выбора динамического содержимого добавьте нужные сведения в текст сообщения. Например, используйте LastName, FirstName, email и Company. Завершив настройку уведомлений по электронной почте, он будет выглядеть так, как показано на следующем экране.


       ![Пример уведомления по электронной почте](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Нажмите кнопку **Сохранить**, чтобы завершить поток. URL-адрес HTTP POST создается и доступен в окне **Получение HTTP-запроса** . Скопируйте этот URL-адрес с помощью элемента управления копированием, расположенного справа от URL-адреса HTTP POST. Использование этого элемента управления важно, чтобы не пропустить любую часть URL-адреса. Сохраните этот URL-адрес, так как он понадобится при настройке управления интересами на портале публикации.

   ![URL-адрес HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Тестирование

Вы можете проверить конфигурацию с помощью [POST](https://app.getpostman.com/app/download/win64). Для Windows доступна загрузка из Интернета. 

1. Запустите POST и выберите **Новый** > **запрос** , чтобы настроить средство тестирования. 

   ![Запрос на настройку средства тестирования](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Заполните форму **запроса Save** и сохраните в созданную папку.

   ![Форма запроса Save](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Выберите **запись** в раскрывающемся списке. 

   ![Тестирование последовательности](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Вставьте URL-адрес HTTP POST из последовательности, созданной в Power автоматизируя там, где указано **URL-адрес запроса**.

   ![Вставьте URL-адрес HTTP POST.](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Вернитесь к [Power автоматизировать](https://flow.microsoft.com/). Найдите последовательность, созданную для отправки интересов, перейдя к пункту " **мои потоки** " в строке меню Power автоматизиру. Нажмите кнопку с многоточием рядом с именем потока, чтобы просмотреть дополнительные параметры, и выберите **изменить**.


1. Выберите **тест** в правом верхнем углу, выберите **действие триггера**и нажмите кнопку **проверить**. В верхней части экрана появится индикатор, который начал тест.

   ![Я выберу параметр действия триггера.](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Вернитесь к своему почтовому приложению и выберите **Отправить**.

   ![Кнопка "Отправить"](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Вернитесь к последовательности и проверьте результат. Если все работает правильно, вы увидите сообщение о том, что последовательность прошла успешно.

   ![Проверка результатов](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Кроме того, вы должны получить сообщение электронной почты. Проверьте папку "Входящие" электронной почты. 

    >[!NOTE] 
    >Если вы не видите электронное письмо с тестом, проверьте нежелательную почту и папки нежелательной почты. На следующем экране вы заметите только метки полей, добавленные при настройке уведомления по электронной почте. Если это фактический интерес, созданный на основе вашего предложения, вы также увидите фактические сведения из контактного лица в тексте и в строке темы.

   ![Электронная почта получена](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Настройка предложения для отправки списка потенциальных клиентов в конечную точку HTTPS

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Войдите в [Центр партнеров](https://partner.microsoft.com/dashboard/home).

1. Выберите свое предложение и перейдите на вкладку **Настройка предложения** .

1. В разделе **управление интересами** выберите **подключить**. 
    ![Кнопка подключения управления интересами](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

1. Во всплывающем окне **сведения о подключении** выберите **Конечная точка HTTPS** для **назначения интереса**. Вставьте URL-адрес HTTP POST из последовательности, созданной на предыдущих шагах, в поле **URL-адрес конечной точки HTTPS** .
    ![Контактный адрес электронной почты для сведений о подключении](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. В разделе **Контактная электронная почта**введите адреса электронной почты сотрудников Организации, которые должны получать уведомления по электронной почте при получении нового интереса. Можно указать несколько сообщений электронной почты, разделяя их точкой с запятой.

1. Щелкните **ОК**.

Чтобы убедиться, что вы успешно подключились к цели назначения, нажмите кнопку **проверить** . В случае успеха вы получите руководителя тестирования в целевом месте назначения.

>[!NOTE] 
>Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.

Когда интересы создаются, корпорация Майкрософт отправляет интересы в последовательность. Интересы направляются в систему CRM или адрес электронной почты, который вы настроили.
