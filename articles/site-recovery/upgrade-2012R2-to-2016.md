---
title: Обновление узлов Windows Server 2012 R2 и SCVMM, настроенных с помощью Azure Site Recovery, до Windows Server 2016
description: Узнайте, как настраивать аварийное восстановление в Azure для виртуальных машин Azure Stack с помощью службы Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61275529"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Обновление узлов Windows Server 2012 R2 и SCVMM 2012 R2, настроенных с помощью Azure Site Recovery, до Windows Server 2016 и SCVMM 2016

В этой статье рассказывается об обновлении узлов Windows Server 2012 R2 и SCVMM 2012 R2, настроенных с помощью Azure Site Recovery, до Windows Server 2016 и SCVMM 2016

Служба Site Recovery помогает реализовать стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR). Служба гарантирует, что рабочие нагрузки ваших виртуальных машин остаются доступными, когда происходят ожидаемые и неожиданные сбои.

> [!IMPORTANT]
> При обновлении узлов Windows Server 2012 R2, которые уже настроены для репликации с помощью Azure Site Recovery, необходимо выполнить действия, описанные в этом документе. Любой альтернативный метод обновления может привести к неподдерживаемым состояниям и к сбою репликации или отработки отказа.


Из этой статьи вы узнаете, как обновить следующие конфигурации в вашей среде.

> [!div class="checklist"]
> * **Узлы Windows Server 2012 R2, которые не управляются SCVMM** 
> * **Узлы Windows Server 2012 R2, которые управляются автономным сервером SCVMM 2012 R2** 
> * **Узлы Windows Server 2012 R2, которые управляются высокодоступным сервером SCVMM 2012 R2**


## <a name="prerequisites--factors-to-consider"></a>Предварительные условия и факторы, которые следует учитывать

Перед обновлением обратите внимание на следующее.

- Если у вас узлы Windows Server 2012 R2, которые не управляются SCVMM, а работают в автономной конфигурации среды, при попытке выполнить обновление репликация завершится сбоем.
- Если во время установки SCVMM 2012 R2 выбран параметр *Не хранить мои ключи в Active Directory в разделе "Управление распределенными ключами"* , обновления могут завершиться сбоем.

- Если вы используете System Center 2012 R2 VMM, 

    - проверьте сведения о базе данных о VMM: **Консоль VMM** -> **Параметры** -> **Общие** -> **Подключение к базе данных**
    - Проверьте учетные записи служб, которые используются для службы агента System Center Virtual Machine Manager
    - Убедитесь, что у вас есть резервная копия базы данных VMM.
    - Запишите имя базы данных задействованных в репликации серверов SCVMM. Это можно сделать,выбрав команду **Консоль VMM** -> **Параметры** -> **Общие** -> **Подключение к базе данных**
    - Запишите идентификаторы VMM: первичного сервера 2012 R2 и сервера восстановления VMM. Идентификатор VMM можно найти в ключе реестра "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Убедитесь, что новые службы SCVMM, которые вы добавляете в кластер, имеют прежние имена. 

- Если вы выполняете репликацию между двумя сайтами, управляемыми службой SCVMM на обеих сторонах, прежде чем обновлять первичный сервер, убедитесь, что сервер восстановления обновлен.
  > [!WARNING]
  > При обновлении SCVMM 2012 R2 в разделе "Распределенное управление ключами", выберите **хранить ключи шифрования в Active Directory**. Тщательно выбирайте параметры для учетной записи службы и распределенного управления ключами. Исходя из вашего выбора, зашифрованные данные, такие как пароли в шаблонах, могут быть недоступны после обновления и могут повлиять на репликацию с помощью Azure Site Recovery.

