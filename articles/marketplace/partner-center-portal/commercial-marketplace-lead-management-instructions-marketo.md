---
title: Настройка управления интересами в Marketo | Azure Marketplace
description: Настройка управления интересами для Marketo для клиентов Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133659"
---
# <a name="configure-lead-management-in-marketo"></a>Настройка управления интересами в Marketo

В этой статье описывается, как настроить систему Marketo CRM для обработки интересов продаж из коммерческого предложения Marketplace.

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

1. Перейдите на страницу **настройки предложения** для вашего предложения.

1. Выберите **Подключиться** в разделе **управление интересами** . 

    ![Кнопка подключения раздела управления интересами](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Во всплывающем окне **сведения о подключении** выберите **Marketo** в качестве **назначения интереса**.

    ![Выбор назначения интереса](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Укажите **идентификатор сервера**, **идентификатор учетной записи Munchkin**и **идентификатор формы**.

    > [!NOTE]
    > Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения. 

1. В разделе **Контактная электронная почта**введите адреса электронной почты сотрудников Организации, которые должны получать уведомления по электронной почте при получении нового интереса. Вы можете указать несколько адресов электронной почты, разделив их точкой с запятой.

1. Нажмите кнопку **OK**.

   Чтобы убедиться, что вы успешно подключились к цели назначения, нажмите кнопку **проверить** . В случае успеха вы получите руководителя тестирования в целевом месте назначения.

   ![Всплывающее окно "сведения о подключении"](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
