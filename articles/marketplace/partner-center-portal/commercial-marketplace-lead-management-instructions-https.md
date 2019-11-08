---
title: Конечная точка HTTPS | Azure Marketplace
description: Настройка управления интересами для конечной точки HTTPS.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812285"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Настройка управления интересами с помощью конечной точки HTTPS

Если система управления взаимоотношениями с клиентами (CRM) не поддерживается явно в центре партнеров для получения Azure Marketplace и AppSource, вы можете использовать конечную точку HTTPS в MS Flow для обработки этих интересов. С конечной точкой HTTPS эти интересы можно отправить в виде уведомления по электронной почте или можно записать в систему управления отношениями с клиентами (CRM), поддерживаемую MS Flow. Инструкции в этой статье помогут вам создать новый поток с помощью Microsoft Flow, который создаст URL-адрес HTTP POST, который будет введен на портал публикации для поля **URL-адрес конечной точки HTTPS** >. Кроме того, в состав включены инструкции по тестированию последовательности с помощью средства, именуемого [POST](https://www.getpostman.com/downloads/) , который можно найти в Интернете.

## <a name="create-a-flow-using-microsoft-flow"></a>Создание потока с помощью Microsoft Flow

1. Откройте веб-страницу [Поток](https://flow.microsoft.com/). Выберите **Вход**или если у вас еще нет учетной записи, выберите **зарегистрироваться бесплатно** , чтобы создать бесплатную учетную запись.

2. Войдите и выберите **Мои потоки** в строке меню.

3. Выберите **+ автоматически — из пустого**поля.

    ![Мои потоки + автоматизированный — из пустых](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. В окне *Построение автоматизированного потока* выберите **пропустить**. 

    ![Автоматизированный поток для сборки — пропустить](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. В поле **Поиск соединителей и триггеров** введите "запрос", чтобы найти соединитель "Запрос".
6. На вкладке *Триггеры* выберите триггер **При получении HTTP-запроса**. 

    ![соединитель запросов — триггеры](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. В диалоговом окне *при получении HTTP-запроса* Скопируйте приведенную ниже схему JSON в текстовое поле текст **запроса схема JSON** . Эта схема используется корпорацией Майкрософт для хранения данных интереса.

    ![соединитель запросов — триггеры](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!Note]
>На этом этапе настройки можно выбрать подключение к системе CRM или Настройка уведомления по электронной почте. Следуйте оставшимся инструкциям в зависимости от вашего выбора.

### <a name="to-connect-to-a-crm-system"></a>Подключение к системе CRM

1. Выберите **+ Новый шаг**.
2. Выберите систему CRM по своему усмотрению, выполнив поиск там, где отображается список *соединителей поиска и действий*, и выберите его в разделе *действия* с действием создать новую запись. На следующем снимке экрана показана **Dynamics 365 — создайте** новую запись в качестве примера.

    ![Создание записи](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Укажите **название организации** , связанное с системой CRM. Выберите **Интересы** в раскрывающемся списке **Имя сущности**.

    ![Выбор потенциальных клиентов](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. В службе Flow отобразится форма для предоставления информации о потенциальном клиенте. Вы можете сопоставлять элементы из входного запроса, выбрав Добавление динамического содержимого. На приведенном ниже снимке экрана показан пример **OfferTitle**.

    ![Добавление динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Сопоставьте нужные поля и сохраните поток нажав **эту** кнопку. Создается URL-адрес HTTP POST и доступен в окне *Получение HTTP-запроса* . Скопируйте этот URL-адрес с помощью элемента управления копированием, который расположен справа от URL-адреса HTTP POST. это важно, чтобы не пропустить все части URL-адреса по ошибке. Сохраните этот URL-адрес, так как он понадобится при настройке управления интересами на портале публикации.

    ![При получении HTTP-запроса.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Настройка уведомлений по электронной почте

1. После завершения схемы JSON выберите **+ новый шаг**.
2. В разделе **Выберите действие** выберите **Действия**.
3. В разделе **действия**выберите **Отправить сообщение электронной почты (Office 365 Outlook)** .

    >[!Note]
    >Если вы хотите использовать другой поставщик электронной почты, выполните поиск по *адресу и выберите Отправить уведомление по электронной почте (mail)* в качестве действия.

    ![Добавление действия электронной почты](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. В окне **Отправка сообщения электронной почты** настройте следующие обязательные поля.

   - Введите по крайней мере один допустимый адрес электронной почты, по которому будут отправляться интересы.
   - **Subject** (Тема) — Flow дает возможность добавить динамическое содержимое, например **LeadSource**, как показано на приведенном ниже снимке экрана. Начните с ввода имени поля, а затем щелкните список выбора динамического содержимого из всплывающего окна. 

        >[!Note] 
        > При добавлении имен полей можно следовать каждому из них с помощью ":", а затем ввести, чтобы создать новую строку. После добавления имен полей можно добавить каждый связанный параметр из списка динамического выбора.

        ![Добавление действия электронной почты с помощью динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Текст** — из списка выбора динамического содержимого добавьте нужные сведения в текст сообщения. Например, LastName (фамилия), FirstName (имя), Email (адрес электронной почты) и Company (компания). <br> <br> После настройки уведомление по электронной почте будет выглядеть, как в примере на приведенном ниже снимке экрана.


       ![Добавление действия электронной почты](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Нажмите кнопку **Сохранить**, чтобы завершить поток. URL-адрес HTTP POST создается и доступен в окне *Получение HTTP-запроса* . Скопируйте этот URL-адрес с помощью элемента управления копированием, который расположен справа от URL-адреса HTTP POST. это важно, чтобы не пропустить все части URL-адреса по ошибке. Сохраните этот URL-адрес, так как он понадобится при настройке управления интересами на портале публикации.

   ![URL-АДРЕС HTTP POST ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Тестирование

Вы можете проверить, что все работает правильно, выполнив следующие действия с помощью средства [POST](https://app.getpostman.com/app/download/win64), которое можно скачать в Интернете. Это доступно для Windows. 

1. Запустите POST и выберите **новый** > **запросе** , чтобы настроить средство тестирования. 

   ![Запрос на настройку средства тестирования](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Заполните форму *запроса Save* и **Сохраните** в созданную папку.

   ![Сохранить запрос](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Выберите **запись** в раскрывающемся списке. 

   ![Тестирование последовательности](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Вставьте URL-адрес HTTP POST из последовательности, созданной в MS Flow, где указано *URL-адрес запроса*.

   ![Вставьте URL-адрес HTTP POST.](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Вернитесь к [последовательности](https://flow.microsoft.com/) и найдите последовательность, созданную для отправки интересов, перейдя в раздел " **мои потоки** " из строки меню "поток".  Выберите три точки рядом с именем потока и нажмите кнопку **изменить**.

   ![Мои потоки — изменить](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Выберите **тест** в правом верхнем углу, выберите "я буду выполнять действие триггера", а затем щелкните " **проверить**". В верхней части экрана появится индикатор, указывающий, что тест начался

   ![Тестовый поток — триггер](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Вернитесь к своему почтовому приложению и выберите **Отправить** рядом с тем местом, куда вы вставили URL-адрес HTTPS.

   ![Проверка отправки потока](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Вернитесь к последовательности и проверьте результат. Если все работает правильно, вы увидите сообщение об успешном выполнении.

   ![Результаты проверки последовательности](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Кроме того, вы должны получить сообщение электронной почты. Проверьте папку "Входящие" электронной почты. 

    >[!Note] 
    >Если вы не видите сообщение с тестом, проверьте нежелательную почту и папки нежелательной почты. Ниже вы увидите только метки полей, добавленные при настройке уведомления по электронной почте. Если это фактический интерес, созданный на основе вашего предложения, вы также увидите фактические сведения из контактного лица в тексте и в строке темы.

   ![Электронная почта получена](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Настройка предложения для отправки списка потенциальных клиентов в конечную точку HTTPS

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключиться** в разделе Управление интересами.
3. Во всплывающем окне сведения о подключении выберите **Конечная точка HTTPS** для **назначения интереса** и вставьте URL-адрес HTTP POST в потоке, который вы создали, выполнив предыдущие шаги в поле **URL-адрес конечной точки HTTPS** .
4. Щелкните **Сохранить**. 

>[!Note] 
>Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.

При создании интересов корпорация Майкрософт отправляет их в службу Flow. Затем выполняется маршрутизация в систему CRM или на адрес электронной почты, настроенные вами.

![Управление интересами — подключение](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

