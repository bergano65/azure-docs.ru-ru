---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c664e73b39ad48a860661cfd9141ee74df203f3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116653"
---
## <a name="generate-the-certificate-signing-request-file"></a>Создание файла запроса на подписывание сертификата

Служба push-уведомлений Apple (APNs) использует сертификаты для аутентификации push-уведомлений. Следуйте приведенным указаниям, чтобы создать сертификата push-уведомлений, необходимый для отправки и получения уведомлений. Дополнительные сведения об этих понятиях см. в официальной документации по [Службе push-уведомлений Apple](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Создайте файл запроса на подписывание сертификата (CSR), используемый Apple для создания подписанного сертификата push-уведомлений.

1. На компьютере Mac запустите средство Keychain Access. Его можно запустить из папки **Служебные программы** или **Другое** на панели запуска.

1. Нажмите **Связка ключей**, разверните **Помощник по сертификатам**, а затем выберите **Запросить сертификат в центре сертификации**.

    ![Использование Keychain Access для запрашивания нового сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Заполните поля **Адрес электронной почты пользователя** и **Личное имя**, выберите **Сохранено на диск**, а затем — **Продолжить**. Оставьте поле **Адрес электронной почты ЦС** пустым, так как оно является необязательным.

    ![Требуемые сведения о сертификате](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Задайте имя CSR-файлу в поле **Сохранить как**, выберите расположение в поле **Где** и нажмите кнопку **Сохранить**.

    ![Выбор имени файла для сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    При этом CSR-файл сохраняется в выбранном месте. Расположением по умолчанию является **рабочий стол**. Запомните расположение, выбранное для файла.

Теперь вам нужно зарегистрировать приложение в Apple, включить push-уведомления и передать экспортированный CSR-файл для создания сертификата push-уведомлений.

## <a name="register-your-app-for-push-notifications"></a>Регистрация приложения для работы с push-уведомлениями

Чтобы отправлять push-уведомления в приложение iOS, зарегистрируйте приложение в системе Apple, а также зарегистрируйте его для получения push-уведомлений.  

1. Если вы еще не зарегистрировали свое приложение, перейдите на [портал подготовки iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) в центре разработчиков Apple. Выполните вход с помощью своего идентификатора Apple, выберите **Identifiers** (Идентификаторы), **App IDs** (Идентификаторы приложений) и **+** , чтобы зарегистрировать новое приложение.

    ![Страница с идентификаторами приложения на портале подготовки iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Обновите следующие три значения для нового приложения и нажмите кнопку **Continue** (Продолжить).

   * **Имя.** В разделе **App ID Description** (Описание идентификатора приложения) в поле **Name** (Имя) введите описательное имя своего приложения.

   * **Идентификатор пакета**. В разделе **Explicit App ID** (Явный идентификатор приложения) введите в форму `<Organization Identifier>.<Product Name>` **идентификатор пакета**, как описано в [руководстве по распространению приложения](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Значения *Organization Identifier* (Идентификатор организации) и *Product Name* (Имя продукта) должны соответствовать идентификатору организации и имени продукта, указанным при создании проекта Xcode. На следующем снимке экрана значение *NotificationHubs* используется как идентификатор организации, а значение *GetStarted* — как название продукта. Убедитесь, что значение **Bundle Identifier** (Идентификатор пакета) совпадает со значением из проекта Xcode, чтобы в Xcode использовался правильный профиль публикации.

   * **Push-уведомления**. В разделе **App Services** (Службы приложений) установите флажок **Push Notifications** (Push-уведомления).

     ![Форма для регистрации нового идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     При этом будет создан идентификатор вашего приложения, а также запрошено подтверждение информации. Выберите **Register** (Зарегистрировать), чтобы подтвердить новый идентификатор приложения.

     После выбора **Register** (Зарегистрировать) отобразится экран **Registration complete** (Регистрация завершена), как показано на изображении ниже. Нажмите кнопку **Готово**.

     ![Завершенная регистрация идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. В центре разработчиков в разделе **App IDs** (Идентификаторы приложений) найдите только что созданный идентификатор и выберите его строку.

    ![Список идентификаторов приложений](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Выберите идентификатор приложения, чтобы отобразить сведения о приложении, а затем нажмите кнопку **Edit** (Изменить) внизу страницы.

    ![Страница изменения идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Прокрутите экран вниз и нажмите кнопку **Create Certificate** (Создать сертификат) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

    ![Кнопка создания сертификата для идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Откроется помощник **Add iOS Certificate** (Добавление сертификата iOS).

    > [!NOTE]
    > В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Только убедитесь, что при отправке уведомлений используется тот же тип сертификата.

1. Нажмите **Choose File** (Выбрать файл), перейдите к папке, в которой сохранен CSR-файл, созданный в первом задании, и нажмите кнопку **Generate** (Создать).

    ![Страница отправки созданного сертификата CSR](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. После того как сертификат будет создан на портале, нажмите кнопку **Download** (Скачать), а затем — **Done** (Готово).

    ![Страница скачивания созданного сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    При этом сертификат будет скачан и сохранен на вашем компьютере в папке **Загрузки**.

    ![Поиск файла сертификата в папке "Загрузки"](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Скачанному сертификату разработки по умолчанию задано имя **aps_development.cer**.

1. Выберите скачанный сертификат push-уведомлений **aps_development.cer**.

    При этом новый сертификат устанавливается в Keychain, как на следующем изображении.

    ![Список сертификатов Keychain Access с новым сертификатом](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Services**.

1. В программе Keychain Access щелкните правой кнопкой мыши новый сертификат push-уведомлений, созданный в категории **Certificates** (Сертификаты). Щелкните **Export** (Экспорт), укажите имя файла, выберите формат **P12** и нажмите кнопку **Save** (Сохранить).

    ![Экспорт сертификата в формате р12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Запишите имя файла и расположение экспортируемого сертификата в формате P12. Они нужны для аутентификации с помощью APNs.

    > [!NOTE]
    > С помощью этого руководства вы создадите файл с именем **Quickstart.p12**. Имя файла и расположение могут отличаться.

## <a name="create-a-provisioning-profile-for-the-app"></a>Создание профиля подготовки для приложения

1. На [портале подготовки iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) выберите **Provisioning Profiles** (Профили подготовки), **All** (Все) и **+** , чтобы создать профиль. Отобразится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

    ![Список с профилем подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. В разделе **Development** (Разработка) выберите **iOS App Development** (Разработка приложений для iOS) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

1. Затем в раскрывающемся списке **App ID** (Идентификатор приложения) выберите созданный идентификатор приложения и нажмите кнопку **Continue** (Продолжить).

    ![Выбор идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. В окне **Select certificates** (Выбор сертификатов) выберите обычный сертификат для разработки, используемый для подписывания кода, и нажмите кнопку **Continue** (Продолжить). Это не созданный сертификат push-уведомлений.

    ![Выбор сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Затем выберите устройства для тестирования и нажмите кнопку **Continue** (Продолжить).

    ![Выбор устройств](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Наконец, укажите имя профиля в поле **Profile Name** (Имя профиля) и нажмите кнопку **Generate** (Создать).

    ![Выбор имени профиля подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Когда новый профиль подготовки будет создан, скачайте и установите его на компьютер с Xcode. Затем выберите **Готово**.

    ![Скачивание профиля подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Создание концентратора уведомлений

В этом разделе показано, как создать центр уведомлений и настроить аутентификацию с использованием APNs и ранее созданного сертификата push-уведомлений (файл с расширением .p12). Если вы хотите использовать уже созданный центр уведомлений, перейдите к шагу 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Добавление данных об APNs в центр уведомлений

1. В разделе **Notification Services** выберите **Apple (APNS)** .

1. Выберите **Сертификат**.

1. Щелкните значок файла.

1. Выберите экспортированный ранее файл с расширением .p12.

1. Укажите пароль.

1. Выберите режим **Песочница**. Используйте режим **рабочей среды**, только если push-уведомления нужно отправлять пользователям, которые приобрели приложение в магазине.

    ![Настройка сертификации APNs на портале Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Вы добавили данные об APNs в центр уведомлений. У вас также есть строки подключения для регистрации приложения и отправки push-уведомлений.
