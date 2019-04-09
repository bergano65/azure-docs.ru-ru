---
title: Настройка агента Azure Log Analytics для гибридного компьютера Linux | Документация Майкрософт
description: Узнайте, как развернуть агент Log Analytics для Linux на компьютерах вне среды Azure и включить сбор данных с помощью Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 15b7c052d0e4d51cb033607c156a55c581f722b1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893968"
---
# <a name="configure-log-analytics-agent-for-linux-computers-in-a-hybrid-environment"></a>Настройка агента Log Analytics для компьютеров Linux в гибридной среде
[Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) может собирать данные напрямую c физических компьютеров или виртуальных машин Linux в вашем центре обработки данных или в другой облачной среде в один репозиторий для подробного анализа и исправления. В этом кратком руководстве показано, как настроить и собирать данные c компьютера Linux с помощью нескольких простых действий.  Сведения о виртуальных машинах Linux в Azure приведены далее в разделе [Сбор данных о виртуальных машинах Azure](quick-collect-azurevm.md).  

Дополнительные сведения о поддерживаемой конфигурации см. в разделах о [поддерживаемых операционных системах Linux](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) и [требованиях к сетевым брандмауэрам](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Создание рабочей области
1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.

    ![Портал Azure](media/quick-collect-linux-computer/azure-portal-01.png) 

2. Щелкните **Создать** и задайте следующие параметры:

   * Введите имя для новой **рабочей области Log Analytics**, например *DefaultLAWorkspace*. Рабочие области OMS теперь называются рабочими областями Log Analytics.   
   * Выберите в раскрывающемся списке **подписку**, с которой нужно связать рабочую область, если выбранная по умолчанию не подходит.
   * В разделе **Группа ресурсов** выберите имеющуюся группу ресурсов, в которой содержится одна или несколько виртуальных машин Azure.  
   * Выберите **Расположение**, в котором развернуты виртуальные машины.  Дополнительные сведения о доступности службы Log Analytics в регионах см. в [этой статье](https://azure.microsoft.com/regions/services/).  
   * При создании рабочей области в новой подписке, созданной после 2 апреля 2018 г., будет автоматически использоваться тарифный план *За ГБ*, и выбор ценовой категории будет недоступен.  При создании рабочей области в существующей подписке, созданной до 2 апреля, или в подписке, которая была привязана к существующей регистрации EA, выберите нужную ценовую категорию.  Дополнительные сведения о конкретной ценовой категории см. в статье [Цены на Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Колонка создания ресурса Log Analytics](media/quick-collect-linux-computer/create-loganalytics-workspace-02.png)<br>  

3. После ввода необходимых сведений в области **Рабочая область Log Analytics** щелкните **OK**.  

Пока проверяются данные, ход создания рабочей области можно проверить в разделе **Уведомления** в меню. 

## <a name="obtain-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области
Перед установкой агента Log Analytics для Linux требуется получить идентификатор и ключ для рабочей области Log Analytics.  Эта информация необходима сценарию оболочки агента для правильной настройки агента и обеспечения его взаимодействия с Log Analytics.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
2. В списке рабочих областей Log Analytics выберите рабочую область *DefaultLAWorkspace*, созданную ранее.
3. Выберите **Дополнительные параметры**.

    ![Дополнительные параметры Log Analytics](media/quick-collect-linux-computer/log-analytics-advanced-settings-01.png) 
 
4. Выберите **Подключенные источники**, а затем выберите **Серверы с Linux**.   
5. Необходимые значения указаны справа от полей **Идентификатор рабочей области** и **Первичный ключ**. Скопируйте их и вставьте в любой удобный для вас редактор.   

## <a name="install-the-agent-for-linux"></a>Установка агента для Linux
Ниже приведены инструкции по настройке установки агента для Log Analytics в Azure и облаке Azure для государственных организаций.  

>[!NOTE]
>Агент Log Analytics для Linux невозможно настроить для отправки отчетов в несколько рабочих областей Log Analytics.  

Если требуется подключение компьютера Linux через прокси-сервер к Log Analytics, конфигурацию прокси-сервера можно указать в командной строке, включив `-p [protocol://][user:password@]proxyhost[:port]`.  Свойство *proxyhost* принимает полное доменное имя или IP-адрес прокси-сервера. 

Например:  `https://user01:password@proxy01.contoso.com:30443`

1. Чтобы настроить компьютер Linux для подключения к Log Analytics, выполните следующую команду, указав идентификатор рабочей области и первичный ключ, скопированные ранее. Следующая команда скачивает агент, проверяет его контрольную сумму и устанавливает его. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Следующая команда включает параметр `-p` прокси-сервера и пример синтаксиса.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Чтобы настроить компьютер Linux для подключения к Log Analytics в облаке Azure для государственных организаций, выполните следующую команду, указав идентификатор рабочей области и первичный ключ, скопированные ранее. Следующая команда скачивает агент, проверяет его контрольную сумму и устанавливает его. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Следующая команда включает параметр `-p` прокси-сервера и пример синтаксиса.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Перезапустите агент, выполнив следующую команду. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Сбор данных событий и производительности
Log Analytics может собирать события из системного журнала и счетчиков производительности Linux, указанных для долгосрочного анализа и формирования отчетов, а также предпринимать действия при обнаружении определенного условия.  Сначала выполните приведенные ниже действия для настройки сбора событий из системного журнала Linux, а также нескольких стандартных счетчиков производительности.  

1. Выберите **Системный журнал**.  
2. Чтобы добавить журнал событий, введите его имя. Введите **Системный журнал** и щелкните знак "плюс" **+**.  
3. В таблице снимите флажок для степеней серьезности **Информация**, **Уведомление** и **Отладить**. 
4. В верхней части страницы щелкните **Сохранить**, чтобы сохранить конфигурацию.
5. Выберите **Linux Performance Data** (Данные производительности Linux), чтобы включить сбор данных счетчиков производительности на компьютере Linux. 
6. При первой настройке счетчиков производительности Linux для новой рабочей области Log Analytics вы можете быстро создать несколько распространенных счетчиков. Рядом с каждым счетчиком в списке есть флажок. 

    ![Выбраны стандартные счетчики производительности Windows](media/quick-collect-linux-computer/linux-perfcounters-default.png)
    
    Щелкните **Add the selected performance counters** (Добавить выбранные счетчики производительности). Они добавляются и устанавливаются с десятисекундным интервалом сбора.

7. В верхней части страницы щелкните **Сохранить**, чтобы сохранить конфигурацию.

## <a name="view-data-collected"></a>Просмотр собранных данных
Теперь, когда сбор данных включен, можно запустить простой пример поиска по журналам, чтобы просмотреть некоторые данные с целевого компьютера.  

1. На портале Azure перейдите к Log Analytics и выберите рабочую область, созданную ранее.
2. Щелкните плитку **Log Search** (Поиск по журналам) и в соответствующей области в поле запроса введите `Perf`, а затем нажмите клавишу ВВОД и нажмите кнопку "Поиск" справа от поля запроса.

    ![Пример запроса поиска по журналам Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Например, запрос на следующем рисунке вернул 735 записей производительности.

    ![Результат поиска по журналам Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда агент больше не нужен, его можно удалить с компьютера Linux, а также можно удалить рабочую область Log Analytics.  

Чтобы удалить агент, выполните следующую команду на компьютере Linux. Аргумент *--purge* полностью удаляет агент и его конфигурацию.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Чтобы удалить рабочую область Log Analytics, созданную ранее, выберите ее, затем на странице ресурсов щелкните **Удалить**.

![Удаление ресурса Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы собираете данные о работе и производительности со своих локальных компьютеров Linux, можно легко начать изучение и анализ собранных данных, а также работать с ними *бесплатно*.  

Чтобы узнать, как просматривать и анализировать данные, перейдите к следующему руководству.   

> [!div class="nextstepaction"]
> [Просмотр и анализ данных в Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
