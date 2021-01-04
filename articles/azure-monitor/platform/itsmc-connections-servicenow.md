---
title: Подключение ServiceNow с Соединитель управления ИТ-услугами
description: В этой статье содержатся сведения о ServiceNow с Соединитель управления ИТ-услугами (ITSMC) в Azure Monitor для централизованного мониторинга рабочих элементов ITSM и управления ими.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729723"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Подключение ServiceNow с Соединитель управления ИТ-услугами

В этой статье содержатся сведения о настройке подключения между экземпляром ServiceNow и Соединитель управления ИТ-услугами (ITSMC) в Log Analytics для централизованного управления рабочими элементами.

В разделах ниже приведены сведения о подключении экземпляра ServiceNow к ITSMC в Azure.

## <a name="prerequisites"></a>Предварительные требования
Выполните указанные ниже предварительные требования.
- ITSMC установлен. Дополнительные сведения: [Adding the IT Service Management Connector Solution](./itsmc-definition.md#add-it-service-management-connector) (Добавление решения "Соединитель управления ИТ-услугами").
- Поддерживаемые версии ServiceNow: Орландо, Нью Йорк, Мадрид, Лондон, Kingston), Джакарта, Стамбул, Хельсинки, Geneva.
- Сегодня оповещения, отправленные из Azure Monitor могут быть созданы в ServiceNow один из следующих элементов: события, инциденты или оповещения.
> [!NOTE]
> ITSMC поддерживает только официальное предложение SaaS из Service Now. Закрытые развертывания Service Now не поддерживаются. 

**Администраторы ServiceNow должны выполнить в экземплярах ServiceNow следующие действия:**
- Создать идентификатор и секрет клиента для продукта ServiceNow. Дополнительные сведения о том, как создать идентификатор и секрет клиента, см. в соответствующих разделах:

    - [Настройка OAuth для Орландо](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Настройка OAuth для версии Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> В рамках определения процедуры "Настройка OAuth" мы рекомендуем сделать следующее.
>
> 1) **Продлите срок действия токена обновления до 90 дней (7 776 000 секунд).** В рамках процедуры [Настройка OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) на этапе 2 необходимо следующее. [Создание конечной точки, чтобы клиенты могли получить доступ к экземпляру](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0). После определения конечной точки в колонке ServiceNow выполните поиск строки System OAuth (OAuth системы), а затем выберите Application Registry (Реестр приложений). Выберите имя OAuth, которое было определено, и продлите срок в поле Refresh token Lifespan (Срок действия токена обновления) до 7 776 000 (90 дней в секундах).
> В конце нажмите кнопку "Обновить".
> 2) **Мы рекомендуем реализовать внутреннюю процедуру, обеспечивающую постоянную активность подключения:** нужно обновлять токен в соответствии со сроком существования токена обновления. Обязательно выполните следующие операции до ожидаемого срока действия токена обновления (рекомендуется делать это за несколько дней до истечения срока).
>
>     1. [Завершите процесс синхронизации вручную для конфигурации соединителя ITSM](./itsmc-resync-servicenow.md).
>     2. Отмените старый токен обновления, так как использовать старые ключи не рекомендуется из соображений безопасности. В колонке ServiceNow выполните поиск строки System OAuth (OAuth системы), а затем выберите Manage Tokens (Управление токенами). Выберите старый токен в списке по имени OAuth и дате окончания срока действия.
> ![Отображение определения OAuth системы](media/itsmc-connections/snow-system-oauth.png)
>     3. Щелкните "Отменить доступ" и "Отменить".

