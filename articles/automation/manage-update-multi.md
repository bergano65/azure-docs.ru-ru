---
title: Управление обновлениями для нескольких виртуальных машин Azure
description: В этой статье описывается, как управлять обновлениями для виртуальных машин Azure и не на основе Azure.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: e9a5a4330a90bd376114f836250e290944f03860
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417826"
---
# <a name="manage-updates-for-multiple-machines"></a>Управление обновлениями для нескольких виртуальных машин

С помощью решения по управлению обновлениями вы можете управлять обновлениями и исправлениями виртуальных машин Windows и Linux. В учетной записи [службы автоматизации Azure](automation-offering-get-started.md) вы можете сделать следующее:

- подключить виртуальные машины;
- оценить состояние доступных обновлений;
- запланировать установку необходимых обновлений;
- проверить результаты развертывания, чтобы убедиться, что обновления были успешно применены на всех виртуальных машинах, для которых включено управление обновлениями.

## <a name="prerequisites"></a>Технические условия

Чтобы использовать решение "Управление обновлениями", требуется:

- Виртуальная машина или компьютер, где установлена одна из поддерживаемых операционных систем.

- Доступ к репозиторию обновлений для виртуальных машин Linux, подключенных к решению.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Решение управления обновлениями поддерживается в приведенных ниже операционных системах.

