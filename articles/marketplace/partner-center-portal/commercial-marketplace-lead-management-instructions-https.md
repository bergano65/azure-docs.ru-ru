---
title: Конечная точка HTTPS Лазурный рынок
description: Настройка управления интересами для конечной точки HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262619"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Настройка управления интересами с помощью конечной точки HTTPS

>[!Note]
>Разъем Power Automate, используемый в этих инструкциях, требует платной подписки на Power Automate. Пожалуйста, объясните это, прежде чем следовать инструкциям в этом документе.

Если система управления взаимоотношениями с клиентами (CRM) явно не поддерживается в Партнерском центре для получения интересов Azure Marketplace и AppSource, для обработки этих интересов можно использовать конечную точку HTTPS в Power Automate. С точки зрения https эти интересы могут быть отправлены в виде уведомления по электронной почте или могут быть записаны в систему управления взаимоотношениями с клиентами (CRM), поддерживаемую Power Automate. Инструкции в этой статье будут проходить через основной процесс для создания нового потока с помощью Power Automate, который будет генерировать HTTP POST URL, который вы войдете в издательский портал для руководства управления > **httpS Endpoint поле URL.** Кроме того, включены инструкции о том, как вы можете проверить свой поток с помощью инструмента под названием [Почтальон,](https://www.getpostman.com/downloads/) который можно найти в Интернете.

## <a name="create-a-flow-using-power-automate"></a>Создание потока с помощью power Automate

1. Откройте веб-страницу [Поток](https://flow.microsoft.com/). Выберите **войти в**систему, или если у вас еще нет учетной записи, выберите **Подпишитесь бесплатно,** чтобы создать бесплатную учетную запись Flow.

2. Войдите и выберите **Мои потоки** в строке меню.

3. Выберите **«Автоматизированный» - из пробела**.

    ![Мои потоки и Автоматизированная - от пустых](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. В *сборке автоматического* окна потока выберите **Skip**. 

    ![Сборка автоматизированного потока - Перейти](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. В поле **Поиск соединителей и триггеров** введите "запрос", чтобы найти соединитель "Запрос".
6. На вкладке *Триггеры* выберите триггер **При получении HTTP-запроса**. 

    ![разъем запроса - Триггеры](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. На *Когда запрос HTTP получает* копию окна и вставьте схему JSON ниже в текстовый ящик **Органа Запроса JSON Schema.** Эта схема используется корпорацией Майкрософт для хранения данных о ведущих.

    ![разъем запроса - Триггеры](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>На этом этапе в конфигурации можно выбрать либо подключение к системе CRM, либо настроить уведомление по электронной почте. Следуйте оставшимся инструкциям, основанным на вашем выборе.

### <a name="to-connect-to-a-crm-system"></a>Подключение к системе CRM

1. Выберите **новый шаг**.
2. Выберите систему CRM по вашему выбору, ища ее там, где написано *разъемы поиска и действия,* и выберите ее в разделе *Действия* с действием для создания новой записи. Следующий захват экрана показывает **Dynamics 365 - Создайте** новую запись в качестве примера.

    ![Создание записи](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Предоставьте **название организации,** связанное с системой CRM. Выберите **Интересы** в раскрывающемся списке **Имя сущности**.

    ![Выбор потенциальных клиентов](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. В службе Flow отобразится форма для предоставления информации о потенциальном клиенте. Вы можете сопоставить элементы из запроса на ввод, выбрав для добавления динамического содержимого. На приведенном ниже снимке экрана показан пример **OfferTitle**.

    ![Добавление динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Сопоставьте нужные поля и сохраните поток нажав **эту** кнопку. Создается URL HTTP POST, который доступен в окне *при получении запроса HTTP.* Копирование этого URL с помощью элемента управления копий, который расположен справа от URL HTTP POST - это важно, чтобы вы не ошиблись ни одной частью всего URL. Сохраните этот URL-адрес при настройке управления свинцом на портале публикации.

    ![При получении запроса HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Настройка уведомлений по электронной почте

1. Теперь, когда вы завершили схему JSON, выберите **новый шаг**.
2. В разделе **Выберите действие** выберите **Действия**.
3. В соответствии с **действиями**выберите **Отправить электронное письмо (Office 365 Outlook)**.

    >[!Note]
    >Если вы хотите использовать другой поиск поставщика электронной почты и выберите *Отправить уведомление по электронной почте (Mail)* в качестве действия вместо.

    ![Добавление действия электронной почты](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. В **окне отправки электронной почты** наверсоняйте следующие необходимые поля:

   - **Для** - Введите по крайней мере один действительный адрес электронной почты, на который будут отправлены интересы.
   - **Subject** (Тема) — Flow дает возможность добавить динамическое содержимое, например **LeadSource**, как показано на приведенном ниже снимке экрана. Начните с ввода в названии поля, а затем нажав на список выбора динамического содержимого из всплывающего окна. 

        >[!Note] 
        > При добавлении имен полей, вы можете следовать каждому с ":" а затем введите, чтобы создать новый ряд. После добавления имен полей можно добавить каждый связанный параметр из списка динамических подборов.

        ![Добавление действия электронной почты с помощью динамического содержимого](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Тело** - Из списка выбора динамического контента добавьте нужную информацию в теле письма. Например, LastName (фамилия), FirstName (имя), Email (адрес электронной почты) и Company (компания). <br> <br> После настройки уведомление по электронной почте будет выглядеть, как в примере на приведенном ниже снимке экрана.


       ![Добавление действия электронной почты](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Нажмите кнопку **Сохранить**, чтобы завершить поток. Создается URL HTTP POST, который доступен в окне *при получении запроса HTTP.* Копирование этого URL с помощью элемента управления копий, который расположен справа от URL HTTP POST - это важно, чтобы вы не ошиблись ни одной частью всего URL. Сохраните этот URL-адрес при настройке управления свинцом на портале публикации.

   ![URL-адрес HTTP POST ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Тестирование

Вы можете проверить, что все работает как ожидалось, используя следующие шаги с помощью инструмента под названием [Postman](https://app.getpostman.com/app/download/win64), который может быть загружен в Интернете. Это доступно для Windows. 

1. Запустите Postman и выберите **новый** > **запрос** для настройки тестового инструмента. 

   ![Запрос на настройку тестового инструмента](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Заполните форму *запроса «Сохранить»,* а затем **сохраните** в созданной папке.

   ![Сохранение запроса](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Выберите **POST** из списка выпадающих. 

   ![Проверьте мой поток](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Вставьте URL HTTP POST из потока, созданного в Power Automate, где он говорит *URL-адрес ввода запроса.*

   ![Вставьте URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Вернитесь к [Flow](https://flow.microsoft.com/) и найдите созданный поток для отправки лидов, перейдя в **My Flows** из панели меню Flow.  Выберите 3 точки рядом с именем потока и выберите **Edit.**

   ![Мои потоки - Отстечение](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Выберите **тест** в правом верхнем углу, выберите "Я выполню триггерное действие", а затем выберите **Test.** В верхней части экрана вы увидите указание, указывающее, что тест начался

   ![Тестовый поток - триггер](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Вернитесь к приложению Postman и выберите **Отправить** рядом с местом, где вы вставили URL HTTPS.

   ![Проверьте мой поток - Отправить](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Вернитесь к потоку и проверьте результат. Если все работает так, как ожидалось, вы увидите сообщение, указывающее, что оно было успешным.

   ![Поток - Проверка результатов](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Вы также должны были получить по электронной почте. Проверьте свой почтовый ящик. 

    >[!Note] 
    >Если вы не видите электронную почту от теста, а затем проверить спам и нежелательной папки. Ниже вы заметите только поле этикетки вы добавили при настройке уведомления по электронной почте. Если бы это был фактический свинец, полученный из вашего предложения, вы бы также увидели фактическую информацию от ведущего контакта в теле и в строке Темы.

   ![Полученная электронная почта](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Настройка предложения для отправки списка потенциальных клиентов в конечную точку HTTPS

Когда вы будете готовы настроить информацию о руководстве для вашего предложения на портале публикации, выполните ниже:

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключение** в разделе «Управление ведущим иссяка».
3. На всплывающем окне подключения выберите **конечную точку HTTPS** для **направления свинца** и вставьте в URL HTTP POST из потока, который вы создали, выполнив более ранние шаги в поле **URL-адреса HTTPS.**
4. **Контактная почта** - Предоставьте электронные письма для людей в вашей компании, которые должны получать уведомления по электронной почте, когда новый свинец получен. Вы можете предоставить несколько писем, разделив их с запятой.
5. Щелкните **ОК**.

Чтобы убедиться, что вы успешно подключены к месту назначения, нажмите на кнопку проверки. В случае успеха, вы будете иметь тест свинца в главной цели.

>[!Note] 
>Вы должны закончить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить приводит для предложения.

При создании интересов корпорация Майкрософт отправляет их в службу Flow. Затем выполняется маршрутизация в систему CRM или на адрес электронной почты, настроенные вами.

![Руководство - подключить](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

