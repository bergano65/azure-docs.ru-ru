---
title: Включение автономной синхронизации для мобильного приложения Azure (Android)
description: Узнайте, как использовать мобильные приложения службы приложений для кэширования и синхронизации автономных данных в приложении Android.
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 0180e100432ac34b876af04ad99c9a5d189455c3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025458"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Включение автономной синхронизации для мобильного приложения Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Центр приложений Visual Studio поддерживает комплексные и интегрированные службы для разработки мобильных приложений. Разработчики могут использовать службы **сборки**, **тестирования** и **распространения** для настройки конвейера непрерывной интеграции и доставки. После развертывания приложения разработчики могут отслеживать состояние и использование своих приложений с помощью служб **аналитики** и **диагностики** , а также привлекать пользователей с помощью службы **push-уведомлений** . Разработчики также **могут использовать проверку подлинности** для аутентификации пользователей и службы **данных** , чтобы сохранять и синхронизировать данные приложений в облаке.
> Если вы хотите интегрировать облачные службы в мобильное приложение, зарегистрируйтесь в центре приложений Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) уже сегодня.

## <a name="overview"></a>Обзор
В этом учебнике рассматривается функция автономной синхронизации мобильных приложений Azure для Android. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением &mdash;просматривать, добавлять или изменять данные &mdash; даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных. Как только устройство возвращается в режим подключения к сети, эти изменения синхронизируются с удаленной внутренним сервером.

Если вы впервые работаете с мобильными приложениями Azure, сначала ознакомьтесь с руководством [Создание приложения Android]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений доступа к данным. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Обновление приложения для поддержки синхронизации автономных данных
При включенной синхронизации автономных данных операции чтения и записи осуществляются с помощью *таблицы синхронизации* (с помощью интерфейса *IMobileServiceSyncTable*), которая является частью базы данных **SQLite** на вашем устройстве.

Для принудительной отправки изменений на устройстве в мобильные службы Azure и получения изменений из мобильных служб используйте *контекст синхронизации* (*MobileServiceClient.SyncContext*), который инициализируется с помощью локальной базы данных.

1. В `TodoActivity.java` закомментируйте существующее определение `mToDoTable` и раскомментируйте версию таблицы синхронизации.
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. В методе `onCreate` закомментируйте существующую инициализацию `mToDoTable` и раскомментируйте следующее определение.
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. В `refreshItemsFromTable` закомментируйте определение `results` и раскомментируйте следующее определение.
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Закомментируйте определение `refreshItemsFromMobileServiceTable`.
5. Раскомментируйте определение `refreshItemsFromMobileServiceTableSyncTable`.
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Раскомментируйте определение `sync`.
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Тестирование приложения
В этом разделе мы протестируем поведение приложения при доступной сети Wi-Fi, а затем отключим Wi-Fi и рассмотрим автономный сценарий.

Добавляемые элементы данных будут находиться в локальном хранилище SQLight, но не будут синхронизированы с мобильной службой, пока вы не нажмете кнопку **Обновить** . У разных приложений могут быть разные требования к синхронизации данных, но для демонстрационных целей этого учебника мы будем считать, что пользователь явно запрашивает ее.

При нажатии этой кнопки запускается новая фоновая задача. Сначала она отправляет все изменения, внесенные в локальное хранилище, используя контекст синхронизации, а затем переносит все измененные данные из службы Azure в локальную таблицу.

### <a name="offline-testing"></a>Тестирование в автономном режиме
1. Переведите устройство или эмулятор в режим *В самолете*. Это позволит смоделировать автономный сценарий.
2. Добавьте несколько элементов *ToDo* или отметьте некоторые элементы как завершенные. Выйдите из эмулятора (или принудительно закройте приложение) и перезапустите его. Убедитесь, что изменения на устройстве по прежнему отображаются (так как они хранятся в локальном хранилище SQLight).
3. Просмотрите содержимое таблицы Azure *TodoItem* с помощью инструмента SQL, например *SQL Server Management Studio* или с помощью клиента REST, например *Fiddler* или *Postman*. Убедитесь, что новые элементы *не* были синхронизированы с сервером.
   
       + Для серверной части Node.js перейдите на [портал Azure](https://portal.azure.com/), затем на странице серверной части своего мобильного приложения щелкните **Easy Tables** > **TodoItem**, чтобы просмотреть содержимое таблицы `TodoItem`.
       + Для серверной части .NET просмотрите содержимое таблицы с помощью средства SQL, например *SQL Server Management Studio*, или с помощью клиента REST, например *Fiddler* или *Postman*.
4. Включите Wi-Fi в эмуляторе или на устройстве. Затем нажмите кнопку **Обновить** .
5. Снова просмотрите данные TodoItem на портале Azure. Должны отображаться как новые, так и измененные TodoItem.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Автономная Синхронизация данных в мобильных приложениях Azure]
* [Cloud Cover: Облачная среда: автономная синхронизация в мобильных службах Azure]\(примечание: видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных службах Azure\).

<!-- URLs. -->

[Автономная Синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md

[Создание приложения Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Облачная среда: автономная синхронизация в мобильных службах Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

