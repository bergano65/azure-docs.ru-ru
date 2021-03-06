---
title: Сведения о том, как на нескольких виртуальных машинах подключить решения из Службы автоматизации Azure для управления обновлениями, отслеживания изменений и инвентаризации
description: Сведения о том, как на виртуальной машине Azure подключить решения для управления обновлениями, отслеживания изменений и инвентаризации, входящие в состав службы автоматизации Azure
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 188306ca02cee05f17514826ae6d973a17e6bb97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421859"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Подключение решений для управления обновлениями, отслеживания изменений и инвентаризации на нескольких виртуальных машинах

Служба автоматизации Azure предоставляет решения для управления обновлениями безопасности операционной системы, отслеживания изменений и инвентаризации компонентов, установленных на компьютерах. Существует несколько способов подключения решений на виртуальных машинах: [непосредственно на виртуальной машине](automation-onboard-solutions-from-vm.md), [в учетной записи Службы автоматизации](automation-onboard-solutions-from-automation-account.md), в окне со списком виртуальных машин и с помощью [модуля runbook](automation-onboard-solutions.md). В этой статье рассматривается подключение таких решений в окне со списком виртуальных машин Azure.

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите в Azure (https://portal.azure.com ).

## <a name="enable-solutions"></a>включение решений;

На портале Azure перейдите в раздел **Виртуальные машины**.

Используя флажки, выберите виртуальные машины, на которых нужно подключить решения для управления обновлениями, отслеживания изменений или инвентаризации. Одновременно можно подключить не более трех групп ресурсов. Виртуальные машины Azure могут находиться в любом регионе вне зависимости от расположения учетной записи службы автоматизации.

![Список виртуальных машин](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Отфильтруйте список виртуальных машин, а затем установите верхний флажок, чтобы выбрать все виртуальные машины в отфильтрованном списке.

На панели команд щелкните **Службы** и выберите **Отслеживание изменений**, **Учет** или **Управление обновлениями**.

> [!NOTE]
> Для **отслеживания изменений** и **инвентаризации** используется одно и то же решение — при включение одного параметра включается и другой.

На рисунке ниже выбрано управление обновлениями. Если будет выбрана инвентаризация или отслеживание изменений, окно и доступные параметры будут такими же.

В списке виртуальных машин отображаются только те виртуальные машины, которые находятся в одной и той же подписке и в том же расположении. Если виртуальные машины находятся в более чем трех группах ресурсов, будут выбраны первые три группы.

### <a name="resource-group-limit"></a>Ограничения подключения

Для количества групп ресурсов для подключения установлены [ограничения развертывания Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Диспетчер ресурсов развертываний, которые не следует путать с развертываниями обновлений, ограничены 5 группами ресурсов на развертывание. Чтобы обеспечить целостность подключения, 2 группы зарезервированы для настройки рабочей области Log Analytics, учетной записи службы автоматизации и связанных ресурсов. Для развертывания можно выбрать 3 оставшиеся группы. Это ограничение применяется только к одновременным подключениям, а не к числу групп ресурсов, которыми может управлять решение службы автоматизации.

Вы также можете использовать модуль Runbook для адаптации. Дополнительные сведения см. [в статье подключение решений по обновлению и отслеживанию изменений к службе автоматизации Azure](automation-onboard-solutions.md).

С помощью фильтров выберите виртуальные машины из разных подписок, расположений и групп ресурсов.

![Подключение решения для управления обновлениями](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Проверьте параметры рабочей области Log Analytics и учетной записи службы автоматизации. По умолчанию выбрана существующая рабочая область и учетная запись Службы автоматизации. Если вы хотите использовать другую рабочую область Log Analytics и учетную запись службы автоматизации, щелкните флажок **Настраиваемая** и выберите нужный вариант в окне **Настраиваемая конфигурация**. После выбора рабочей области Log Analytics выполняется проверка, чтобы определить, связана ли рабочая область с учетной записью службы автоматизации. Если связь установлена, отобразится следующее. По завершении нажмите кнопку **ОК**.

![Выбор рабочей области и учетной записи](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Если связь с учетной записью не установлена, отобразится показанный ниже экран. Выберите учетную запись Службы автоматизации и по завершении нажмите кнопку **ОК**.

![Рабочая область отсутствует](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> При включении решений только определенные регионы поддерживают связывание рабочей области Log Analytics и учетной записи службы автоматизации.
>
> Список поддерживаемых пар сопоставлений см. в разделе [сопоставление регионов для учетной записи службы автоматизации и log Analytics рабочей области](how-to/region-mappings.md).

Если какую-то виртуальную машину не нужно подключать, снимите флажок рядом с ее именем. Если виртуальную машину подключить невозможно, флажок рядом с ее именем стоять не будет.

Нажмите кнопку **Включить**, чтобы включить решение. Процесс включения решения может занять до 15 минут.

## <a name="unlink-workspace"></a>Удаление связи с рабочей областью

Следующие решения зависят от рабочей области Log Analytics:

* [Управление обновлениями](automation-update-management.md)
* [Отслеживание изменений](automation-change-tracking.md)
* [Запуск и остановка виртуальных машин в нерабочее время](automation-solution-vm-management.md)

Если вы решили, что вам больше не нужно интегрировать учетную запись службы автоматизации с Log Analytics рабочей областью, вы можете удалить связь учетной записи непосредственно с портал Azure. Прежде чем продолжить, необходимо удалить решения, которые упоминались ранее. Иначе вы не сможете выполнить эту процедуру. Найдите в статье решение, которое вы импортировали, чтобы узнать, как его удалить.

Удалив эти решения, выполните следующие действия, чтобы отменить привязку учетной записи службы автоматизации.

> [!NOTE]
> Некоторые решения, в том числе решения для мониторинга Azure SQL более ранних версий, могли создать ресурсы службы автоматизации. Их также потребуется удалить, прежде чем удалить связь с рабочей областью.

1. На портале Azure откройте свою учетную запись службы автоматизации и на странице этой учетной записи выберите **Связанная рабочая область** в разделе **Связанные ресурсы** слева.

2. На странице "Отмена связи с рабочей областью" нажмите кнопку **Отменить связь с рабочей областью**.

   ![Страница "Отмена связи с рабочей областью"](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Появится запрос на подтверждение удаления.

3. Пока служба автоматизации Azure пытается отменить привязку учетной записи к рабочей области Log Analytics, вы можете отслеживать ход выполнения этой задачи в меню **Уведомления**.

Если вы используете решение управления обновлениями, при необходимости можно удалить следующие элементы, которые больше не нужны после удаления решения.

* Обновление расписаний. Каждый элемент имеет имя, которое соответствует созданному вами развертыванию обновления.

* Группы гибридных рабочих ролей, созданные для решения. Каждая группа будет иметь приблизительно такое имя: machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Если вы используете решение запуска и остановки виртуальных машин в нерабочее время, при необходимости можно удалить следующие элементы, которые больше не нужны после удаления решения.

* Расписание запуска и остановки виртуальной машины.
* Рабочие роли Runbook запуска и остановки виртуальной машины.
* Переменные

Кроме того, вы можете удалить связь рабочей области с учетной записью службы автоматизации из рабочей области Log Analytics. В рабочей области выберите **учетная запись службы автоматизации** в разделе **связанные ресурсы**. На странице Учетная запись службы автоматизации выберите отменить **связь с учетной записью**.

## <a name="troubleshooting"></a>Устранение неисправностей

При подключении нескольких виртуальных машин для некоторых из них может отображаться сообщение **Невозможно включить**. У этого могут быть разные причины. Ниже рассмотрены возможные причины появления сообщения **Невозможно включить** для виртуальных машин, на которых вы пытаетесь подключить решение.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Виртуальная машина относится к другой рабочей области: \<имя рабочей области\>.  Чтобы подключить виртуальную машину, измените ее конфигурацию

**Причина**. Эта ошибка показывает, что виртуальная машина, которую вы пытаетесь подключить, относится к другой рабочей области.

**Решение**. Щелкните **Использовать в качестве конфигурации**, чтобы изменить учетную запись службы автоматизации и рабочую область Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Виртуальная машина относится к рабочей области, которая недоступна в этой подписке

**Причина**. Причин может быть несколько:

* рабочая область находится в другой подписке;
* рабочая область больше не существует;
* рабочая область находится в группе ресурсов, к которой у вас нет доступа.

**Решение**. Найдите учетную запись Службы автоматизации, связанную с рабочей областью, к которой относится виртуальная машина, и подключите виртуальную машину, изменив область конфигурации.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Версия или дистрибутив операционной системы виртуальной машины не поддерживается

**Причина**. Не все дистрибутивы Linux и версии Windows поддерживают выбранное решение.

**Решение**. См. [список клиентов](automation-update-management.md#clients), которые поддерживает это решение.

### <a name="classic-vms-cannot-be-enabled"></a>Невозможно подключить классические виртуальные машины

**Причина**. Виртуальные машины, использующие классическую модель развертывания, не поддерживаются.

**Решение**. Перенесите виртуальную машину в модель развертывания Диспетчер ресурсов. Сведения о том, как это сделать, см. в статье [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Виртуальная машина остановлена (освобождена)

**Причина**. Виртуальная машина не находится в состоянии **Работает**.

**Решение**. Чтобы подключить виртуальную машину к решению, машина должна работать. Щелкните **Запустить ВМ**, чтобы запустить виртуальную машину, не покидая страницы.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить виртуальную машину для Управления обновлениями, необходимо сделать следующее.

* В рабочей области Log Analytics удалите виртуальную машину из сохраненных поисковых запросов конфигурации области `MicrosoftDefaultScopeConfig-Updates`. Сохраненные поисковые запросы хранятся на вкладке **Общие** рабочей области.
* Удалите [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) или [Агент Log Analytics для Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда решение включено для виртуальных машин, ознакомьтесь со статьей Управление обновлениями обзор, чтобы узнать, как создать **развертывание обновлений** для компьютеров.

> [!div class="nextstepaction"]
> [Управление обновлениями — Управление обновлениями и исправлениями для виртуальных машин Azure](./automation-tutorial-update-management.md)

Дополнительные руководства по решениям и сведения о работе с ними:

* [Управление обновлениями Windows в службе автоматизации Azure](automation-tutorial-update-management.md)

* [Получение данных об установленном программном обеспечении на виртуальных машинах Azure и других компьютерах](automation-tutorial-installed-software.md)

* [Устранение неполадок при изменениях в среде](automation-tutorial-troubleshoot-changes.md)