|Операционная система  |Примечания  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Поддерживает только оценки обновлений.         |
|Windows Server 2008 R2 с пакетом обновления 1 и более поздней версии     |Windows PowerShell, начиная с версии 4.0. ([Скачать WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855).)</br> Для повышения надежности рекомендуем использовать Windows PowerShell 5.1. ([Скачать WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)         |
|CentOS 6 (x86 или x64) и 7 (x64).      | |
|Red Hat Enterprise 6 (x86 или x64) и 7 (x64).     | |
|SUSE Linux Enterprise Server 11 (x86 или x64) и 12 (x64)     | |
|Ubuntu 14.04 LTS, 16.04 LTS и 18.04 LTS (x86/x64)      | |

> [!NOTE]
> Чтобы обновления применялись только в период обслуживания в Ubuntu, повторно настройте пакет unattended-upgrades и отключите автоматическое обновление. Дополнительные сведения см. в разделе об [автоматических обновлениях руководства по Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Включение управления обновлениями для виртуальных машин Azure

На портале Azure откройте учетную запись службы автоматизации и выберите **Управление обновлениями**.

Выберите **Добавить виртуальные машины Azure**.

![Вкладка "Добавить виртуальную машину Azure"](./media/manage-update-multi/update-onboard-vm.png)

Выберите виртуальную машину для подключения.

В разделе **Включение управления обновлениями** нажмите кнопку **Включить**, чтобы подключить виртуальную машину.

![Диалоговое окно "Включение управления обновлениями"](./media/manage-update-multi/update-enable.png)

После подключения управление обновлениями будет включено для виртуальной машины.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Включение управления обновлениями для виртуальных машин Azure и компьютеров

Агент Log Analytics для Windows и Linux необходимо установить на виртуальных машинах, работающих в корпоративной сети или в другой облачной среде, чтобы включить их с помощью Управление обновлениями. Сведения о требованиях к системе и поддерживаемых методах развертывания агента на компьютерах, размещенных за пределами Azure, см. [в разделе Обзор агента log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Просмотр сведений о компьютерах, подключенных к учетной записи службы автоматизации

Включив управление обновлениями для компьютеров, можно просмотреть сведения о них, нажав кнопку **Компьютеры**. Это такие сведения, как *имя компьютера*, *состояние соответствия*, *среда*, *тип ОС*, *установленные критически важные обновления и обновления для системы безопасности*, *другие установленные обновления* и *состояние готовности к обновлению агента*.

  ![Просмотр вкладки "Компьютеры"](./media/manage-update-multi/update-computers-tab.png)

Если управление обновлениями недавно включено для компьютеров, возможно, они еще не оценивались. Для таких компьютеров будет отображаться состояние соответствия **Без оценки**. Вот список возможных значений состояния соответствия:

- **Соответствует** — компьютеры, на которых установлены все критически важные обновления и обновления для системы безопасности.

- **Не соответствует** — компьютеры, на которых отсутствует как минимум одно критически важное обновление или обновление для системы безопасности.

- **Без оценки** — данные оценки обновления не были получены от компьютера в течение ожидаемого отрезка времени. Для компьютеров Linux период ожидания находится за последний час. Для компьютеров Windows это последние 12 часов.

Чтобы просмотреть состояние агента, перейдите по ссылке в столбце **Готовность к обновлению агента**. Откроется область **гибридной рабочей роли** со сведениями о состоянии этой роли. Ниже приведен пример агента, который долгое время не был подключен к решению "Управление обновлениями".

![Просмотр вкладки "Компьютеры"](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Просмотр оценки обновлений

После включения решения "Управление обновлениями" отобразится панель **Управление обновлениями**. На вкладке **Отсутствующие обновления** можно просмотреть список отсутствующих обновлений.

## <a name="collect-data"></a>Сбор данных

Агенты, установленные на виртуальных машинах и компьютерах, собирают данные об обновлениях и отправляют их в решение управления обновлениями Azure.

### <a name="supported-agents"></a>Поддерживаемые агенты

В приведенной ниже таблице описаны подключенные источники, которые поддерживает это решение.

| Подключенный источник | Поддерживается | Description |
| --- | --- | --- |
| Агенты Windows |Да |Решение управления обновлениями собирает сведения об обновлениях системы с агентов Windows и запускает установку требуемых обновлений. |
| Агенты Linux |Да |Решение управления обновлениями собирает сведения об обновлениях системы с агентов Linux и запускает установку требуемых обновлений для поддерживаемых дистрибутивов. |
| Группа управления Operations Manager |Да |Решение управления обновлениями собирает сведения о системных обновлениях с агентов, состоящих в подключенной группе управления. |
| Учетная запись хранения Azure |Нет |В службе хранилища Azure не хранятся сведения о системных обновлениях. |

### <a name="collection-frequency"></a>Частота сбора

После того как компьютер выполнит проверку соответствия обновлений требованиям, Агент пересылает данные в неполное Azure Monitor журналов. На компьютере с Windows проверка на соответствие по умолчанию выполняется каждые 12 часов.

Помимо расписания проверки в течение 15 минут после перезапуска MMA запускается проверка на соответствие обновлений. Это происходит перед установкой обновления и после нее.

Для компьютера Linux проверка соответствия выполняется каждый час по умолчанию. При перезапуске агента MMA проверка соответствия инициируется в течение 15 минут.

Отображение обновленных данных с управляемых компьютеров на панели мониторинга может занять от 30 минут до 6 часов.

## <a name="schedule-an-update-deployment"></a>Планирование развертывания обновлений

Чтобы установить обновления, составьте план развертывания в рамках графика выпуска и периода обслуживания. Вы можете выбрать типы обновлений, которые будут включены в развертывание. Например, можно включить только критические обновления или обновления для системы безопасности и исключить накопительные пакеты обновления.

>[!NOTE]
>При планировании развертывания обновлений создается ресурс [планирования](shared-resources/schedules.md), связанный с runbook **Patch-MicrosoftOMSComputers**, который обрабатывает установку обновления на целевых компьютерах. Удаление ресурса расписания из портала Azure или с помощью PowerShell приводит к прерыванию запланированного развертывания обновлений и ошибке при попытке повторной настройки на портале. Ресурс расписания можно удалить только удалив соответствующее расписание развертывания.
>

Новое развертывание обновления для одной или нескольких виртуальных машин можно запланировать, щелкнув **Запланировать развертывание обновлений** в разделе **Управление обновлениями**.

В области **Новое развертывание обновления** укажите следующие сведения:

- **Имя**. Введите уникальное имя для идентификации развертывания обновлений.
- **Операционная система**. Выберите **Windows** или **Linux**.
- **Обновляемые группы**: Определите запрос на основе сочетания подписки, групп ресурсов, расположений и тегов, чтобы создать динамическую группу виртуальных машин Azure для включения в развертывание. Для виртуальных машин, не относящихся к Azure, сохраненные поисковые запросы используются для создания динамической группы, включаемой в развертывание. Дополнительные сведения см. в разделе [динамические группы](automation-update-management-groups.md).
- **Компьютеры, на которые нужно установить обновления**. Выберите "Сохраненные поисковые запросы", группу "Импортировано" или выберите "Компьютеры", чтобы выбрать компьютеры, которые требуется обновить.

   >[!NOTE]
   >Выбор параметра сохраненный поиск не приводит к возврату удостоверений компьютеров, а только их имен. При наличии нескольких виртуальных машин с одинаковым именем в нескольких группах ресурсов они возвращаются в результатах. Рекомендуется использовать параметр **группы для обновления** , чтобы обеспечить включение уникальных виртуальных машин, соответствующих Вашим критериям.

   Если выберете **Компьютеры**, готовность к обновлению будет показана в столбце **Готовность к обновлению агента**. Вы можете просмотреть состояние работоспособности компьютера перед планированием развертывания обновлений. Дополнительные сведения о различных способах создания групп компьютеров в журналах Azure Monitor см. в статье [Группы компьютеров в запросах к журналам Azure Monitor](../azure-monitor/platform/computer-groups.md).

  ![Область "Новое развертывание обновления"](./media/manage-update-multi/update-select-computers.png)

- **Классификации обновлений**. Выберите типы программного обеспечения, которые будут включены в развертывание обновления. Описание типов классификации обновлений см. в этом [разделе](automation-view-update-assessments.md#update-classifications). Ниже приведены типы классификации:
  - критические обновления;
  - Обновления для системы безопасности
  - накопительные пакеты обновления;
  - пакеты дополнительных компонентов;
  - пакеты обновления;
  - обновления определений;
  - Средства
  - Обновления

- **Обновления для включения или исключения**. После этого откроется страница **включения и исключения**. Обновления, которые можно включить или исключить, находятся на отдельных вкладках. Дополнительные сведения об обработке включения см. в разделе [Планирование развертывания обновлений](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Параметры расписания**. Вы можете принять дату и время по умолчанию (на 30 минут позже текущего времени) либо указать другое время.

   Кроме того, можно указать, происходит ли развертывание один раз или повторяется по расписанию. Чтобы настроить регулярное расписание, в разделе **Повторение** щелкните параметр **Повторяющееся**.

   ![Диалоговое окно "Параметры расписания"](./media/manage-update-multi/update-set-schedule.png)

- **Сценарии предварительного и последующего выполнения**: выберите сценарии, которые будут выполняться до и после развертывания. Дополнительные сведения см. в статье [Управление сценариями предварительного и последующего выполнения (предварительная версия)](pre-post-scripts.md).
- **Период обслуживания (минуты)** . Укажите временной период, в течение которого произойдет обновление развертывания. Этот параметр позволяет гарантировать, что изменения выполняются в рамках заданного периода обслуживания.

- **Управление перезагрузкой**. Этот параметр определяет способ обработки для развертывания обновлений после перезагрузки.

   |Вариант|Description|
   |---|---|
   |Перезагрузка при необходимости| **(По умолчанию)** . Перезагрузка запускается при необходимости, если позволяет окно обслуживания.|
   |Всегда выполнять перезагрузку|Перезагрузка запускается независимо от того, требуется ли она. |
   |Никогда не перезагружать|Независимо от того, требуется ли перезагрузка, перезагрузки сбрасываются.|
   |Только перезагрузка без установки обновлений.|Этот параметр игнорирует установку обновлений, а только запускает перезагрузку.|

Завершив настройку расписания, нажмите кнопку **Создать**, чтобы вернуться к панели мониторинга состояния. В таблице **Запланированные** отобразится созданное расписание развертываний.

> [!NOTE]
> Управление обновлениями поддерживает развертывание обновлений из Центра обновлений Windows и предварительное скачивание исправлений. Для этого необходимо внести изменения в исправляемые системы. Просмотрите [этот раздел](automation-configure-windows-update.md#pre-download-updates), чтобы узнать, как настроить эти параметры в своих системах.

## <a name="view-results-of-an-update-deployment"></a>Просмотр результатов развертывания обновлений

После запуска запланированного развертывания можно контролировать его состояние на вкладке **Развертывания обновлений** раздела **Управление обновлениями**.

Если развертывание выполняется в этот момент, отображается состояние **Выполняется**. Когда развертывание успешно завершится, состояние изменится на **Успешно**.

В случае сбоя одного или нескольких обновлений при развертывании для состояния отобразится значение **Частичный сбой**.

![Состояние обновления развертывания](./media/manage-update-multi/update-view-results.png)

Щелкните завершенное развертывание обновлений, чтобы просмотреть панель мониторинга этого развертывания.

В области **Результаты обновления** отображаются общее количество обновлений и результаты развертывания на виртуальной машине. В таблице справа представлен подробный разбор каждого обновления и результатов установки. Результаты установки могут принимать одно из следующих значений:

- **Попытка не предпринималась**. Обновление не установлено из-за недостатка времени в соответствии с заданным периодом обслуживания.
- **Выполнено**. Обновление успешно выполнено.
- **Сбой**. Обновление не удалось выполнить.

Чтобы просмотреть все записи журнала, созданные при развертывании, щелкните **Все журналы**.

Щелкните плитку "Выходные данные", чтобы просмотреть поток заданий модуля runbook, который управляет развертыванием обновления на целевой виртуальной машине.

Чтобы просмотреть подробные сведения об ошибках развертывания, щелкните **Ошибки**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управлении обновлениями, включая журналы, выходные данные и ошибки, см. в статье [Решение для управления обновлениями в Azure](../operations-management-suite/oms-solution-update-management.md).

