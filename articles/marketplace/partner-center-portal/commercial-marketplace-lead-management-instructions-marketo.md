---
title: Настройка руководства управления в Marketo (ru) Лазурный рынок
description: Направляй руководство для Marketo для клиентов рынка Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281516"
---
# <a name="configure-lead-management-in-marketo"></a>Настройка управления интересами в Marketo

В этой статье описывается, как настроить систему Marketo CRM для обработки продаж приводит от вашего предложения рынка.

## <a name="set-up-your-marketo-crm-system"></a>Настройка CRM-системы Marketo

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

7. На вкладке *MarketplaceLeadBacked* выберите **встраиваемый код.** 

    ![Код внедрения](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Код внедрения Marketo отображает код, аналогичный приведенному ниже.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Копируйте значения для нижеуказанных полей, показанных в форме кода встраиваемого кода. Вы будете использовать эти значения для настройки вашего предложения для получения интересов на следующем этапе. Используйте следующий пример в качестве руководства для получения необходимых идентификаторов из примера кода Marketo.

    - Идентификатор сервера **ys12**
    - Идентификатор Мунчкина **No 123-ПЗР-789**
    - Идентификатор формы **No 1179**

    **Еще один способ выяснить эти значения**

    - Идентификатор сервера находится в URL-адресе`serverID.marketo.com`вашего Marketo, например, ".
    - Получите идентификатор вашего подписки Munching, перейдя в меню Admin>Munchkin в поле "Munchkin Account `https://{Munchkin ID}.mktorest.com`ID" или из первой части вашего поддомена Marketo REST API: .
    - Идентификатор формы — это идентификатор формы кода встраиваемого кода, созданного на шаге 7 для маршрутного направления с нашего рынка.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Налажить ваше предложение отправить приводит к Marketo

Когда вы будете готовы настроить информацию о руководстве для вашего предложения на портале публикации, выполните ниже: 

1. Перейдите на страницу **настройки предложения** для вашего предложения.
1. Выберите **Подключение** в разделе «Управление ведущим иссяка». 

    ![Руководство - Подключение](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. На всплывающем окне подключения выберите **Marketo** для ведущего назначения.

    ![Выберите направление для руководства](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Предоставьте **идентификатор сервера,** **идентификатор учетной записи**и **идентификатор формы.**

    >[!Note]
    >Вы должны закончить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить приводит для предложения. 

