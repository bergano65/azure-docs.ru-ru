---
title: Настройка управления интересами для Salesforce | Azure Marketplace
description: Настройка управления интересами в Salesforce для клиентов Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133636"
---
# <a name="configure-lead-management-for-salesforce"></a>Настройка управления интересами для Salesforce

В этой статье описывается, как настроить систему salesforce для обработки интересов продаж из коммерческого предложения Marketplace.

> [!NOTE]
> Azure Marketplace не поддерживает предварительно заполненные списки, например список значений для поля Country ( **страна** ). Прежде чем продолжить, убедитесь, что списки не настроены. Кроме того, можно настроить [конечную точку HTTPS](./commercial-marketplace-lead-management-instructions-https.md) или [таблицу Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) для получения интересов.

## <a name="set-up-your-salesforce-system"></a>Настройка системы Salesforce

1. Войдите в Salesforce.
1. Если вы используете интерфейс освещения Salesforce:
    1. На домашней странице Salesforce выберите **Setup (Настройка** ).

       ![Настройка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. На странице **Установка** последовательно выберите **инструменты** > платформы**Параметры** > компоненты**маркетинг** > **в Интернете**.

        ![SalesForce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Если вы используете классический интерфейс Salesforce:

    1. На домашней странице Salesforce выберите **Setup (Настройка** ).

       ![Классическая установка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. На странице **Установка** выберите **Сборка** > **Настройка** > **интересов** > **Интернет-руководитель**.

        ![Классический веб-узел Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Остальные инструкции одинаковы, независимо от того, какой интерфейс Salesforce вы используете.

1. На странице **Настройка с веб-сайта** нажмите кнопку **создать форму Web-to-Lead** .
1. При **настройке "с веб-сайта**" выберите **создать форму с веб-интереса**.

    ![Настройка веб-приложения Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. При **создании формы "веб-форма**" `Include reCAPTCHA in HTML` убедитесь, что параметр сброшен, и выберите **создать**.

    ![SalesForce создание панели «веб-форма-интерес»](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Появится текст HTML. Выполните поиск текста "OID" и скопируйте **значение "OID"** из текста HTML (только текст между кавычками) и сохраните его. Это значение будет вставлено в поле **идентификатор организации** на портале публикации.

    ![SalesForce создание веб-формы с отображением значения "OID" в формате HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Щелкните **Готово**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Настройка предложения для отправки интересов в Salesforce

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
1. Выберите **Подключиться** в разделе **управление интересами** .

    ![Кнопка подключения раздела управления интересами](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Во всплывающем окне **сведения о подключении** выберите **Salesforce** для **назначения интереса** и вставьте `oid` значение из созданной формы в поле **идентификатор организации** .

1. В разделе **Контактная электронная почта**введите адреса электронной почты сотрудников Организации, которые должны получать уведомления по электронной почте при получении нового интереса. Можно указать несколько сообщений электронной почты, разделяя их точкой с запятой.

1. Нажмите кнопку **OK**.

Чтобы убедиться, что вы успешно подключились к цели назначения, нажмите кнопку **проверить** . В случае успеха вы получите руководителя тестирования в целевом месте назначения.

>[!NOTE]
>Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.

![Всплывающее окно "сведения о подключении" Выбор поля назначения интереса](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Всплывающее окно "сведения о подключении" Проверка поля Контактная электронная почта](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
