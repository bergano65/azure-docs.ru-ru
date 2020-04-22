---
title: Настройка управления свинцом с помощью конечной точки HTTPS Лазурный рынок
description: Узнайте, как использовать конечную точку HTTP для обработки интересов Microsoft AppSource и Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770188"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Настройка управления интересами с помощью конечной точки HTTPS

Для обработки интересов Microsoft AppSource и Azure Marketplace можно использовать конечную точку HTTPS. Эти интересы могут быть записаны в систему управления взаимоотношениями с клиентами (CRM) или отправлены в виде уведомления по электронной почте. В этой статье описывается, как использовать службу автоматизации [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) для настройки руководства.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Создание потока с помощью автоматизации Microsoft Power

1. Откройте веб-страницу [Power Automate.](https://flow.microsoft.com/) Чтобы создать бесплатную учетную запись потока, нажмите **Войти** или выберите **Бесплатная регистрация**.

1. Войдите и выберите **Мои потоки** в строке меню.
    > [!div class="mx-imgBorder"]
    > ![Мои потоки](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. В **соответствии**с no New , выберите **мгновенный - от пустых**.
    > [!div class="mx-imgBorder"]
    > ![Создание с нуля](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Назовите поток, а затем под **выберите, как вызвать этот поток,** выберите **Когда получен запрос HTTP.**

    > [!div class="mx-imgBorder"]
    > ![Выбор триггера "При получении HTTP-запроса"](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Нажмите на шаг потока, чтобы расширить его.

    > [!div class="mx-imgBorder"]
    > ![Расширить шаг потока](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Используйте один из следующих методов для настройки **органа запроса JSON Schema:**

   - Скопируйте [схему JSON](#json-schema) в конце этой статьи в текстовое поле **Схема JSON текста запроса**.
   - Выберите **Использовать пример полезной нагрузки, чтобы создать схему**. В текстовое поле **Enter or paste a sample JSON payload** (Введите или вставьте пример полезных данных JSON) вставьте [пример JSON](#json-example). Выберите **Готово**, чтобы создать схему.

   >[!Note]
   >На этом этапе потока можно подключиться к системе CRM или настроить уведомление по электронной почте.

### <a name="to-connect-to-a-crm-system"></a>Подключение к системе CRM

1. Выберите **новый шаг**.
2. Выберите систему управления отношениями с клиентами по своему усмотрению и с помощью действия создайте новую запись. На приведенном ниже снимке экрана показан пример действия **Dynamics 365 для создания записи**.

    ![Создание записи](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Укажите **имя организации**, которое является входными данными для подключения соединителя. Выберите **Интересы** в раскрывающемся списке **Имя сущности**.

    ![Выбор потенциальных клиентов](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. В службе Flow отобразится форма для предоставления информации о потенциальном клиенте. Вы можете сопоставить элементы из запроса ввода, выбрав динамическое содержимое. На приведенном ниже снимке экрана показан пример **OfferTitle**.

    ![Добавление динамического содержимого](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Сопоставьте нужные поля и сохраните поток нажав **эту** кнопку.

6. В запросе создается URL-адрес HTTP POST. Скопируйте этот URL-адрес и используйте его в качестве конечной точки HTTPS.

    ![URL-адрес HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Настройка уведомлений по электронной почте

1. Выберите **новый шаг**.
2. В разделе **Выберите действие** выберите **Действия**.
3. В разделе **Действия** выберите **Отправить электронное письмо**.

    ![Добавление действия электронной почты](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. В разделе **Отправить электронное письмо** настройте следующие обязательные поля:

   - **To** (Кому) — укажите не менее одного допустимого адреса электронной почты.
   - **Subject** (Тема) — Flow дает возможность добавить динамическое содержимое, например **LeadSource**, как показано на приведенном ниже снимке экрана.

     ![Добавление действия электронной почты с помощью динамического содержимого](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Body** (Текст) — добавьте нужные сведения в текст электронного письма из списка динамического содержимого. Например, LastName (фамилия), FirstName (имя), Email (адрес электронной почты) и Company (компания).

   После настройки уведомление по электронной почте будет выглядеть, как в примере на приведенном ниже снимке экрана.

   ![Добавление действия электронной почты](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Нажмите кнопку **Сохранить**, чтобы завершить поток.

6. В запросе создается URL-адрес HTTP POST. Скопируйте этот URL-адрес и используйте его в качестве конечной точки HTTPS.

    ![URL-адрес HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Настройка предложения для отправки списка потенциальных клиентов в конечную точку HTTPS

Когда вы настраиваете информацию управления интересами для своего предложения, выберите **конечную точку HTTPS** в качестве **назначения интереса** и вставьте URL-адрес HTTP POST, который был скопирован на предыдущем шаге.  

![Добавление динамического содержимого](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

При генерации интересов корпорация Майкрософт отправляет на поток Power Automate, который направляется в систему CRM или адрес электронной почты, настроенный на настройку.

## <a name="json-schema-and-example"></a>Пример и схема JSON

В тестовом примере JSON используется следующая схема:

### <a name="json-schema"></a>Схема JSON

``` json
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
    "Description": {
      "id": "/properties/Description",
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

Вы можете скопировать и отсечь следующий пример JSON для использования в качестве теста в потоке.

### <a name="json-example"></a>Пример JSON-файла

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

## <a name="next-steps"></a>Следующие шаги

Настройте получение сведений об [интересах](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) на Портале Cloud Partner, если это еще не сделано.
