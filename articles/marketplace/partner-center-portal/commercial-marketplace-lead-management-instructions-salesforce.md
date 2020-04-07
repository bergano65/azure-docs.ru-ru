---
title: Настройка руководства управления для Salesforce (ru) Лазурный рынок
description: Налажить руководство в отделе продаж для клиентов Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 9b59181f7c1b24a7bc00ab579bbe67a70f499c92
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755378"
---
# <a name="configure-lead-management-for-salesforce"></a>Настройка управления интересами для Salesforce

В этой статье описывается, как настроить систему Salesforce для обработки продаж приводит от вашего коммерческого предложения рынке.

> [!Note]
> Marketplace не поддерживает предварительно заполненные списки, такие как список значений для поля **страны.** Убедитесь, что перед продолжением списков нет. Кроме того, можно настроить [конечную точку HTTPS](./commercial-marketplace-lead-management-instructions-https.md) или [таблицу Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) для получения удовбытостей.

## <a name="set-up-your-salesforce-system"></a>Настройка системы Salesforce

1. Войдите в Salesforce.
2. Если вы используете опыт освещения Salesforce.
    1. Выберите **настройку** на главной странице Salesforce.
    ![Установка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Со страницы настройки перейдите через левую навигацию на **платформу Инструменты >настройки функции >Маркетинг->Web-to-Lead**.

        ![SalesForce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Если вы используете опыт Salesforce Classic:
    1. Выберите **настройку** на главной странице Salesforce.
    ![Классическая установка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Со страницы настройки перейдите через левую навигацию в **Build->Настроить->ведет >Web-to-Lead.**

        ![Salesforce классический веб-к-ведущего](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Остальные инструкции одинаковы, независимо от того, какой опыт Salesforce вы используете.

4. На **странице настройки Web-to-Lead**выберите кнопку **«Создание веб-формы».**
5. На **установке Web-to-Lead** выберите **Создать форму Web-to-Lead**.
    ![Salesforce - Настройка веб-руководства](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. При **создании формы Web-to-Lead**убедитесь, что `the Include reCAPTCHA in HTML` настройка не проверена, и выберите **Generate.** 
    ![Salesforce - Создание веб-формы](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Вам будет представлен текст HTML. Поиск текста "oid" и скопировать **значение oid** из текста HTML (только текст между кавычками) и сохранить его. Это значение будет вклеено в поле **идентификатора Организации** на портале публикации.
    ![Salesforce - Создание веб-формы](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Выбранный **Законченный**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Напередите предложение отправить литые в Salesforce

Когда вы будете готовы настроить информацию о руководстве для вашего предложения на портале публикации, выполните ниже:

1. Перейдите на страницу **настройки предложения** для вашего предложения.
1. Выберите **Подключение** в разделе «Управление ведущим иссяка».
    ![Руководство - Подключение](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. На всплывающем окне подключения выберите **Salesforce** для **ведущего** назначения `oid` и вставьте в созданной из веб-формы, созданной вами, следуя более ранним шагам в поле **идентификатора Организации.**

1. Щелкните **Сохранить**. 

    >[!Note]
    >Вы должны закончить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить приводит для предложения.

    ![Детали подключения - Выберите место назначения](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Детали подключения - Выберите место назначения](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