- Установить пользовательское приложение (ServiceNow) для интеграции Microsoft Log Analytics. [Подробнее](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC поддерживает только официальное пользовательское приложение для интеграции с Microsoft Log Analytics, которое скачивается из магазина ServiceNow. ITSMC не поддерживают прием кода на стороне ServiceNow или приложение, которое не является частью официального решения ServiceNow. 
- Создать роль пользователя для интеграции в установленном приложении для пользователя. Дополнительные сведения о создании роли пользователя для интеграции см. [здесь](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Процедура подключения**
Используйте следующую процедуру, чтобы создать подключение ServiceNow:


1. На портале Azure перейдите к разделу **Все ресурсы** и найдите элемент **ServiceDesk(имя_вашей_рабочей_области)**

2.  В области **Источники данных рабочей области** щелкните **Подключения ITSM**.
    ![Новое подключение](media/itsmc-connections/add-new-itsm-connection.png)

3. Вверху справа щелкните **Добавить**.

4. Укажите сведения, которые перечислены в следующей таблице, а затем щелкните **OK**, чтобы создать подключение.


> [!NOTE]
> Все эти параметры являются обязательными.

| **Поле** | **Описание** |
| --- | --- |
| **Имя соединения**   | Введите имя экземпляра ServiceNow, который вы хотите подключить к ITSMC.  В Log Analytics это имя вам потребуется позже при настройке рабочих элементов в ITSM или подробном анализе журналов. |
| **Тип партнера**   | Выберите **ServiceNow**. |
| **Имя пользователя**   | Введите имя пользователя для интеграции, которое вы создали в приложении ServiceNow для подключения к ITSMC. Дополнительные сведения: [Создание роли пользователя для интеграции в приложении ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Пароль**   | Введите пароль, связанный с этим именем пользователя. **Примечание.** Имя пользователя и пароль будут использоваться только для создания маркеров аутентификации. Они не сохраняются в службе ITSMC.  |
| **URL-адрес сервера**   | Введите URL-адрес экземпляра ServiceNow, который вы хотите подключить к ITSMC. URL-адрес должен указывать на поддерживаемую версию SaaS с суффиксом .servicenow.com.|
| **Идентификатор клиента**   | Введите созданный ранее идентификатор клиента, который вы хотите использовать для проверки подлинности OAuth2.  Дополнительные сведения о создании идентификатора и секрета клиента см.   на странице [установки OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Секрет клиента**   | Введите секрет клиента, созданный для этого идентификатора.   |
| **Область синхронизации данных**   | Выберите рабочие элементы ServiceNow, которые вы хотите синхронизировать с Azure Log Analytics через ITSMC.  Выбранные значения импортируются в службу Log Analytics.   **Параметры:**  инциденты и запросы на изменения.|
| **Синхронизация данных** | Введите число прошедших дней, которые будут учитываться при синхронизации. **Максимальное ограничение:** 120 дней. |
| **Создание нового элемента конфигурации в решении ITSM** | Выберите этот параметр, если вы хотите создать элементы конфигурации в продукте ITSM. После выбора этого параметра ITSM создаст необходимые элементы (если они еще не созданы) как элементы конфигурации в поддерживаемой системе ITSM. **Параметр по умолчанию**: отключено. |

![Подключение к ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**После успешного подключения и синхронизации:**

- Выбранные рабочие элементы из экземпляра ServiceNow будут импортированы в службу Azure **Log Analytics**. Вы можете просмотреть сводку по этим рабочим элементам с помощью плитки соединителя управления ИТ-службами.

- Вы можете создавать инциденты из оповещений или записей журналов Log Analytics, а также из оповещений Azure в этом экземпляре ServiceNow.

> [!NOTE]
> В ServiceNow имеется ограничение скорости для запросов в час. Чтобы настроить ограничение, используйте его, определив "ограничение скорости для входящих REST API" в экземпляре ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Создание роли пользователя для интеграции в приложении ServiceNow

Выполните следующие действия:

1. Посетите [магазин ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) и установите **приложение для пользователя для интеграции Microsoft OMS и ServiceNow** в экземпляр ServiceNow.
   
   >[!NOTE]
   >В рамках перехода с Microsoft Operations Management Suite (OMS) на Azure Monitor набор OMS теперь называется Log Analytics.     
2. После установки перейдите на левую панель навигации экземпляра ServiceNow, найдите и выберите интегратор Microsoft OMS.  
3. Щелкните **Installation Checklist** (Контрольный список установки).

   Если роль пользователя еще не создана, вы увидите состояние **Не завершено**.

4. В текстовых полях рядом с разделом **Создание пользователя для интеграции** введите имя пользователя, который может подключиться к ITSMC в Azure.
5. Введите пароль для этого пользователя и нажмите кнопку **ОК**.  

> [!NOTE]
> Эти учетные данные потребуются для подключения к ServiceNow в Azure.

Отобразится созданный пользователь с назначенными по умолчанию ролями.

**Роли по умолчанию:**
- personalize_choices;
- import_transformer;
-   x_mioms_microsoft.user;
-   itil;
-   template_editor;
-   view_changer.

После успешного создания пользователя состояние **проверки контрольного списка установки** изменится на "Завершено" и будут выведены дополнительные сведения о созданной для приложения роли пользователя.

> [!NOTE]
> Соединитель ITSM могут отправлять инциденты ServiceNow. Для этого на экземпляре ServiceNow не нужно устанавливать модули. Если в экземпляре ServiceNow используется модуль EventManagement и вы хотите создать события или предупреждения в ServiceNow с помощью соединителя, добавьте следующие роли для пользователя интеграции:
> 
>    - evt_mgmt_integration;
>    - evt_mgmt_operator.  


## <a name="next-steps"></a>Дальнейшие действия

* [Обзор соединитель ITSM](itsmc-overview.md)
* [Создание рабочих элементов ITSM из оповещений Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Устранение неполадок с соединителем ITSM](./itsmc-resync-servicenow.md)