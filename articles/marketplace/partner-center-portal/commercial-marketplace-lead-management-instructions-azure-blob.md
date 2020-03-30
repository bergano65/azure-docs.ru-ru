---
title: Таблица Azure в программе коммерческого рынка Лазурный рынок
description: Настройка руководства для Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285254"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Инструкции по управлению руководителями для Azure Blob

>[!Caution]
>Вариант Azure Blob для обработки интересов из предложения на рынке был универслен. Если в настоящее время есть предложение, опубликованное с конфигурацией управления свинцом для Azure Blob, вы больше не получаете интересы клиентов. Пожалуйста, обновите конфигурацию управления свинцом до любого из других вариантов управления свинцом. Узнайте о других вариантах на [целевой странице руководства](./commercial-marketplace-get-customer-leads.md)управления»...

Если система управления взаимоотношениями с клиентами (CRM) не поддерживается явно в Центре-партнере по приему интересов Azure Marketplace и AppSource, вы можете использовать Azure Blob для обработки этих интересов. Затем можно экспортировать данные и импортировать их в свою CRM-систему. Инструкции в этой статье дадут вам возможность создать учетную запись хранилища Azure и учетную запись Azure Blob под этой учетной записью. Кроме того, можно создать новый поток с помощью Microsoft Flow для отправки уведомления по электронной почте, когда ваше предложение получит зацепку.


## <a name="how-to-configure-azure-blob"></a>Как настроить Azure Blob

1. Если у вас нет учетной записи Azure, вы можете [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
1. После того, как ваша учетная запись Azure активна, вопийте на [порталAz.](https://portal.azure.com)
1. На портале Azure создайте учетную запись хранения с помощью следующей процедуры.  
    1. Выберите **ресурс «Создайте ресурс»** в левой панели меню.  **Новая** панель (лезвие) будет отображаться справа.
    2. Выберите **Хранилище** в **новой** панели.  **Рекомендуемый** список отображается справа.
    3. Выберите **учетную запись хранения** для начала создания учетной записи.  Следуйте инструкциям в статье [Создание учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Шаги по созданию учетной записи хранения Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Для получения более подробной информации о учетных записях хранения, выберите [учебник по быстрому запуску.](https://docs.microsoft.com/azure/storage/)  Дополнительные сведения о ценах на службу хранилища см. на странице [Обзор цен на хранилище Azure](https://azure.microsoft.com/pricing/details/storage/).

4. Подождите, пока будет подготовлена учетная запись хранилища, процесс, который обычно занимает несколько минут.  Затем получите доступ к учетной записи хранилища с **главной** страницы портала Azure, выбрав **все ресурсы** или выбрав **все ресурсы** из левой панели меню навигации портала Azure.

    ![Доступ к учетной записи хранения Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Из панели учетной записи хранилища выберите **ключи доступа** и скопируйте значение *строки Connection* для ключа. Сохраните это значение, так как это значение *строки подключения к учетной записи* хранилища, которое необходимо предоставить на портале публикации для получения интересов для вашего предложения на рынке.

     Примером укуса соединения является:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Ключ к хранилищу Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. На странице учетной записи хранилища выберите **Blobs.**

   ![Ключ к хранилищу Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. После того, как на странице капли, выберите кнопку **контейнера.**

8. Введите **имя** для нового контейнера. Все знаки в имени контейнера должны быть строчными. Оно должно начинаться с буквы или цифры и может содержать только буквы, цифры и дефисы (-). Для получения дополнительной информации об именах контейнеров и каблов [см.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

    Сохраните это значение, так как это значение *названия контейнера,* которое необходимо предоставить на портале публикации для получения интересов для вашего предложения на рынке.

9. Установите уровень публичного доступа к контейнеру как **частный (без анонимного доступа).**

10. Нажмите кнопку **ОК**, чтобы создать контейнер.

    ![Новый контейнер](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Напередите предложение об отправке лидов в Azure Blob

Когда вы будете готовы настроить информацию о руководстве для вашего предложения на портале публикации, выполните ниже:

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключение** в разделе «Управление ведущим иссяка».

    ![Подключить предложение](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. На всплывающем окне подключения выберите **Azure Blob** для ведущего назначения.

    ![Подробная информация о подключении](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Предоставьте **строку подключения** к учетным записям контейнера и **учетной записи хранилища,** полученную от выполнения этих инструкций.

    * Пример названия контейнера:`marketplaceleadcontainer`
    * Пример строки подключения `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![к учетной записи хранилища: Деталь подключения](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Нажмите кнопку **Сохранить**.

    > [!NOTE]
    > Вы должны закончить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить приводит для предложения.