> [!IMPORTANT]
> Дополнительные сведения о SCVMM см. в разделе [Предварительные требования](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Узлы Windows Server 2012 R2, которые не управляются SCVMM 
Действия, описанные ниже, относятся к конфигурации пользователя из [узлов Hyper-V в Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture), применяемой в соответствии с этим [руководством](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial).

> [!WARNING]
> Как уже упоминалось в предварительных условиях, эти шаги применимы только к сценарию кластерной среды, но не к автономной конфигурации узла Hyper-V.

1. Выполните инструкции по использованию [последовательного обновления кластера](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process). для выполнения последовательного обновления кластера.
2. Удалите ссылку на узел Windows Server 2012 R2 из Azure Site Recovery для каждого нового узла Windows Server 2016, представленного в кластере, выполнив шаги, упомянутые [здесь]. Это должен быть узел, выбранный для завершения задачи и отключения от кластера.
3. Когда команда *Update-VMVersion* будет выполнена для всех виртуальных машин, эти обновления будут завершены. 
4. Выполните действия, указанные [здесь](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment), чтобы зарегистрировать новый узел Windows Server 2016 для Azure Site Recovery. Обратите внимание, что Hyper-V уже активирован на сайте, и вам просто нужно зарегистрировать новый узел в кластере. 
5.  Перейдите на портал Azure и проверьте состояние репликации в службах восстановления.

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Обновление узлов Windows Server 2012 R2, которые управляются автономным сервером SCVMM 2012 R2
Перед тем как обновить узлы Windows Server 2012 R2, необходимо выполнить обновление SCVMM 2012 R2 до SCVMM 2016. Выполните следующие шаги.

**Обновите автономную службу SCVMM 2012 R2 до SCVMM 2016**

1.  Чтобы удалить поставщик ASR, в Панели управления выберите "Программы -> Программы и компоненты ->Microsoft Azure Site Recovery" и нажмите "Удалить"
2. [Сохраните базу данных SCVMM и обновите операционную систему](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. В разделе **Установка и удаление программ** выберите **VMM** > **Удаление**. 2\. Щелкните **Удалить компоненты**, а затем выберите **Сервер управления и консоль VMM**. c. В разделе **Параметры базы данных** выберите **Сохранить базу данных**. d. Просмотрите итоги и нажмите кнопку **Удалить**.

4. [Установка VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Запустите SCVMM и проверьте состояние каждого узла на вкладке **Структуры**. Щелкните **Обновить**, чтобы получить самое последнее состояние. Должно отобразиться состояние "Требует внимания". 
17. Установите последнюю версию [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) на сервер SCVMM.
16. Установите последнюю версию агента [Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) на каждом узле кластера. Обновите, чтобы убедиться, что SCVMM успешно может запрашивать узлы.

**Обновите Windows Server 2012 R2 до Windows Server 2016**

1. Для выполнения последовательного обновления кластера выполните действия, описанные [здесь](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process). 
2. После добавления нового узла в кластер, обновите его из консоли SCVMM для установки агента VMM на обновленный узел.
3. Выполните обновление версии виртуальной машины с помощью команды *Update-VMVersion*. 
4.  Перейдите на портал Azure и проверьте состояние репликации виртуальных машин в хранилище служб восстановления. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Обновление узлов Windows Server 2012 R2, которые управляются высокодоступным сервером SCVMM 2012 R2
Перед тем как обновить узлы Windows Server 2012 R2, необходимо выполнить обновление SCVMM 2012 R2 до SCVMM 2016. При обновлении серверов SCVMM 2012 R2, настроенных с помощью Azure Site Recovery, поддерживаются следующие режимы обновления: смешанный режим без дополнительных серверов VMM и смешанный режим с дополнительными серверами VMM.

**Обновление SCVMM 2012 R2 до SCVMM 2016**

1.  Чтобы удалить поставщик ASR, в Панели управления выберите "Программы -> Программы и компоненты ->Microsoft Azure Site Recovery" и нажмите "Удалить"
2. В зависимости от выбранного режима обновления, выполните шаги, описанные [здесь](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server).
3. Запустите консоль SCVMM и проверьте состояние всех узлов на вкладке **Структуры**. Щелкните **Обновить**, чтобы получить самое последнее состояние. Должно отобразиться состояние "Требует внимания".
4. Установите последнюю версию [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) на сервер SCVMM.
5. Обновите последнюю версию [агента Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) на каждом узле кластера. Обновите, чтобы убедиться, что SCVMM успешно может запрашивать узлы.


**Обновите Windows Server 2012 R2 до Windows Server 2016**

1. Для выполнения последовательного обновления кластера выполните действия, описанные [здесь](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process).
2. После добавления нового узла в кластер, обновите его из консоли SCVMM для установки агента VMM на обновленный узел.
3. Выполните обновление версии виртуальной машины с помощью команды *Update-VMVersion*. 
4.  Перейдите на портал Azure и проверьте состояние репликации виртуальных машин в хранилище служб восстановления. 

## <a name="next-steps"></a>Дальнейшие действия
При выполнении обновления узлов можно выполнить [тестовую отработку отказа](tutorial-dr-drill-azure.md), чтобы проверить работоспособность репликации и статус восстановления.

