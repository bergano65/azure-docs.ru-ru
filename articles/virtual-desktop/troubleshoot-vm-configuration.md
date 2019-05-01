---
title: Клиент и узла создания пула в Windows виртуальный рабочий стол — Azure
description: Как устранить проблемы при настройке клиента и сеанса узла виртуальной машины (VM) в среде виртуального рабочего стола Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928127"
---
# <a name="tenant-and-host-pool-creation"></a>Создание пула клиента и узла

Используйте эту статью для устранения неполадок, которые возникают при настройке виртуального рабочего стола Windows сеанса размещения виртуальных машин (ВМ).

## <a name="provide-feedback"></a>Отзывы

В период предоставления предварительной версии Виртуального рабочего стола Windows мы не принимаем запросы в службу поддержки. Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="vms-are-not-joined-to-the-domain"></a>Виртуальные машины не присоединены к домену

Если у вас возникают проблемы, присоединение виртуальных машин к домену, выполните следующие действия.

- Присоедините виртуальную Машину вручную с помощью процесса в [присоединение виртуальной машины Windows Server к управляемому домену](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) или с помощью [шаблона присоединения к домену](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Попробуйте обратиться имя домена из командной строки на виртуальной Машине.
- Просмотрите список сообщений об ошибке соединения домена в [Устранение неполадок домена соединения сообщения об ошибках](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Ошибка: Неправильные учетные данные

**Причина.** Произошла опечатка при учетные данные были введены в Azure Resource Manager устраняет интерфейс шаблона.

**Исправление:** Следуйте этим инструкциям, чтобы исправить учетные данные.

1. Вручную добавьте виртуальные машины к домену.
2. Повторное развертывание после подтверждения учетных данных. См. в разделе [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Присоединение виртуальных машин к домену, с помощью шаблона с [объединяет существующей виртуальной Машины Windows с доменом AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Ошибка: Время ожидания для ввода данных пользователем

**Причина.** Возможно, учетная запись, используемая для завершения присоединения к домену многофакторной проверки подлинности (MFA).

**Исправление:** Следуйте этим инструкциям, чтобы завершить присоединение к домену.

1. Временно удалите многофакторной проверки Подлинности для учетной записи.
2. Используйте учетную запись службы.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Ошибка: Учетная запись, используемая во время подготовки не имеет разрешений для завершения операции

**Причина.** Используется учетная запись не имеет разрешений для присоединения виртуальных машин к домену из-за соответствия требованиям и нормативам.

**Исправление:** Следуйте этим инструкциям.

1. Эта учетная запись является членом группы администраторов.
2. Предоставьте необходимые разрешения для используемой учетной записи.

### <a name="error-domain-name-doesnt-resolve"></a>Ошибка: Не выполняет разрешение имени домена

**Причина 1**. Виртуальные машины находятся в группе ресурсов, не связанного с виртуальной сетью (VNET), где расположен этот домен.

**Исправление 1:** Создание Пиринговой связи между виртуальной сети, где были подготовлены виртуальных машин и виртуальной сети, в котором выполняется контроллер домена (DC). См. в разделе [Создание пиринга виртуальных сетей — Resource Manager, в разных подписках](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Причина 2.** При использовании AadService (AADS), DNS-записи не были установлены.

**Исправление 2:** Чтобы установить доменные службы, см. в разделе [Включение доменных служб Active Directory Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Виртуальный рабочий стол агента Windows и виртуальных рабочих столов Windows Boot Loader не установлены

Рекомендуемый способ подготовки виртуальных машин с помощью Azure Resource Manager **Создание и подготовка виртуального рабочего стола Windows для размещения пула** шаблона. Шаблон автоматически устанавливает виртуального рабочего стола агента Windows и Windows виртуального рабочего стола агента Boot Loader.

Выполните эти инструкции, чтобы убедиться, что компоненты устанавливаются и проверить наличие сообщений об ошибках.

1. Убедитесь, что эти два компонента устанавливаются путем возврата **панели управления** > **программы** > **программы и компоненты**. Если **виртуального рабочего стола агента Windows** и **Windows виртуального рабочего стола агента Boot Loader** они не видны, они не установлены на виртуальной Машине.
2. Откройте **проводнике** и перейдите к **C:\Windows\Temp\scriptlogs.log**. Если файл отсутствует, значит DSC PowerShell, установленную в эти два компонента не может запустить в предоставленного контекста безопасности.
3. Если файл **C:\Windows\Temp\scriptlogs.log** — отсутствует, откройте его и проверьте наличие сообщений об ошибках.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Ошибка: Отсутствуют виртуального рабочего стола агента Windows и Windows виртуального рабочего стола агента Boot Loader. C:\Windows\Temp\scriptlogs.log также отсутствует

**Причина 1**. Указаны неверные учетные данные, предоставленные во время ввода для шаблона Azure Resource Manager или разрешения были недостаточно.

**Исправление 1:** Вручную добавить недостающие компоненты на виртуальные машины с помощью [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Причина 2.** DSC PowerShell была возможность запуска и выполнения, но не удалось завершить, так как он не может войти в виртуальный рабочий стол Windows и получить необходимые сведения.

**Исправление 2:** Подтвердите элементы в списке ниже.

- Убедитесь, что учетная запись не имеет многофакторной проверки Подлинности.
- Убедитесь, что точное имя клиента, и клиент присутствует в виртуальный рабочий стол Windows.
- Подтвердите учетная запись имеет по крайней мере разрешения участника служб удаленных рабочих СТОЛОВ.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Ошибка: Проверка подлинности не пройдена, ошибки в C:\Windows\Temp\scriptlogs.log

**Причина.** DSC PowerShell была возможность выполнения, но не удалось подключиться к виртуальному рабочему столу Windows.

**Исправление:** Подтвердите элементы в списке ниже.

- Вручную зарегистрируйте виртуальные машины в службе виртуальный рабочий стол Windows.
- Убедитесь, что учетная запись, используемая для подключения к виртуальному рабочему столу Windows имеет разрешения на клиенте, чтобы создать пулы узлов.
- Убедитесь, что у учетной записи многофакторной проверки Подлинности.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Виртуальный рабочий стол агент Windows не регистрируется в службе виртуальный рабочий стол Windows

При первой установке агента виртуального рабочего стола Windows в сеансе размещения виртуальных машин (либо вручную или с помощью шаблона Azure Resource Manager и PowerShell DSC), он предоставляет маркер регистрации. В следующем разделе описано устранение неполадок агента виртуального рабочего стола Windows и маркер.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Ошибка: Отображается состояние, зарегистрированные в командлете Get-RdsSessionHost состояние недоступно

![Командлет Get-RdsSessionHost показано состояние как недоступный.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Причина.** Агент не может получить ее обновление до новой версии.

**Исправление:** Следуйте этим инструкциям, чтобы вручную обновить агент.

1. Скачайте новую версию агента на узле сеанс виртуальной Машины.
2. Запустите диспетчер задач и на вкладке «службы» остановите службу RDAgentBootLoader.
3. Запустите установщик для новой версии агента виртуального рабочего стола Windows.
4. При появлении запроса на маркер регистрации, удалите запись INVALID_TOKEN и нажмите кнопку Далее (новый маркер не требуется).
5. Выполните мастер установки.
6. Откройте диспетчер задач и запустите службу RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Ошибка:  Запись реестра Windows виртуального рабочего стола агента IsRegistered содержит значение 0

**Причина.** Маркер регистрации истек или был создан со значением срока действия 999999.

**Исправление:** Следуйте этим инструкциям, чтобы устранить ошибку раздела реестра агента.

1. Если маркер регистрации уже существует, удалите ее с помощью Remove-RDSRegistrationInfo.
2. Создайте новый маркер с NewRegistrationInfo служб удаленных рабочих столов.
3. Убедитесь, что параметр - ExpriationHours присвоено 72 (максимальное значение равно 99 999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Ошибка: Виртуальный рабочий стол Windows агент не отправляет отчеты периодический сигнал при выполнении Get-RdsSessionHost

**Причина 1**. RDAgentBootLoader служба остановлена.

**Исправление 1:** Запустите диспетчер задач и, если вкладка «службы» сообщает о RDAgentBootLoader службы остановлена, запустите службу.

**Причина 2.** Может быть закрыто порт 443.

**Исправление 2:** Следуйте этим инструкциям, чтобы открыть порт 443.

1. Подтвердите порт 443 открыт, скачав средство PSPing из [инструменты Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Установите PSPing на узле сеанс виртуальной Машины, где запущен агент.
3. Откройте командную строку от имени администратора и выполните приведенную ниже команду:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Подтвердите эту информацию PSPing получено от RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Устранение неполадок со стеком side-by-side виртуальный рабочий стол Windows

Стек side-by-side виртуальный рабочий стол Windows устанавливается автоматически вместе с Windows Server 2019. Используйте Microsoft Installer (MSI) для установки стека side-by-side на Microsoft Windows Server 2016 или Windows Server 2012 R2. Для Windows 10, стеке side-by-side виртуальный рабочий стол Windows включен с **enablesxstackrs.ps1**.

Существует три способа основной, стеке side-by-side получает установлена или настроена на виртуальные машины пула узлов сеанса:

- С помощью Azure Resource Manager **Создание и Подготовка нового пул узлов виртуального рабочего стола Windows** шаблона
- По включена и включить в главный образ
- Установлена или настроена вручную, на каждой виртуальной Машине (или с помощью расширения или PowerShell)

Если у вас возникают проблемы со стеком side-by-side виртуального рабочего стола Windows, введите **qwinsta** команду в командной строке, чтобы убедиться, что стек side-by-side установлена или настроена.

Выходные данные **qwinsta** перечислит **rdp-sxs** в выходных данных, если стек side-by-side установлен и включен.

![Стек Side-by-side установлена или настроена с qwinsta, указанные как rdp-sxs в выходных данных.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Проверьте перечисленные ниже элементы реестра и убедитесь, что их значения соответствовали. Если отсутствуют ключи реестра или значения совпадают, следуйте инструкциям в [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) по повторной установке стека side-by-side.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Ошибка: O_REVERSE_CONNECT_STACK_FAILURE

![Код ошибки O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Причина.** Side-by-side стека не установлен на узле сеанс виртуальной Машины.

**Исправление:** Выполните эти инструкции, чтобы установить стек side-by-side на узле сеанс виртуальной Машины.

1. Позволяет получить непосредственно в учетную запись локального администратора виртуальной Машины узла сеансов удаленного рабочего стола (RDP).
2. Загрузите и импортируйте [Windows виртуального рабочего стола модуль PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell, если у вас еще нет.
3. Установите side-by-side стека с помощью [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Как устранить стек side-by-side виртуального рабочего стола Windows, который работает неправильно

Существуют известные обстоятельства, которые могут привести к неправильной работе стека side-by-side:

- Не подписаны на правильный порядок шагов для включения в стеке side-by-side
- Автоматическое обновление для Windows 10 усиленной универсальный диск (EVD)
- Отсутствует роль узла сеансов удаленных рабочих столов (RDSH)
- Запустив enablesxsstackrc.ps1 несколько раз
- Запуск enablesxsstackrc.ps1 в учетной записи без прав локального администратора

Инструкции в этом разделе помогут вам удалить side-by-side стека виртуального рабочего стола Windows. После удаления side-by-side стека, перейдите на «Зарегистрировать виртуальную Машину с узла рабочего стола пулом виртуальных Windows» [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) переустановка side-by-side стека.

Виртуальной Машины, используемой для запуска исправления должны находиться в одной подсети и домен, что и виртуальная машина со стеком неработающий side-by-side.

Выполните следующие инструкции для запуска исправления в той же подсети и домена.

1. Подключение с помощью стандартных удаленного рабочего стола (RDP) к виртуальной Машине из применения исправления.
2. Скачайте программы PsExec от https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Распакуйте скачанный файл.
4. Откройте командную строку от имени локального администратора.
5. Перейдите к папке, в которой был распакованный PsExec.
6. Из командной строки используйте следующую команду:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname — это имя компьютера виртуальной машины со стеком side-by-side неисправен.

7. Примите лицензионное соглашение PsExec, нажав кнопку Принимаю.

    ![Снимок экрана соглашения лицензии программного обеспечения.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Это диалоговое окно будет отображаться только время первого запуска PsExec.

8. Открыв сеанс командной строки на виртуальной Машине со стеком неработающий side-by-side, запустите qwinsta и проверьте, доступен запись с именем rdp-sxs. В противном случае в стек side-by-side отсутствует на виртуальной Машине, чтобы проблема не привязана к стеку side-by-side.

    ![Командную строку администратора](media/AdministratorCommandPrompt.png)

9. Выполните следующую команду, где будут перечислены компоненты Microsoft на виртуальной Машине со стеком side-by-side неисправен.

    ```cmd
        wmic product get name
    ```

10. Выполните команду ниже с названиями продуктов в предыдущем шаге.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Удалить все продукты, которые начинаются с «Удаленный рабочий стол».

12. После удаления всех компонентов виртуального рабочего стола Windows следуйте инструкциям для используемой операционной системы:

13. Если вашей операционной системой является Windows Server, перезапустите виртуальную Машину оказывали неработающий side-by-side стека (либо с помощью портала Azure или с помощью средства PsExec).

Если операционной системе Microsoft Windows 10, выполните приведенные ниже инструкции:

14. Виртуальной машине под управлением программы PsExec откройте проводник и скопируйте disablesxsstackrc.ps1 на системный диск виртуальной машины со стеком malfunctioned side-by-side.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname — это имя компьютера виртуальной машины со стеком side-by-side неисправен.

15. Рекомендуемый процесс: средством PsExec, запустите PowerShell и перейдите к папке, из предыдущего шага и запустите disablesxsstackrc.ps1. Кроме того можно выполнить следующие командлеты:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Когда все готово командлеты запущена, перезапустите виртуальную Машину со стеком side-by-side неисправен.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок виртуального рабочего стола Windows и отслеживает эскалации, см. в разделе [Устранение неполадок, Обзор, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Чтобы устранить неполадки при создании клиента и узла пула в среде виртуального рабочего стола Windows, см. в разделе [клиента и узла пул создания](troubleshoot-set-up-issues.md).
- Чтобы устранить неполадки при настройке виртуальной машины (VM) в виртуальный рабочий стол Windows, см. в разделе [конфигурацию виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).
- Чтобы устранить неполадки с помощью клиентских подключений виртуального рабочего стола Windows, см. в разделе [клиента к удаленному рабочему столу](troubleshoot-client-connection.md).
- Чтобы устранить неполадки при использовании PowerShell с помощью виртуального рабочего стола Windows, см. в разделе [виртуального рабочего стола PowerShell Windows](troubleshoot-powershell.md).
- Дополнительные сведения о предварительной версии службы, см. в разделе [Windows Desktop предварительной среде](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Сведения об устранении неполадок см. в статье [Tutorial: Устраняйте неполадки развертывания шаблонов Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).