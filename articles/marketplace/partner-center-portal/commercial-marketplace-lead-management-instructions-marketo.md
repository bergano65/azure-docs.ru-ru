---
title: Управление интересами в Marketo — коммерческий рынок Майкрософт
description: Узнайте, как использовать систему Marketo CRM для управления интересами из Microsoft AppSource и Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 1f3a097629f8c5f4a3f0ecefa5ee50f2d3e62162
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789884"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Использование Marketo для управления интересами коммерческого рынка

В этой статье описывается, как настроить систему Marketo CRM для обработки интересов продаж из ваших предложений в Microsoft AppSource и Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Настройка системы Marketo CRM

1. Войдите в Marketo.

1. Выберите **Разработка студии**.

    ![Разработка студии Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Выберите **Новая форма**.

    ![Новая форма Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Заполните обязательные поля в диалоговом окне **Новая форма** , а затем выберите **создать**.

    ![Создание новой формы Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  На странице **сведения о поле** нажмите кнопку **Готово**.

    ![Завершение формы Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Утвердить и закрыть.

1. На вкладке **маркетплацелеадбаккенд** выберите **код внедрения**. 

    ![Код внедрения Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Код внедрения Marketo отображает код, аналогичный приведенному ниже.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Скопируйте значения для следующих полей, показанных в форме кода внедрения. Эти значения используются для настройки предложения на получение интересов на следующем шаге. Используйте следующий пример в качестве руководства по получению нужных идентификаторов из примера кода внедрения Marketo.

    - Идентификатор сервера = **ys12**
    - Идентификатор Munchkin = **123-PQR-789**
    - Идентификатор формы = **1179**

    Другой способ определить эти значения:

    - Идентификатор сервера находится в URL-адресе экземпляра Marketo, например `serverID.marketo.com`.
    - Получите идентификатор Munchkin вашей подписки, перейдя в меню **Администратор** > **Munchkin** в поле **идентификатор учетной записи Munchkin** или из первой части поддомена Marketo REST API узла: `https://{Munchkin ID}.mktorest.com`.
    - Идентификатор формы — это идентификатор формы кода внедрения, созданной на шаге 7 для направления интересов из Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Настройка предложения для отправки интересов в Marketo

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия. 

1. Войдите в [Центр партнеров](https://partner.microsoft.com/dashboard/home).

1. Выберите свое предложение и перейдите на вкладку **Настройка предложения** .

1. В разделе **управление интересами** выберите **подключить**. 

    ![Кнопка подключения раздела управления интересами](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Во всплывающем окне **сведения о подключении** выберите **Marketo** в качестве **назначения интереса**.

    ![Выбор назначения интереса](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Укажите **идентификатор сервера**, **идентификатор учетной записи Munchkin**и **идентификатор формы**.

    > [!NOTE]
    > Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения. 

1. В разделе **Контактная электронная почта**введите адреса электронной почты сотрудников Организации, которые должны получать уведомления по электронной почте при получении нового интереса. Вы можете указать несколько адресов электронной почты, разделив их точкой с запятой.

1. Щелкните **ОК**.

   Чтобы убедиться, что вы успешно подключились к цели назначения, выберите **проверить**. В случае успеха вы получите руководителя тестирования в целевом месте назначения.

   ![Всплывающее окно "сведения о подключении"](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
