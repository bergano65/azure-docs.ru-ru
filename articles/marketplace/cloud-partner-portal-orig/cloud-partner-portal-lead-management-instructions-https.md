---
title: Конечная точка HTTPS | Документация Майкрософт
description: Настройка управления интересами для конечной точки HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 8898149e6858c5a1cdb2d4510ad2764ffe25fda5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964259"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Настройка управления интересами с помощью конечной точки HTTPS

Вы можете использовать конечную точку HTTPS для обработки интересов Azure Marketplace и AppSource. Сведения об этих интересах можно записать в систему управления отношениями с клиентами (CRM) или отправить как уведомление по электронной почте. В этой статье описана настройка управления интересами с помощью службы автоматизации [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/).

## <a name="create-a-flow-using-microsoft-flow"></a>Создание потока с помощью Microsoft Flow

1. Откройте веб-страницу [Поток](https://flow.microsoft.com/). Чтобы создать бесплатную учетную запись потока, нажмите **Войти** или выберите **Бесплатная регистрация**.

2. Войдите и выберите **Мои потоки** в строке меню.

    ![Мои потоки](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Выберите **+ Создать с нуля**.

    ![Создание с нуля](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Выберите **Создать с нуля**.

    ![Создание с нуля](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. В поле **Поиск соединителей и триггеров** введите "запрос", чтобы найти соединитель "Запрос".
6. На вкладке **Триггеры** выберите триггер **При получении HTTP-запроса**. 

    ![Выбор триггера "При получении HTTP-запроса"](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Настройте **схему текста запроса JSON** одним из следующих способов:

   - Скопируйте [схему JSON](#JSON-schema) в конце этой статьи в текстовое поле **Схема JSON текста запроса**.
   - Выберите **Использовать пример полезной нагрузки, чтобы создать схему**. В текстовое поле **Enter or paste a sample JSON payload** (Введите или вставьте пример полезных данных JSON) вставьте [пример JSON](#JSON-example). Выберите **Готово**, чтобы создать схему.

   >[!Note]
   >На этом этапе потока можно подключиться к системе CRM или настроить уведомление по электронной почте.

### <a name="to-connect-to-a-crm-system"></a>Подключение к системе CRM

1. Выберите **+ Новый шаг**.
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

1. Выберите **+ Новый шаг**.
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

При создании интересов корпорация Майкрософт отправляет их в службу Flow. Затем выполняется маршрутизация в систему CRM или на адрес электронной почты, настроенные вами.

## <a name="json-schema-and-example"></a>Пример и схема JSON

В тестовом примере JSON используется следующая схема:

### <a name="json-schema"></a>Схема JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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

Можно скопировать и изменить приведенный ниже пример JSON, чтобы использовать его в качестве теста в Microsoft Flow.

### <a name="json-example"></a>Пример JSON-файла

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Дополнительная информация

Настройте получение сведений об [интересах](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) на Портале Cloud Partner, если это еще не сделано.
