---
title: Приступая к работе с Обозревателем службы хранилища | Документация Майкрософт
description: Управление ресурсами службы хранилища Azure с помощью Обозревателя службы хранилища
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279797"
---
# <a name="get-started-with-storage-explorer"></a>Начало работы с Обозревателем службы хранилища

## <a name="overview"></a>Обзор

Microsoft Azure Storage Explorer — это автономное приложение, которое упрощает работу с данными хранения данных Azure Storage на Windows, macOS и Linux. В этой статье вы узнаете несколько способов подключения к учетным записям данных хранения Azure и управления ими.

![Обозреватель службы хранилища Microsoft Azure][0]

## <a name="prerequisites"></a>Предварительные требования

# <a name="windows"></a>[Windows](#tab/windows)

Следующие версии службы поддержки Windows Storage Explorer:

* Windows 10 (рекомендуется).
* Windows 8
* Windows 7

Для всех версий Windows, Storage Explorer требует .NET Framework 4.6.2 или позже.

# <a name="macos"></a>[macOS](#tab/macos)

Следующие версии macOS поддержки Storage Explorer:

* macOS 10.12 Сьерра и более поздние версии

# <a name="linux"></a>[Linux](#tab/linux)

Исследователь хранения данных доступен в [Snap Store](https://snapcraft.io/storage-explorer) для наиболее распространенных дистрибутивов Linux. Мы рекомендуем Snap Store для этой установки. Снимок Storage Explorer устанавливает все свои зависимости и обновления при публикации новых версий в Snap Store.

Для поддерживаемых дистрибутивов см. [snapd installation page](https://snapcraft.io/docs/installing-snapd)

Исследователь хранения требует использования менеджера паролей. Возможно, вам придется подключиться к менеджеру паролей вручную. Вы можете подключить Storage Explorer к менеджеру паролей вашей системы, запустив следующую команду:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer также доступен в виде загрузки *.tar.gz.* Вы должны установить зависимости вручную. Следующие дистрибутивы Linux поддерживают *установку .tar.gz:*

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64.

Установка *.tar.gz* может работать на других дистрибутивах, но только те перечисленные официально поддерживаются.

Для получения дополнительной помощи в [Linux dependencies](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) установке Explorer для хранения данных на Linux см.

---

## <a name="download-and-install"></a>Загрузите и установите

Чтобы загрузить и установить [Azure Storage Explorer](https://www.storageexplorer.com)исследователь хранения, см.

## <a name="connect-to-a-storage-account-or-service"></a>Подключение к учетной записи хранения или к службе

Обозреватель службы хранилища предоставляет несколько способов подключения к учетным записям хранения. В общем, вы можете либо:

* [Войти в Azure для доступа к подпискам и их ресурсам](#sign-in-to-azure)
* [Прикрепите конкретный ресурс хранения или ресурс CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Вход в Azure

> [!NOTE]
> Для полного доступа к ресурсам после вступления в систему Data Explorer требуется как управление (менеджер ресурсов Azure), так и разрешения на уровне данных. Это означает, что вам нужны разрешения Active Directory (Azure AD), которые дают вам доступ к учетной записи хранилища, контейнерам в учетной записи и данным в контейнерах. Если у вас есть разрешения только на уровне данных, подумайте о [добавлении ресурса через Azure AD.](#add-a-resource-via-azure-ad) Для получения дополнительной информации о конкретных [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)разрешениях, необходимых для хранения, см.

1. В группе Storage Explorer выберите**управление учетной** **записью** > или кнопку **«Управление учетными записями».**

    ![Управление учетными записями][1]

1. **ACCOUNT MANAGEMENT** теперь отображает все учетные записи Azure, в которые вы подписались. Чтобы подключиться к другой учетной записи, **выберите Добавить учетную запись.**

1. В **систему подключения к хранилищам Azure**выберите облако Azure из **среды Azure** для вхотливки в национальное облако или стек Azure. После выбора среды выберите **следующий**.

    ![Возможность вхуза][2]

    Storage Explorer открывает страницу для входной двери. Для получения дополнительной информации [см.](/azure-stack/user/azure-stack-storage-connect-se)

1. После успешного вхлечения в учетную запись Azure учетная запись и подписки Azure, связанные с этой учетной записью, отображаются в **ACCOUNT MANAGEMENT.** Выберите **все подписки,** чтобы переключить свой выбор между всеми или ни одной из перечисленных подписок Azure. Выберите подписки Azure, с которыми вы собираетесь работать, а затем щелкните **Применить**.

    ![Выбор подписок Azure][3]

    **EXPLORER** отображает учетные записи хранения, связанные с выбранными подписками Azure.

    ![Выбранные подписки Azure][4]

### <a name="attach-a-specific-resource"></a>Прикрепить определенный ресурс

Существует несколько способов присоединения к ресурсу в Storage Explorer:

* [Добавить ресурс через Azure AD](#add-a-resource-via-azure-ad). Если у вас есть разрешения только на уровне данных, используйте эту опцию, чтобы добавить контейнер с каплями или контейнер для хранения данных Azure Data Lake Gen2 Blob.
* [Используйте строку соединения.](#use-a-connection-string) Используйте эту опцию, если у вас есть строка подключения к учетной записи хранилища. Storage Explorer поддерживает как ключевые, так и общие строки подключения [к подписи доступа.](storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Используйте общую подпись доступа URI.](#use-a-shared-access-signature-uri) Если у вас есть [общая подпись доступа URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) к контейнеру с каплями, общим файлом, очереди или таблице, используйте ее для присоединения к ресурсу. Чтобы получить общую подпись доступа URI, можно использовать [explorer хранения](#generate-a-sas-in-storage-explorer) или [портал Azure.](https://portal.azure.com)
* [Используйте имя и ключ.](#use-a-name-and-key) Если вы знаете какой-либо из ключей учетной записи к вашей учетной записи, вы можете использовать эту опцию для быстрого подключения. Найдите ключи на странице учетной записи хранилища, выбрав**ключи доступа** **к настройкам** > на [портале Azure.](https://portal.azure.com)
* [Прикрепите к местному эмулятору.](#attach-to-a-local-emulator) Если вы используете один из доступных эмуляторов хранения Azure, используйте эту опцию, чтобы легко подключиться к эмулятору.
* [Подключитесь к учетной записи Azure Cosmos DB с помощью строки подключения.](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string) Используйте эту опцию, если у вас есть строка подключения к экземпляру CosmosDB.
* [Подключитесь к магазину Azure Data Lake Store по URI](#connect-to-azure-data-lake-store-by-uri). Используйте эту опцию, если у вас есть URI в магазине azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Добавление ресурса через Azure AD

1. Выберите символ **подключения,** чтобы открыть **Подключение к лазурному хранилище.**

    ![Варианты подключения к службе хранилища Azure][9]

1. Если вы еще этого не сделали, используйте **опцию «Добавить учетную запись Azure»,** чтобы войти в учетную запись Azure, которая имеет доступ к ресурсу. После вху-на-е место вернитесь **в систему Connect to Azure Storage.**

1. Выберите **Добавить ресурс через Активный каталог Azure (Azure AD),** а затем выберите **Следующий.**

1. Выберите учетную запись Azure и арендатора. Эти значения должны иметь доступ к ресурсу хранения, к который вы хотите прикрепить. Нажмите кнопку **Далее**.

1. Выберите тип ресурса, который вы хотите прикрепить. Введите информацию, необходимую для подключения. 

   Информация, вводимых на этой странице, зависит от типа добавления ресурса. Убедитесь в том, чтобы выбрать правильный тип ресурса. После ввода необходимой информации выберите **Следующий**.

1. Просмотрите **Резюме подключения,** чтобы убедиться, что вся информация верна. Если это так, выберите **Connect**. В противном случае выберите **Вернуться на** предыдущие страницы, чтобы исправить любую неверную информацию.

После успешного добавления соединения дерево ресурсов переходит в узла, представляющего соединение. Ресурс отображается под **локальными & присоединенные** > **учетные записи** > хранения **(присоединенные контейнеры)** > **Blob Контейнеры**. Если Storage Explorer не смог добавить соединение или не смог получить доступ к [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)данным после успешного добавления соединения, см.

#### <a name="use-a-connection-string"></a>Используйте строку соединения

1. Выберите символ **подключения,** чтобы открыть **Подключение к лазурному хранилище.**

    ![Варианты подключения к службе хранилища Azure][9]

1. Выберите **Используйте строку соединения,** а затем выберите **Следующую.**

1. Выберите имя дисплея для подключения и введите строку соединения. Затем выберите **Следующий**.

1. Просмотрите **Резюме подключения,** чтобы убедиться, что вся информация верна. Если это так, выберите **Connect**. В противном случае выберите **Вернуться на** предыдущие страницы, чтобы исправить любую неверную информацию.

После успешного добавления соединения дерево ресурсов переходит в узла, представляющего соединение. Ресурс отображается под **локальными & присоединенными** > **учетными записями хранения.** Если Storage Explorer не смог добавить соединение или не смог получить доступ к [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)данным после успешного добавления соединения, см.

#### <a name="use-a-shared-access-signature-uri"></a>Используйте общую подпись доступа URI

1. Выберите символ **подключения,** чтобы открыть **Подключение к лазурному хранилище.**

    ![Варианты подключения к службе хранилища Azure][9]

1. Выберите **Используйте общую подпись доступа (SAS) URI,** а затем выберите **Следующий.**

1. Выберите имя дисплея для подключения и введите общую подпись доступа URI. Конечная точка службы для типа ресурса, который вы прикрепляете, должна быть автозаполнена. Если вы используете пользовательскую конечную точку, возможно, что это не так. Нажмите кнопку **Далее**.

1. Просмотрите **Резюме подключения,** чтобы убедиться, что вся информация верна. Если это так, выберите **Connect**. В противном случае выберите **Вернуться на** предыдущие страницы, чтобы исправить любую неверную информацию.

После успешного добавления соединения дерево ресурсов переходит в узла, представляющего соединение. Ресурс отображается в **локальном & присоединенных** > **учетных записей** > **хранения (Присоединенных контейнерах)** > *сервисного узла для присоединенного контейнера.* Если Исследователь хранения не смог добавить [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)соединение, см. Просмотрите руководство по устранению неполадок, если вы не можете получить доступ к данным после успешного добавления соединения.

#### <a name="use-a-name-and-key"></a>Используйте имя и ключ

1. Выберите символ **подключения,** чтобы открыть **Подключение к лазурному хранилище.**

    ![Варианты подключения к службе хранилища Azure][9]

1. Выберите **Используйте имя учетной записи и ключ для хранения,** а затем выберите **Следующий.**

1. Выберите имя дисплея для подключения.

1. Введите имя учетной записи хранилища и любой из ключей доступа.

1. Выберите **домен хранения** для использования, а затем выберите **Следующий.**

1. Просмотрите **Резюме подключения,** чтобы убедиться, что вся информация верна. Если это так, выберите **Connect**. В противном случае выберите **Вернуться на** предыдущие страницы, чтобы исправить любую неверную информацию.

После успешного добавления соединения дерево ресурсов переходит в узла, представляющего соединение. Ресурс отображается под **локальными & присоединенными** > **учетными записями хранения.** Если Storage Explorer не смог добавить соединение или не смог получить доступ к [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)данным после успешного добавления соединения, см.

#### <a name="attach-to-a-local-emulator"></a>Прикрепите к местному эмулятору

Storage Explorer в настоящее время поддерживает два официальных эмулятора хранения:

* [Эмулятор хранения azure](storage/common/storage-use-emulator.md) (только Windows)
* [Азурит](https://github.com/azure/azurite) (Windows, macOS или Linux)

Если эмулятор прослушивает порты по умолчанию, вы можете использовать узла **Emulator - Значения портов по умолчанию** для доступа к эмулятору. Ищите **эмулятор - Порты по умолчанию** под **локальной & присоединенные** > **учетные записи хранения.**

Если вы хотите использовать другое имя для подключения, или если эмулятор не работает в портах по умолчанию, выполните следующие действия:

1. Запустите эмулятор. Введите `AzureStorageEmulator.exe status` команду для отображения портов для каждого типа обслуживания.

   > [!IMPORTANT]
   > Storage Explorer автоматически не запускает эмулятор. Вы должны начать его вручную.

1. Выберите символ **подключения,** чтобы открыть **Подключение к лазурному хранилище.**

    ![Варианты подключения к службе хранилища Azure][9]

1. Выберите **Прикрепите к локальному эмулятору,** а затем выберите **Следующий.**

1. Выберите имя дисплея для подключения и введите порты, которые ваш эмулятор слушает для каждого типа службы. **Прикрепите к локальному эмулятору,** предлагает значения порта по умолчанию для большинства эмуляторов. **Порт файлов** пуст, так как ни один из официальных эмуляторов в настоящее время не поддерживает службу Файлов. Если эмулятор, который вы используете, поддерживает файлы, вы можете войти в порт для использования. Затем выберите **Следующий**.

1. Просмотрите **Резюме подключения** и убедитесь, что вся информация верна. Если это так, выберите **Connect**. В противном случае выберите **Вернуться на** предыдущие страницы, чтобы исправить любую неверную информацию.

После успешного добавления соединения дерево ресурсов переходит в узла, представляющего соединение. Узла должна отображаться под **локальными & присоединенными** > **учетными записями хранения.** Если Storage Explorer не смог добавить соединение или не смог получить доступ к [Azure Storage Explorer troubleshooting guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)данным после успешного добавления соединения, см.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Подключение к учетной записи Azure Cosmos DB с помощью строки подключения

Вместо того, чтобы управлять dB-аккаунтами Azure Cosmos через подписку Azure, можно подключиться к DB Azure Cosmos с помощью строки подключения. Чтобы подключиться, выполните следующие действия.

1. Под **EXPLORER**, расширить **локальные & прилагается**, право йгняйте **Космоус DB счета**, и выберите Подключение к **Azure Космос DB**.

    ![Подключение к Azure Cosmos DB с помощью строки подключения][21]

1. Выберите API DB Azure Cosmos, введите данные **строки подключения,** а затем выберите **OK** для подключения учетной записи Azure Cosmos DB. Для получения информации о том, как получить строку соединения, [см.](https://docs.microsoft.com/azure/cosmos-db/manage-account)

    ![Строка подключения][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Подключение Azure Data Lake Store по URI

Вы можете получить доступ к ресурсу, которого нет в подписке. Вам нужен кто-то, кто имеет доступ к этому ресурсу, чтобы дать вам ресурс URI. После вступления в систему подключитесь к Магазину Data Lake Store с помощью URI. Чтобы подключиться, выполните следующие действия.

1. Под **EXPLORER,** расширить **местные & прилагается**.

1. Нажмите правой кнопкой мыши **Data Lake Storage Gen1**, и выберите **Подключитесь к хранению данных озера Gen1**.

    ![Подключение к контекст-меню хранилища data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Введите URI, а затем выберите **OK**. Ваш магазин озер данных отображается под **хранением данных lake.**

    ![Подключение к результату хранилища озер данных](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

В этом примере используется Data Lake Storage Gen1. Azure Data Lake Storage Gen2 теперь доступен. Для получения дополнительной информации смотрите [что такое Хранилище озер данных Azure Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Создание общей подписи доступа в Хранилище Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Общий уровень учетной записи с подписью доступа

1. Нажмите правой кнопкой мыши учетной записи хранения вы хотите поделиться, а затем выберите **Получить общий доступ Подпись**.

    ![Получите опцию меню контекста общей подписи доступа][14]

1. В **общей подписи доступа**укажите временные рамки и разрешения, которые вы хотите для учетной записи, а затем выберите **Создать.**

    ![Получить общую подпись доступа][15]

1. Копируйте строку **Connection** или необработанную **строку Запроса** на буфер обмена.

### <a name="service-level-shared-access-signature"></a>Общий уровень обслуживания с подписью доступа

Вы можете получить общую подпись доступа на уровне сервиса. Для получения дополнительной [информации, см Получить SAS для капли контейнера](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Поиск учетных записей хранилищ

Чтобы найти ресурс для хранения, можно найти в панели **EXPLORER.**

При вводе текста в поле поиска исследователь хранилища отображает все ресурсы, которые соответствуют значению поиска, вкоторую введенное до этого момента. Этот пример показывает поиск **конечных точек:**

![Поиск учетной записи хранения][23]

> [!NOTE]
> Чтобы ускорить поиск, используйте **управление учетной записью,** чтобы отменить все подписки, которые не содержат элемент, который вы ищете. Вы также можете нажать на узла и выбрать **поиск отсюда,** чтобы начать поиск из определенного узла.
>
>

## <a name="next-steps"></a>Дальнейшие действия

* [Управление ресурсами хранения Azure Blob с помощью исследователя хранения](vs-azure-tools-storage-explorer-blobs.md)
* [Работа с данными с помощью Обозревателя службы хранилища Azure](./cosmos-db/storage-explorer.md)
* [Управление ресурсами Azure Data Lake Store с помощью Обозревателя службы хранилища](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
