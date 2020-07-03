---
title: Управление интересами с помощью хранилища BLOB-объектов Azure — коммерческий рынок Майкрософт
description: Узнайте, как использовать большой двоичный объект Azure для настройки интересов для Microsoft AppSource и Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 076edc62a467701eaf0de23f280cdaf2abd945de
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792723"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Использование хранилища BLOB-объектов Azure для управления интересами для коммерческого рынка

>[!Caution]
>Поддержка коммерческого рынка для хранилища BLOB-объектов Azure устарела и больше не является возможностью обработки интересов из вашего предложения. Если у вас в настоящее время есть коммерческое предложение Marketplace с управлением интересами для большого двоичного объекта Azure, вы больше не будете отправлять клиентские интересы. Обновите конфигурацию управления интересами, дойдите любые другие варианты управления интересами. Узнайте о других возможностях на главной [странице управления интересами](./commercial-marketplace-get-customer-leads.md)».

 Если система управления отношениями с клиентами (CRM) не поддерживается в центре партнеров для получения Microsoft AppSource и интересов Azure Marketplace, можно использовать хранилище BLOB-объектов Azure. Затем можно выбрать экспорт данных и их импорт в систему CRM. Инструкции в этой статье помогут вам создать учетную запись хранения Azure и большой двоичный объект под этой учетной записью. Кроме того, вы можете создать новый поток с помощью Power автоматизировать, чтобы отправить уведомление по электронной почте, когда ваше предложение получит интерес.

>[!NOTE]
>Соединитель Power автоматизиру, используемый в этих инструкциях, требует платной подписки на Power автоматизиру. Перед выполнением инструкций, приведенных в этой статье, убедитесь, что учетная запись выполняется.

## <a name="configure-azure-blob-storage"></a>Настройка хранилища BLOB-объектов Azure

1. Если у вас нет учетной записи Azure, вы можете [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).

2. Когда учетная запись Azure будет активна, войдите в [портал Azure](https://portal.azure.com).

3. В портал Azure создайте учетную запись хранения, выполнив следующую процедуру.  
    1. Выберите **+ создать ресурс** в левой строке меню.  Справа появится **Новая** панель (колонка).
    2. В области **создать** выберите **хранилище** .  Справа отображается список с **описанием** .
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

9. Установите уровень общего доступа к контейнеру как **частный (без анонимного доступа)**.

10. Нажмите кнопку **ОК**, чтобы создать контейнер.

    ![Создать контейнер](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Настройка предложения для отправки интересов в хранилище BLOB-объектов Azure

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключиться** в разделе Управление интересами.

    ![Подключить предложение](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Во всплывающем окне сведения о подключении выберите **большой двоичный объект Azure** для назначения интереса.

    ![Сведения о подключении](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Укажите **имя контейнера** и **строку подключения к учетной записи хранения** , которые вы получили, следуя этим инструкциям.

    * Пример имени контейнера:`marketplaceleadcontainer`
    * Пример строки подключения учетной записи `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![хранения: сведения о подключении](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Щелкните **Сохранить**.

    > [!NOTE]
    > Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.


