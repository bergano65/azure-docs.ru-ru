---
title: Настройка анализа зависимостей на основе агента в оценке azure Migrate Server
description: В этой статье описывается, как настроить анализ зависимостей на основе агентов в оценке azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453621"
---
# <a name="set-up-dependency-visualization"></a>Настройка визуализации зависимостей

В этой статье описывается, как настроить анализ зависимостей на основе агентов в Azure Migrate:Server Assessment. [Анализ зависимостей](concepts-dependency-visualization.md) помогает определить и понять зависимости между машинами, которые вы хотите оценить и перенести в Azure.

## <a name="before-you-start"></a>Перед началом работы

- [Узнайте об](concepts-dependency-visualization.md#agent-based-analysis) анализе зависимостей на основе агента.
- Просмотрите предпосылки и требования поддержки для настройки визуализации зависимостей на основе агента для [VMware VMs,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [физических серверов](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)и [Hyper-V VMs.](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Убедитесь, что вы [создали](how-to-add-tool-first-time.md) проект Azure Migrate.
- Если вы уже создали проект, убедитесь, что вы [добавили](how-to-assess.md) инструмент оценки Azure Migrate:Server.
- Убедитесь, что вы создали [прибор Azure Migrate,](migrate-appliance.md) чтобы обнаружить ваши банкоматы. Узнайте, как настроить прибор для [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)или [физических серверов.](how-to-set-up-appliance-physical.md) Прибор обнаруживает на месте машины и отправляет метаданные, данные о производительности в Azure Migrate:Server Assessment.
- Чтобы использовать визуализацию зависимостей, вы связываете [рабочее пространство log Analytics](../azure-monitor/platform/manage-access.md) с проектом Azure Migrate:
    - Рабочее пространство можно прикрепить только после настройки прибора Azure Migrate и обнаружения машин в проекте Azure Migrate.
    - Убедитесь, что в подписке есть рабочее пространство, содержащее проект Azure Migrate.
    - Рабочее пространство должно находиться в регионах Восточной Азии, юго-восточной Азии или Западной Европы. Рабочие области в других регионах не могут быть связаны с проектом.
    - Рабочее пространство должно находиться в регионе, в котором [поддерживается карта обслуживания.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
    - Можно связать новое или существующее рабочее пространство анализа журналов с проектом Azure Migrate.
    - Прикрепляемые рабочие области в первый раз настройки визуализации зависимости для машины. Рабочее пространство для проекта Azure Migrate не может быть изменено после его добавления.
    - В журнале «Аналитика» рабочее пространство, связанное с Azure Migrate, помечено ключом проекта «Миграция» и названием проекта.

## <a name="associate-a-workspace"></a>Ассоциировать рабочее пространство

1. После обнаружения машин для оценки в **серверах** > **Azure Migrate: Server Assessment**— нажмите **«Обзор».**  
2. В **Azure Migrate: Оценка серверов,** нажмите **Essentials**.
3. В **рабочей пространстве OMS,** нажмите **Требует конфигурации**.

     ![Настройка рабочего пространства анализа журналов](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. В **рабочем пространстве настройки OMS**укажите, хотите ли вы создать новое рабочее пространство или использовать существующее.
    - Можно выбрать существующее рабочее пространство из всех рабочих областей в подписке проекта мигрировать.
    - Для его ассоциированного доступа Reader необходим доступ к рабочему пространству.
5. Если вы создаете новое рабочее пространство, выберите место для него.

    ![Добавление новой рабочей области](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Скачивание и установка агентов виртуальной машины

На каждой машине, которую вы хотите проанализировать, установите агенты.

> [!NOTE]
> Для машин, контролируемых System Center Operations Manager 2012 R2 или позже, вам не нужно устанавливать агент MMA. Карта обслуживания интегрируется с менеджером операций. [Следуйте инструкциям](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) по интеграции.

1. В **Azure Migrate: Оценка серверов**, нажмите **Обнаруженные серверы**.
2. Для каждой машины, которую вы хотите проанализировать с визуализацией зависимостей, в столбце **зависимостей** нажмите **«Требуется установка агента».**
3. На странице **Зависимостей** загрузите агент MMA и зависимость для Windows или Linux.
4. Под **настройкой агента MMA**скопируйте идентификатор рабочего пространства и ключ. Вы нуждаетесь в них, когда вы устанавливаете агента ММА.

    ![Установка агентов](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Установка MMA

Установите MMA на каждую машину Windows или Linux, которую вы хотите проанализировать.

### <a name="install-mma-on-a-windows-machine"></a>Установка MMA на машине Windows

Вот как можно установить агент на компьютере Windows.

1. Дважды щелкните скачанный файл агента.
2. На странице **приветствия** нажмите кнопку **Далее**. На странице **Лицензионные Условия** щелкните, **чтобы я согласился** принять лицензию.
3. В поле **Конечная папка** оставьте или измените папку установки по умолчанию. Нажмите кнопку **Далее**.
4. В разделе **Параметры установки агента** последовательно выберите **Azure Log Analytics** > **Далее**.
5. Щелкните **Добавить**, чтобы добавить новую рабочую область Log Analytics. Вставьте идентификатор и ключ рабочей области, скопированные на портале. Нажмите кнопку **Далее**.

Вы можете установить агент из командной строки или с помощью автоматизированного метода, такого как Configuration Manager или [Intigua.](https://go.microsoft.com/fwlink/?linkid=2104196)
- Дополнительные сведения об использовании этих методов для установки агента MMA см. в разделе [Установка и настройка агента](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration).
- Кроме того, агент MMA можно установить с помощью этого [скрипта](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Узнайте больше](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) об операционных системах Windows, поддерживаемых MMA.

### <a name="install-mma-on-a-linux-machine"></a>Установка MMA на машине Linux

Для установки MMA на машину Linux:

1. Перенесите соответствующий пакет (x86 или x64) на компьютер Linux с помощью scp или sftp.
2. Установите пакет, используя аргумент --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Дополнительные сведения](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) о списке операционных систем Linux, поддерживаемых MMA. 

## <a name="install-the-dependency-agent"></a>Установка агента зависимостей

1. Чтобы установить агент зависимостей на компьютере Windows, дважды щелкните файл установки и следуйте инструкциям мастера.
2. Чтобы установить агент зависимостей на компьютере Linux, сделайте это с правами привилегированного пользователя, используя следующую команду.

    ```sh InstallDependencyAgent-Linux64.bin```

- [Узнайте больше](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) о том, как установить агент зависимостей с помощью скриптов.
- [Узнайте больше](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) об операционных системах, поддерживаемых агентом зависимости.


## <a name="create-a-group-using-dependency-visualization"></a>Создание группы с использованием визуализации зависимостей

Теперь создайте группу для оценки. 


> [!NOTE]
> Группы, для которых необходимо визуализировать зависимости, должны содержать не более 10 компьютеров. Если у вас есть более 10 машин, разделите их на более мелкие группы.

1. В **Azure Migrate: Оценка серверов**, нажмите **Обнаруженные серверы**.
2. В столбце **«Зависимости»** нажмите **«Вид» зависимостей** для каждой машины, которую вы хотите просмотреть.
3. На карте зависимости можно увидеть следующее:
    - Входящие (клиенты) и исходящие (серверы) соединения TCP, к и из машины.
    - Зависимые машины, в которых нет установленных агентов зависимости, сгруппированы по номерам портов.
    - Зависимые машины с установленными агентами зависимости отображаются как отдельные коробки.
    - Процессы, работающие внутри машины. Расширьте каждую машинную коробку для просмотра процессов.
    - Свойства машины (включая ФЗДН, операционную систему, адрес MAC). Нажмите на каждую машинную коробку, чтобы просмотреть детали.

4. Вы можете просмотреть зависимости за разные периоды. Для этого нужно щелкнуть длительность периода в метке диапазона времени.
    - По умолчанию диапазон равен одному часу. 
    - Можно изменить диапазон времени или указать даты начала и окончания и длительность.
    - Диапазон времени может составить до часа. Если вам нужен более длительный диапазон, используйте Azure Monitor для запроса зависимых данных на более длительный период.

5. После того как вы определили зависимые машины, которые вы хотите сгруппировать вместе, используйте Ctrl-Click, чтобы выбрать несколько машин на карте, и нажмите **групповые машины.**
6. Укажите имя группы.
7. Проверьте, нашла ли служба миграции Azure зависимые компьютеры.

    - Если зависимая машина не обнаружена Azure Migrate: Server Assessment, ее нельзя добавить в группу.
    - Чтобы добавить машину, запустите открытие снова и убедитесь, что машина обнаружена.

8. Если вы хотите создать оценку для этой группы, установите флажок.
8. Нажмите кнопку **ОК**, чтобы сохранить группу.

После создания группы мы рекомендуем установить агенты на всех машинах группы, а затем визуализировать зависимости для всей группы.

## <a name="query-dependency-data-in-azure-monitor"></a>Данные зависимости запросов в Azure Monitor

Можно заставить зазапрос данных зависимостей, захваченных картой службы, в рабочей области журнала Analytics, связанной с проектом Azure Migrate. Аналитика журналов используется для записи и запуска запросов журналов Azure Monitor.

- [Узнайте, как](../azure-monitor/insights/service-map.md#log-analytics-records) найти данные карты обслуживания в журнале Analytics.
- [Получить обзор](../azure-monitor/log-query/get-started-queries.md) написания запросов журнала в [журнале Analytics](../azure-monitor/log-query/get-started-portal.md).

Выполнить запрос для данных зависимости следующим образом:

1. После установки агентов перейдите на портал и выберите **Обзор**.
2. В **Azure Миграция: Оценка серверов**, нажмите **Обзор**. Нажмите вниз стрелка, чтобы расширить **Essentials**.
3. В **рабочей области OMS**щелкните имя рабочего пространства.
3. На странице рабочего пространства журнала Analytics > **общем**, нажмите **Журналы**.
4. Напишите запрос и нажмите **«Бег».**

### <a name="sample-queries"></a>Примеры запросов

Вот несколько примеров запросов, которые можно использовать для извлечения данных зависимостей.

- Вы можете изменить запросы, чтобы извлечь предпочтительные точки данных.
- [Просмотрите](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) полный список записей данных о зависимостях.
- [Просмотрите](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) дополнительные выборочные запросы.

#### <a name="sample-review-inbound-connections"></a>Пример: Просмотр входящих соединений

Просмотр входящих соединений для набора VMs.

- Записи в таблице для метрик соединения (VMConnection) не представляют отдельные физические сетевые соединения.
- Система группирует несколько физических сетевых подключений в логическое подключение.
- [Узнайте больше](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) о том, как агрегируются данные физического сетевого соединения в VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Пример: Обобщить отправленные и полученные данные

Этот пример обобщает объем данных, отправляемых и полученных на входящих соединениях между набором машин.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Дальнейшие действия

[Создайте оценку](how-to-create-assessment.md) для группы.


