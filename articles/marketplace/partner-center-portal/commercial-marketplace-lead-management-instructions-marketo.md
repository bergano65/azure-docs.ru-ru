---
title: Настройка управления интересами в Marketo | Azure Marketplace
description: Настройка управления интересами для Marketo для клиентов Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812142"
---
# <a name="configure-lead-management-in-marketo"></a>Настройка управления интересами в Marketo

В этой статье описывается, как настроить систему Marketo CRM для обработки интересов продаж из предложения Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Настройка системы Marketo CRM

1. Войдите в Marketo.
2. Выберите **Разработка студии**.
    ![Разработка студии Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Выберите **Новая форма**.
    ![Новая форма Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Заполните необходимые поля в новой форме, а затем выберите **Создать**.
    ![Создание новой формы Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  В сведениях о поле, выберите **Готово**.
    ![Завершение формы Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Утвердите и закройте окно.

7. На вкладке *маркетплацелеадбаккед* выберите **код внедрения**. 

    ![Код внедрения](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Код внедрения Marketo отображает код, аналогичный приведенному ниже.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Скопируйте значения для следующих полей, показанных в форме кода внедрения. Эти значения будут использоваться для настройки предложения на получение интересов на следующем шаге. Используйте следующий пример в качестве руководства для получения необходимых идентификаторов из примера кода Marketo.

    - Идентификатор сервера = **ys12**
    - Идентификатор Munchkin = **123-PQR-789**
    - Идентификатор формы = **1179**

    **Другой способ определить эти значения**

    - Идентификатор сервера находится в URL-адресе экземпляра Marketo, например "`serverID.marketo.com`".
    - Получите идентификатор Мунчинг вашей подписки, перейдя в меню Администратор > Munchkin в поле Идентификатор учетной записи Munchkin или из первой части дочернего домена Marketo REST API: `https://{Munchkin ID}.mktorest.com`.
    - Идентификатор формы — это идентификатор формы кода внедрения, созданной на шаге 7 для направления интересов из нашего магазина.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Настройка предложения для отправки интересов в Marketo

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия. 

1. Перейдите на страницу **настройки предложения** для вашего предложения.
1. Выберите **Подключиться** в разделе Управление интересами. 

    ![Управление интересами — подключение](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Во всплывающем окне сведения о подключении выберите **Marketo** в качестве назначения интереса.

    ![Выбор назначения интереса](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Укажите **идентификатор сервера**, **идентификатор учетной записи Мунчинг**и **идентификатор формы**.

    >[!Note]
    >Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения. 

