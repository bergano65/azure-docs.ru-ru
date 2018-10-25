---
title: Конечная точка HTTPS | Документация Майкрософт
description: Настройка управления интересами для HTTPS.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807151"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Настройка управления интересами с помощью конечной точки HTTPS

Вы можете использовать конечную точку HTTPS для обработки приложений Azure Marketplace и AppSource, которые могут быть записаны в систему управления отношениями с клиентами. В этой статье описывается настройка управления интересами с помощью службы автоматизации Microsoft Flow.


## <a name="create-a-flow-using-microsoft-flow"></a>Создание потока с помощью Microsoft Flow

1.  Откройте веб-страницу [Поток](https://flow.microsoft.com/). Чтобы создать бесплатную учетную запись потока, нажмите **Войти** или выберите **Бесплатная регистрация**.

2.  Войдите и выберите **Мои потоки** в строке меню.

    ![Мои последовательности](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Выберите **Создать с нуля**.

    ![Создание с нуля](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Выберите соединитель **Запрос/Ответ** и выполните поиск триггера запроса. 

    ![Создание с нуля](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Выберите триггер **Запрос**.
    ![Триггер запросов](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Скопируйте **пример JSON** в конце этой статьи в **Схему текста запроса JSON**.

7.  Добавьте новый шаг и выберите систему управления отношениями с клиентами по своему усмотрению и с помощью действия создайте новую запись. В качестве примера на следующем экране отобразится **Dynamics 365 (создание новой записи)**.

    ![Создание записи](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Предоставьте входные данные для подключения соединителя и выберите сущность **Потенциальные клиенты**.

    ![Выбор потенциальных клиентов](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Потоки показывают форму для предоставления информации о потенциальном клиенте. Вы можете сопоставить элементы из запроса ввода, выбрав динамическое содержимое.

    ![Добавление динамического содержимого](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Сопоставьте нужные поля и сохраните поток нажав **эту** кнопку.

11. В запросе создается URL-адрес HTTP POST. Скопируйте этот URL-адрес и используйте его в качестве конечной точки HTTPS.

    ![URL-адрес HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Настройка предложения для отправки списка потенциальных клиентов в конечную точку HTTPS

Когда вы настраиваете информацию управления интересами для своего предложения, выберите **Конечную точку HTTPS** для назначения потенциального клиента и вставьте URL-адрес HTTP POST, который был скопирован на предыдущем шаге.  

![Добавление динамического содержимого](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

При создании потенциальных клиентов, корпорация Майкрософт отправит их в поток, который направится в систему управления отношениями с клиентами, настроенную вами.


## <a name="json-example"></a>Пример JSON

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
