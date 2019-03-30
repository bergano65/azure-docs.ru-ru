---
title: Подключение Configuration Manager к Azure Monitor | Документация Майкрософт
description: В этой статье показано, как подключить Configuration Manager к рабочей области в Azure Monitor и начать анализ данных.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630670"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Подключение Configuration Manager к Azure Monitor
Можно подключить среду System Center Configuration Manager к Azure Monitor для синхронизации данных коллекций устройств и указания этих коллекций в Azure Monitor и службы автоматизации Azure.  

## <a name="prerequisites"></a>Технические условия

Azure Monitor поддерживает текущей ветви System Center Configuration Manager, версия 1606 или более поздней версии.  

## <a name="configuration-overview"></a>Общие сведения о настройке
Ниже приведены действия по настройке интеграции Configuration Manager с помощью Azure Monitor.  

1. На портале Azure зарегистрируйте Configuration Manager как веб-приложение и (или) приложение веб-API. Затем убедитесь, что у вас есть идентификатор клиента и секретный ключ клиента, полученные при регистрации в Azure Active Directory. Подробные сведения о выполнении этого шага см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md).
2. На портале Azure [предоставить Configuration Manager (зарегистрированное веб-приложение) с разрешением на доступ к Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. В Configuration Manager добавьте подключение с помощью мастера добавления подключений к OMS.
4. В случае утери пароля или секретного ключа клиента либо истечения срока их действия вы можете обновить свойства подключения в Configuration Manager.
5. [Скачайте и установите Microsoft Monitoring Agent](#download-and-install-the-agent) на компьютере под управлением роли системы сайта для точки подключения службы Configuration Manager. Агент отправляет данные Configuration Manager в рабочей области Log Analytics в Azure Monitor.
6. В Azure Monitor [импорту коллекций из Configuration Manager](#import-collections) как групп компьютеров.
7. В Azure Monitor, просматривать данные из Configuration Manager как [групп компьютеров](computer-groups.md).

Дополнительные сведения о подключении Configuration Manager к Azure Monitor в [синхронизация данных из Configuration Manager к рабочей области Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Предоставление Configuration Manager разрешений для Log Analytics
В следующей процедуре вы предоставляете роль *Участник* в рабочей области Log Analytics для приложения AD и субъекта-службы, созданных ранее для Configuration Manager.  Если у вас еще нет рабочей области, см. в разделе [создать рабочую область в Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) прежде чем продолжить.  Это позволит Configuration Manager проходить аутентификацию и подключаться к рабочей области Log Analytics.  

> [!NOTE]
> Необходимо указать разрешения в рабочей области Log Analytics для Configuration Manager. В противном случае при использовании мастера конфигурации в Configuration Manager вы получите сообщение об ошибке.
>

1. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
2. В списке рабочих областей Log Analytics выберите рабочую область, которую требуется изменить.
3. В области слева выберите **Управление доступом (IAM)**.
4. На странице "Управление доступом (IAM)" щелкните **Добавить назначение ролей** , после чего откроется **соответствующая область**.
5. В области **Добавить назначение ролей**  из раскрывающегося списка **Роли** выберите роль **Участник**.  
6. Из раскрывающегося списка **Назначение доступа к** выберите приложение Configuration Manager, ранее созданное в AD, и нажмите кнопку **ОК**.  

## <a name="download-and-install-the-agent"></a>Загрузка и установка агента
Ознакомьтесь со статьей [компьютеров Windows для подключения к Azure Monitor в Azure](agent-windows.md) знать методы, доступные для установки Microsoft Monitoring Agent на компьютере, где размещается точка подключения службы Configuration Manager роль системы сайта.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Добавление подключения к Log Analytics в Configuration Manager
Чтобы добавить подключение к Log Analytics, необходимо настроить [точку подключения службы](https://technet.microsoft.com/library/mt627781.aspx) в среде Configuration Manager для работы в оперативном режиме.

1. В рабочей области **Администрирование** в Configuration Manager выберите **Соединитель OMS**. Откроется **мастер добавления подключений к Log Analytics**. Щелкните **Далее**.

   >[!NOTE]
   >OMS, теперь называется Log Analytics, который входит в состав Azure Monitor.
   
2. На экране **Общие** убедитесь, что вы выполнили следующие действия и указали сведения по каждому пункту, а затем щелкните **Далее**.

   1. На портале Azure вы зарегистрировали Configuration Manager как веб-приложение и (или) приложение веб-API, и у вас есть [идентификатор клиента, полученный при регистрации](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. На портале Azure вы создали секретный ключ приложения для зарегистрированного приложения в Azure Active Directory.  
   3. На портале Azure вы указали зарегистрированное веб-приложение с разрешением на доступ к рабочей области Log Analytics в Azure Monitor.  
      ![Страница "Общие" мастера подключений к Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. На **Azure Active Directory** экрана, настройте параметры подключения к рабочей области Log Analytics, указав вашей **клиента**, **идентификатор клиента**и **Секретный ключ клиента**, а затем выберите **Далее**.  
   ![Страница "Azure Active Directory" мастера подключений к Log Analytics](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Если вы успешно выполнили все остальные процедуры, сведения, указанные на экране **OMS Connection Configuration** (Конфигурация подключения к OMS), автоматически появятся на этой странице. Параметры подключения должны отобразиться в вашей **подписке Azure**, **группе ресурсов Azure** и **рабочей области Operations Management Suite**.  
   ![Страница "Соединение Log Analytics" мастера подключений к Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. Мастер подключится к рабочей области Log Analytics, используя вами сведения. Выберите коллекции устройств, которые требуется синхронизировать со службой, а затем нажмите кнопку **Добавить**.  
   ![Выбор коллекций](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Проверьте параметры подключения на экране **Сводка**, а затем щелкните **Далее**. На экране **Ход выполнения** отображается состояние подключения (должно отобразиться состояние **Выполнено**).

> [!NOTE]
> Необходимо подключить сайт верхнего уровня в иерархии. Это позволит Azure Monitor. При подключении к автономному первичному сайту в Azure Monitor и добавьте в среду сайт центра администрирования, необходимо удалить и заново создать подключение в новой иерархии.
>
>

После вы связали Configuration Manager с Azure Monitor, можно добавить или удалить коллекции и просмотр свойств соединения.

## <a name="update-log-analytics-workspace-connection-properties"></a>Обновление свойств подключения к рабочей области Log Analytics
В случае утери пароля или секретного ключа клиента либо истечения срока их действия необходимо вручную обновить свойства подключения к Log Analytics.

1. В Configuration Manager перейдите к разделу **Облачные службы**, а затем выберите **Соединитель OMS**, чтобы открыть страницу **OMS Connection Properties** (Свойства подключения к OMS).
2. На этой странице щелкните **Azure Active Directory**, чтобы просмотреть сведения о **клиенте**, **идентификаторе клиента** и **сроке действия секретного ключа клиента**. **Проверьте** **секретный ключ клиента**, если срок его действия истек.

## <a name="import-collections"></a>Импорт коллекций
После добавления подключения к Log Analytics в Configuration Manager и агент установлен на компьютере под управлением Configuration Manager подключения службы роль системы сайта точки, следующему шагу — импорту коллекций из Configuration Manager в Azure Отслеживать как групп компьютеров.

После завершения начальной настройки для импорта коллекций устройств из иерархии сведения о членстве в коллекции извлекается каждые 3 часа для обеспечения актуальности сведений о членстве. Это можно отключить в любое время.

1. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Рабочие области Log Analytics**.
2. Из списка рабочих областей Log Analytics выберите ту, в которой зарегистрировано приложение Configuration Manager.  
3. Выберите **Дополнительные параметры**.
4. Выберите **Группы компьютеров**, а затем щелкните **SCCM**.  
5. Выберите **Импортировать членства в коллекциях Configuration Manager** и нажмите кнопку **Сохранить**.  
   ![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Просмотр данных из Configuration Manager
После добавления подключения к Log Analytics в Configuration Manager и агент установлен на компьютере под управлением роли системы сайта Configuration Manager службы подключения точки, данные от агента отправляются в рабочую область Log Analytics в Azure Monitor. В Azure Monitor, коллекции из Configuration Manager отображаются как [групп компьютеров](../../azure-monitor/platform/computer-groups.md). Группы можно просматривать на странице **Configuration Manager**, выбрав **Параметры > Группы компьютеров**.

После импорта коллекций можно увидеть число обнаруженных компьютеров с членством в коллекциях. Также можно увидеть число импортированных коллекций.

![Вкладка "SCCM" на вкладке "Группы компьютеров"](./media/collect-sccm/sccm-computer-groups02.png)

Если щелкнуть одну из них, откроется окно поиска, в котором отображаются либо все импортированные группы, либо все компьютеры, принадлежащие к каждой группе. С помощью [поиска по журналам](../../azure-monitor/log-query/log-query-overview.md) можно выполнить подробный анализ данных Configuration Manager.

## <a name="next-steps"></a>Дальнейшие действия
* Воспользуйтесь [поиском по журналам](../../azure-monitor/log-query/log-query-overview.md) для просмотра подробных сведений о данных Configuration Manager.
