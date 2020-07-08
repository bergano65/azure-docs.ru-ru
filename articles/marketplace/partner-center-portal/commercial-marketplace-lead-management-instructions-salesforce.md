---
title: Управление интересами в Salesforce — коммерческая платформа Майкрософт
description: Узнайте, как использовать Salesforce для управления интересами в Microsoft AppSource и Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7fbe27845164eb7711d3531173fb440c5a3570f4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849147"
---
# <a name="configure-lead-management-for-salesforce"></a>Настройка управления интересами для Salesforce

В этой статье описывается, как настроить систему Salesforce для обработки потенциальных клиентов ваших предложений в Microsoft AppSource и Azure Marketplace как интересов.

> [!NOTE]
> Azure Marketplace не поддерживает предварительно заполненные списки, например список значений для поля **Страна**. Прежде чем продолжить, убедитесь, что никаких списков не настроено. Также можно настроить конечную точку [HTTPS](./commercial-marketplace-lead-management-instructions-https.md) или [таблицу Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) для получения интересов.

## <a name="set-up-your-salesforce-system"></a>Настройка системы Salesforce

1. Войдите в Salesforce.
1. Перейдите к параметрам **Web-to-Lead**. 
    
    Если вы используете Salesforce Lighting Experience
    1. На домашней странице Salesforce выберите **Настройка**.

       ![Настройка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. На странице **Установка** перейдите в раздел **Инструменты платформы** > **Параметры функции** > **Маркетинг** > **Web-to-Lead**.

        ![Web-to-Lead в Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Если вы используете классический интерфейс Salesforce:

    1. На домашней странице Salesforce выберите **Настройка**.

       ![Классическая настройка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. На странице **Настройка** выберите **Сборка** > **Настроить** > **Интересы** > **Web-to-Lead**.

        ![Классический Web-to-Lead Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Оставшиеся шаги одинаковы для обеих интерфейсов Salesforce.

1. На странице **Настройка Web-to-Lead** щелкните кнопку **Создать форму Web-to-Lead**.
1. В разделе **Настройка Web-to-Lead** выберите **Создать форму Web-to-Lead**.

    ![Настройка Web-to-Lead Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. В разделе **Создать форму Web-to-Lead** убедитесь, что параметр `Include reCAPTCHA in HTML` очищен, и выберите **Создать**.

    ![Создание панели формы Web-to-Lead в Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Появится текст HTML. Найдите текст "oid", скопируйте значение **"OID"** из текста HTML (только текст между кавычками) и сохраните его. Это значение будет вставлено в поле **Идентификатор организации** на портале публикации.

    ![Salesforce создание формы Web-to-Lead с отображением значения "oid" в формате HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Щелкните **Готово**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Настройка предложения для отправки интересов в Salesforce

После того как все готово к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Войдите в [Центр партнеров](https://partner.microsoft.com/dashboard/home).

1. Выберите свое предложение и перейдите на вкладку **Настройка предложения**.

1. В разделе **Потенциальные клиенты** выберите **Подключить**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Потенциальные клиенты":::

1. Во всплывающем окне **Сведения о подключении** выберите **Salesforce** как **Место назначения интересов** и вставьте значение `oid` из формы Web-to-Lead, созданной в поле **Идентификатор организации**.

    ![Всплывающее окно со сведениями о подключении, поле проверки контактного адреса электронной почты](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. В разделе **Контактный адрес электронной почты** введите адреса электронной почты сотрудников организации, которые должны получать уведомления по электронной почте при обнаружении нового интереса. Можно указать несколько адресов электронной почты, разделяя их точкой с запятой.

1. Щелкните **ОК**.

Чтобы убедиться, что вы успешно подключились к месту назначения интересов, выберите **Проверить**. В случае успеха вы получите тестовый интерес в месте назначения интересов.

>[!NOTE]
>Завершите настройку остальной части предложения и опубликуйте его, прежде чем вы сможете получать сведения об интересах для своего предложения.
