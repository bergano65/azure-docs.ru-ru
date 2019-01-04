---
title: Управление сервером обработки для аварийного восстановления виртуальных машин VMware и физических серверов в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описано управление сервером обработки, настроенным для аварийного восстановления виртуальных машин VMware и физических серверов в Azure с помощью Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 05e77821ffe913a8c6669d58a3145635d230ff2b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847003"
---
# <a name="manage-process-servers"></a>Управление серверами обработки

По умолчанию сервер обработки, используемый при репликации виртуальных машин VMware или физических серверов в Azure, установлен на локальном компьютере сервера конфигурации. На некоторых экземплярах нужно настроить отдельный сервер обработки:

- Для больших развертываний могут понадобиться дополнительные серверы обработки в локальной среде, чтобы изменять масштаб емкости.
- Для восстановления размещения потребуется настроить временный сервер обработки в Azure. Восстановив размещение, вы можете удалить эту виртуальную машину. 

В этой статье собраны стандартные задачи управления для этих дополнительных серверов обработки.

## <a name="upgrade-a-process-server"></a>Обновление сервера обработки

Обновите сервер обработки, запущенный локально или в Azure (для восстановления размещения), следующим образом:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Как правило, при использовании образа из коллекции Azure, чтобы создать сервер обработки в Azure для восстановления размещения, применяется последняя доступная версия. Команды разработчиков Site Recovery регулярно выпускают исправления и улучшения, и мы рекомендуем вам систематически обновлять свои серверы обработки.

## <a name="balance-the-load-on-process-server"></a>Балансировка нагрузки на сервере обработки

Балансировка нагрузки между двумя серверами обработки:

1. Выберите **Хранилище служб восстановления** > **Управление** > **Site Recovery Infrastructure** (Инфраструктура Site Recovery)  > **For VMware & Physical machines** (Для VMware и физических компьютеров)  > **Серверы конфигурации**.
2. Щелкните сервер конфигурации, на котором зарегистрированы серверы обработки.
3. Список серверов обработки, зарегистрированных на серверах конфигурации, доступен на странице.
4. Щелкните сервер обработки, на котором необходимо изменить рабочую нагрузку.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Вы можете использовать параметры **Балансировка нагрузки** или **Переключить**, как описано ниже, в соответствии с требованиями.

### <a name="load-balance"></a>Балансировка нагрузки

Используя этот параметр, можно выбрать одну или несколько виртуальных машин и перенести их на другой сервер обработки.

1. Щелкните **Балансировка нагрузки**, выберите целевой сервер обработки из раскрывающегося списка. Щелкните **ОК**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Щелкните **Выбор машин**, выберите виртуальные машины, которые необходимо переместить c текущего сервера обработки на целевой. Сведения о среднем значении изменения данных отображаются возле каждой виртуальной машины.
3. Последовательно выберите **ОК**. Отслеживайте ход выполнения задания в разделе **Хранилище служб восстановления** > **Мониторинг** > **Задания Site Recovery**.
4. Изменения отобразятся через 15 минут после успешного завершения этой операции. Вы также можете [обновить сервер конфигурации](vmware-azure-manage-configuration-server.md#refresh-configuration-server), чтобы они вступили в силу немедленно.

### <a name="switch"></a>Switch

При использовании этого параметра вся рабочая нагрузка, защищенная на сервере обработки, перемещается на другой сервер обработки.

1. Щелкните **Переключить**, выберите целевой сервер обработки, нажмите кнопку **ОК**.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. Отслеживайте ход выполнения задания в разделе **Хранилище служб восстановления** > **Мониторинг** > **Задания Site Recovery**.
3. Изменения отобразятся через 15 минут после успешного завершения этой операции. Вы также можете [обновить сервер конфигурации](vmware-azure-manage-configuration-server.md#refresh-configuration-server), чтобы они вступили в силу немедленно.

## <a name="reregister-a-process-server"></a>Повторная регистрация сервера обработки

Если нужно повторно зарегистрировать сервер обработки, запущенный локально или в Azure с сервером конфигурации, сделайте следующее:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Сохранив параметры, сделайте следующее:

1. Откройте командную строку администратора на сервере обработки.
2. Перейдите к папке **%PROGRAMDATA%\ASR\Agent** и выполните эту команду:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Изменение параметров прокси-сервера для сервера обработки в локальной среде

Если в Azure сервер обработки подключается к Site Recovery с помощью прокси-сервера, используйте описанную ниже процедуру, чтобы изменить существующие параметры прокси-сервера.

1. Войдите на компьютер сервера обработки. 
2. Откройте командную строку PowerShell с правами администратора и выполните следующую команду.
  ```powershell
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Перейдите к папке **%PROGRAMDATA%\ASR\Agent** и выполните следующую команду:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Удаление сервера обработки

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Управление антивирусным ПО на серверах обработки

Если на изолированном сервере обработки или главном целевом сервере запущено антивирусное ПО, добавьте следующие папки в список исключений из антивирусной проверки:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR.
- C:\ProgramData\ASRLogs.
- C:\ProgramData\ASRSetupLogs.
- C:\ProgramData\LogUploadServiceLogs.
- C:\ProgramData\Microsoft Azure Site Recovery.
- Каталог установки сервера обработки, например C:\Program Files (x86)\Microsoft Azure Site Recovery

