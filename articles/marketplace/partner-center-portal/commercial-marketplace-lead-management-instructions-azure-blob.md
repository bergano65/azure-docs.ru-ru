---
title: Таблица Azure в программе коммерческого рынка | Azure Marketplace
description: Настройка управления интересами для большого двоичного объекта Azure
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902368"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Инструкции по управлению интересами для большого двоичного объекта Azure

>[!Caution]
>Параметр большого двоичного объекта Azure для обработки интересов из вашего предложения Marketplace является устаревшим. Если в настоящее время имеется предложение, опубликованное с помощью настройки управления интересами для большого двоичного объекта Azure, вы больше не получаете потенциальных клиентов. Обновите конфигурацию управления интересами, дойдите любые другие варианты управления интересами. Узнайте о других возможностях на главной [странице управления интересами](./commercial-marketplace-get-customer-leads.md)».

Если система управления отношениями с клиентами (CRM) не поддерживается в центре партнеров для получения Azure Marketplace и AppSource интересов, вы можете использовать большой двоичный объект Azure для обработки этих интересов. Затем можно выбрать экспорт данных и их импорт в систему CRM. Инструкции в этой статье помогут вам создать учетную запись хранения Azure и большой двоичный объект Azure под этой учетной записью. Кроме того, можно создать новый поток с помощью Microsoft Flow, чтобы отправить уведомление по электронной почте, когда ваше предложение получит интерес.


## <a name="how-to-configure-azure-blob"></a>Как настроить большой двоичный объект Azure

1. Если у вас нет учетной записи Azure, вы можете [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
1. Когда учетная запись Azure будет активна, войдите в [портал Azure](https://portal.azure.com).
1. В портал Azure создайте учетную запись хранения, выполнив следующую процедуру.  
    1. Выберите **+ создать ресурс** в левой строке меню.  Справа появится **Новая** панель (колонка).
    2. В области **создать** выберите **хранилище** .  Справа отображается список с описанием.
    3. Выберите **учетную запись хранения** , чтобы начать создание учетной записи.  Следуйте инструкциям в статье [Создание учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Шаги по созданию учетной записи хранения Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Для получения дополнительных сведений об учетных записях хранения выберите [Краткое руководство](https://docs.microsoft.com/azure/storage/).  Дополнительные сведения о ценах на службу хранилища см. на странице [Обзор цен на хранилище Azure](https://azure.microsoft.com/pricing/details/storage/).

4. Подождите, пока ваша учетная запись хранения не будет подготовлена, процесс, который обычно занимает несколько минут.  Затем откройте учетную запись хранения на **домашней** странице портал Azure, выбрав **Просмотреть все ресурсы** или выбрав **все ресурсы** в строке навигации слева от портал Azure.

    ![Доступ к учетной записи хранения Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. В области Учетная запись хранения выберите **ключи доступа** и скопируйте значение *строки подключения* для ключа. Сохраните это значение, так как это значение *строки подключения к учетной записи хранения* , которое потребуется предоставить на портале публикации, чтобы получить интересы для вашего предложения Marketplace.

     Пример соединения проверочного:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Ключ к хранилищу Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. На странице учетной записи хранения выберите **BLOB-объекты**.

   ![Ключ к хранилищу Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. На странице большие двоичные объекты нажмите кнопку **+ Container (+ контейнер** ).

8. Введите **имя** нового контейнера. Все знаки в имени контейнера должны быть строчными. Оно должно начинаться с буквы или цифры и может содержать только буквы, цифры и дефисы (-). Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

    Сохраните это значение, так как это значение *имени контейнера* , которое необходимо указать на портале публикации для получения интересов для вашего предложения Marketplace.

9. Установите уровень общего доступа к контейнеру как **частный (без анонимного доступа)** .

10. Нажмите кнопку **ОК**, чтобы создать контейнер.

    ![Создать контейнер](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Настройка предложения для отправки интересов в большой двоичный объект Azure

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключиться** в разделе Управление интересами.

    ![Подключить предложение](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Во всплывающем окне сведения о подключении выберите **большой двоичный объект Azure** для назначения интереса.

    ![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Укажите **имя контейнера** и **строку подключения к учетной записи хранения** , которые вы получили, следуя этим инструкциям.

    * Пример имени контейнера:`marketplaceleadcontainer`
    * Пример строки подключения учетной записи хранения: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Щелкните **Сохранить**.

    > [!NOTE]
    > Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.


