---
title: Настройка управления интересами для Salesforce | Azure Marketplace
description: Настройка управления интересами в Salesforce для клиентов Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812159"
---
# <a name="configure-lead-management-for-salesforce"></a>Настройка управления интересами для Salesforce

В этой статье описывается, как настроить систему salesforce для обработки интересов продаж из предложения Marketplace.

## <a name="set-up-your-salesforce-system"></a>Настройка системы Salesforce

1. Войдите в Salesforce.
2. Если вы используете интерфейс освещения Salesforce.
    1. Выберите **Настройка** на домашней странице Salesforce.
    ![Установка Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. На странице Программа установки перейдите к разделу средства "Навигация" в левой части окна " **инструменты платформы" — > параметры компонентов — > Marketing — > "Интернет-к-интерес**".
    ![SalesForce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. При использовании классического интерфейса SalesForce:
    1. Выберите **Настройка** на домашней странице Salesforce.
    ![классической установки Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. На странице Настройка перейдите по левой панели навигации, чтобы **построить-> customize-> интересы — > веб-to-Lead**.
    ![классическая веб-](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Остальные инструкции одинаковы, независимо от используемого интерфейса SalesForce.

4. На **странице Настройка с веб-сайта**нажмите кнопку **создать форму Web-to-Lead** .
5. На **установке Web-to-Lead** выберите **Создать форму Web-to-Lead**.
    ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png) установки ![Salesforce-Web-to-Lead

6. В **форме создание веб-формы**убедитесь, что `the Include reCAPTCHA in HTML` параметр снят, и нажмите кнопку **создать**. 
    ![Salesforce — создание формы с Интернет-руководителем](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Появится текст HTML. Найдите текст "OID" и скопируйте **значение OID** из текста HTML (только текст между кавычками) и сохраните его. Это значение будет вставлено в поле **идентификатор организации** на портале публикации.
    ![Salesforce — создание формы с Интернет-руководителем](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Выбранные **Завершенные**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Настройка предложения для отправки интересов в Salesforce

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
1. Выберите **Подключиться** в разделе Управление интересами.
    ![управление интересами. Подключение](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Во всплывающем окне Connection Details (сведения о подключении) выберите **Salesforce** для **целевого адресата** и вставьте `oid` из формы Web to-Lead, созданной на предыдущем шаге, в поле **идентификатор организации** .

1. Щелкните **Сохранить**. 

    >[!Note]
    >Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.

    ![Сведения о подключении. Выбор назначения интереса](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Сведения о подключении. Выбор назначения интереса](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)