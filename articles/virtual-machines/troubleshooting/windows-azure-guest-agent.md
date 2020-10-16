---
title: Устранение неполадок гостевого агента Windows Azure
description: Устранение неполадок гостевого агента Windows Azure — нерабочие проблемы
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 738c2a240ad6c88186357e69b02d33b40d366d7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977071"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Устранение неполадок гостевого агента Windows Azure

Гостевой агент Windows Azure — это агент виртуальной машины. Она позволяет виртуальной машине взаимодействовать с контроллером структуры (базовым физическим сервером, на котором размещена виртуальная машина) на IP-адресе 168.63.129.16. Это виртуальный общедоступный IP-адрес, который упрощает обмен данными. Дополнительные сведения см. в разделе [что такое IP-адрес 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

 Для виртуальной машины, перенесенной в Azure из локальной среды или созданной с помощью настроенного образа, не установлен Гостевой агент Windows Azure. В этих сценариях необходимо вручную установить агент виртуальной машины. Дополнительные сведения об установке агента виртуальной машины см. в статье [Обзор агента виртуальной машины Azure](../extensions/agent-windows.md).

После успешной установки гостевого агента Windows Azure можно увидеть следующие службы, перечисленные в разделе Services. msc на виртуальной машине:
 
- Служба гостевого агента Windows Azure
- Служба телеметрии
- Служба агента удаленных рабочих столов

**Служба гостевого агента Windows Azure**. это служба, которая отвечает за все журналы в ваппажент. log. Эта служба отвечает за настройку различных расширений и обмен данными между гостевой системой и узлом. 

**Служба телеметрии**. Эта служба отвечает за отправку данных телеметрии виртуальной машины на внутренний сервер.

**Служба агента удаленных рабочих столов**. Эта служба отвечает за установку гостевого агента. Прозрачный установщик также является компонентом агента удаленных рабочих столов, который помогает обновлять другие компоненты и службы гостевого агента. Рдажент также отвечает за отправку пакетов пульса от гостевой виртуальной машины к агенту узла на физическом сервере.

> [!NOTE]
> Начиная с версии 2.7.41491.971 гостевого агента виртуальной машины компонент телеметрии включается в службу Рдажент, поэтому эта служба телеметрии может не отображаться в списке только что созданных виртуальных машин.

## <a name="checking-agent-status-and-version"></a>Проверка состояния и версии агента

Перейдите на страницу свойств виртуальной машины в портал Azure и проверьте **состояние агента**. Если Гостевой агент Windows Azure работает правильно, состояние отображается как **Готово**. Если агент виртуальной машины находится в состоянии " **не готов** ", **команды** "расширения" и "выполнить" на портал Azure не будут работать.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Устранение неполадок агента виртуальной машины, состояние которого не готов

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Шаг 1. Проверьте, установлена ли служба гостевого агента Windows Azure

- Проверка пакета

    Откройте папку К:\виндовсазуре. Если отображается папка нулевое с номером версии, это означает, что на виртуальной машине установлен Гостевой агент Windows Azure. Также можно найти установленный пакет.  Если на виртуальной машине установлен Гостевой агент Windows Azure, пакет будет сохранен в следующем расположении: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Чтобы проверить, развернут ли агент ВМ на виртуальной машине, можно выполнить следующую команду PowerShell:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    В выходных данных выберите свойство **провисионвмажент** и проверьте, установлено ли для этого параметра значение **true**. Если это так, это означает, что агент установлен на виртуальной машине.
    
- Проверка служб и процессов

   Перейдите в консоль служб (Services. msc) и проверьте состояние следующих служб: Служба гостевого агента Windows Azure, служба Рдажент, Служба телеметрии Windows Azure и служба сетевого агента Windows Azure.
 
    Вы также можете проверить, работают ли эти службы, изучив Диспетчер задач для следующих процессов:
       
    - WindowsAzureGuestAgent.exe: Служба гостевого агента Windows Azure
    - WaAppAgent.exe: служба Рдажент
    - WindowsAzureNetAgent.exe: служба сетевого агента Windows Azure
    - WindowsAzureTelemetryService.exe: Служба телеметрии Windows Azure

   Если вы не можете найти эти процессы и службы, это означает, что у вас не установлен Гостевой агент Windows Azure.

- Проверка программы и компонента

    На панели управления перейдите в раздел **программы и компоненты** , чтобы определить, установлена ли служба гостевого агента Windows Azure.

Если вы не нашли какие бы то ни было пакеты, службы и процессы и не видите установленный Гостевой агент Windows Azure в разделе "программы и компоненты", попробуйте [установить службу гостевого агента Windows Azure](../extensions/agent-windows.md). Если Гостевой агент не устанавливается должным образом, можно [установить агент виртуальной машины в автономном режиме](./install-vm-agent-offline.md).

Если вы видите службы и они работают, перезапустите службу, чтобы проверить, решена ли проблема. Если службы остановлены, запустите их и подождите несколько минут. Затем проверьте, сообщается ли **состояние агента** как **готовое**. Если вы обнаружите, что эти службы завершаются сбоем, некоторые сторонние процессы могут привести к сбою этих служб. Чтобы устранить эти проблемы, обратитесь в [Служба поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Шаг 2. Проверьте, работает ли автоматическое обновление

Гостевой агент Windows Azure имеет функцию автоматического обновления. Он автоматически будет проверять наличие новых обновлений и устанавливать их. Если функция автоматического обновления работает неправильно, попробуйте удалить и установить Гостевой агент Windows Azure, выполнив следующие действия.

1. Если Гостевой агент Windows Azure отображается в окне " **программы и компоненты**", удалите Гостевой агент Windows Azure.

2. Откройте окно командной строки с правами администратора.

3. Завершите работу служб гостевого агента. Если службы не останавливаются, необходимо настроить службы для **запуска вручную** , а затем ПЕРЕзапустить виртуальную машину.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Удалите службы гостевого агента:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. В разделе `C:\WindowsAzure` Создание папки с именем Old.

1. Переместите все папки с именами Packages или нулевое в СТАРую папку.

1. Скачайте и установите последнюю версию пакета установки агента [отсюда](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409). Чтобы выполнить установку, необходимо иметь права администратора.

1. Установите Гостевой агент с помощью следующей команды:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Затем проверьте, правильно ли запущены службы гостевого агента.
 
    В редких случаях, когда Гостевой агент устанавливается неправильно, можно [установить агент виртуальной машины в автономном режиме](./install-vm-agent-offline.md).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Шаг 3. Проверьте, может ли виртуальная машина подключаться к контроллеру структуры

Используйте такое средство, как PsPing, чтобы проверить, может ли виртуальная машина подключаться к 168.63.129.16 через порты 80, 32526 и 443. Если виртуальная машина не подключается должным образом, проверьте, открыт ли Исходящая связь через порты 80, 443 и 32526 в локальном брандмауэре на виртуальной машине. Если этот IP-адрес заблокирован, агент виртуальной машины может отобразить непредвиденное поведение в различных сценариях.

## <a name="advanced-troubleshooting"></a>Расширенный поиск проблем

События для устранения неполадок гостевого агента Windows Azure записываются в следующие файлы журналов:

- к:\виндовсазуре\логс\вааппажент.лог
- к:\виндовсазуре\логс\транспарентинсталлер.лог

Ниже приведены некоторые распространенные сценарии, в которых Гостевой агент Windows Azure может переходить в состояние **неготовности** или перестают работать должным образом.

### <a name="agent-stuck-on-starting"></a>Агент заблокирован на "запуске"

В журнале Вааппажент можно увидеть, что агент зависает в процессе запуска и не может быть запущен.

**Сведения о журнале**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] Виндовсазурегуестажент starting. Версия 2.7.41491.901

**Анализ**
 
На виртуальной машине по-прежнему выполняется старая версия гостевого агента Windows Azure. В папке К:\виндовсазуре можно заметить, что установлены несколько экземпляров гостевого агента Windows Azure, включая несколько из одной версии. Поскольку установлено несколько экземпляров агента, виртуальная машина не запускает последнюю версию гостевого агента Windows Azure.

**Решение**

Вручную удалите Гостевой агент Windows Azure, а затем переустановите его, выполнив следующие действия.

1. Откройте панель управления > "программы и компоненты" и удалите Гостевой агент Windows Azure.
1. Откройте диспетчер задач и приостанавливаете следующие службы: Служба гостевого агента Windows Azure, служба Рдажент, Служба телеметрии Windows Azure и служба сетевого агента Windows Azure.
1. В разделе К:\виндовсазуре создайте папку с именем OLD.
1. Переместите все папки с именами Packages или нулевое в СТАРую папку. Кроме того, переместите все папки нулевое в К:\виндовсазуре\логс, которые начинаются с GuestAgent_x. x. xxxxx, в СТАРую папку.
1. Скачайте и установите последнюю версию агента MSI. Для завершения установки необходимо иметь права администратора.
1. Установите Гостевой агент с помощью следующей команды MSI:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Убедитесь, что Рдажент, Гостевой агент Windows Azure и службы телеметрии Windows Azure уже работают.
 
1. Проверьте Вааппажент. log, чтобы убедиться в том, что установлена последняя версия гостевого агента Windows Azure.
 
1. Удалите СТАРую папку в разделе К:\виндовсазуре.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Не удается подключиться к Виресервер IP (IP-адрес узла) 

Обратите внимание на следующие записи об ошибках в Вааппажент. log и телеметрии. log:

**Сведения о журнале**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Анализ**

Виртуальная машина не может связаться с сервером узла виресервер.

**Решение**

1. Так как виресервер недоступен, подключитесь к виртуальной машине с помощью удаленный рабочий стол и попытайтесь получить доступ к следующему URL-адресу из браузера: http://168.63.129.16/?comp=versions 
1. Если у вас нет доступа к URL-адресу из шага 1, проверьте сетевой интерфейс, чтобы определить, настроен ли он с поддержкой DHCP и содержит DNS. Чтобы проверить состояние DHCP сетевого интерфейса, выполните следующую команду:  `netsh interface ip show config` .
1. Если DHCP отключен, выполните следующую команду, чтобы изменить значение желтого на имя интерфейса: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Проверьте наличие проблем, которые могут быть вызваны брандмауэром, прокси-сервером или другим источником, который может блокировать доступ к IP-адресу 168.63.129.16.
1. Проверьте, блокирует ли брандмауэр Windows или брандмауэр стороннего производителя доступ к портам 80, 443 и 32526. Дополнительные сведения о том, почему этот адрес не должен блокироваться, см. в разделе [что такое IP Address 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

### <a name="guest-agent-is-stuck-stopping"></a>Гостевой агент завис "останавливается"  

Обратите внимание на следующие записи об ошибках в Вааппажент. log:

**Сведения о журнале** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Анализ**

Гостевой агент Windows Azure остановлен в процессе остановки.

**Решение**

1. Убедитесь, что на виртуальной машине работает WaAppAgent.exe. Если она не запущена, перезапустите службу рдгажент и подождите пять минут. Когда WaAppAgent.exe выполняется, завершите процесс WindowsAzureGuest.exe.
2. Если шаг 1 не решает проблему, удалите текущую установленную версию и установите последнюю версию агента вручную.

### <a name="npcap-loopback-adapter"></a>Адаптер замыкания на себя Npcap 

Обратите внимание на следующие записи об ошибках в Вааппажент. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Анализ**

Адаптер замыкания на себя Npcap устанавливается на виртуальной машине с помощью Wireshark. Wireshark — это инструмент с открытым исходным кодом для профилирования сетевого трафика и анализа пакетов. Такой инструмент часто называют анализатором сети, анализатором сетевых протоколов или средством прослушивания.

**Решение**

Адаптер замыкания на себя Npcap, вероятно, устанавливается с помощью WireShark. Попробуйте отключить его, а затем проверьте, устранена ли проблема.

## <a name="next-steps"></a>Дальнейшие действия

Для дальнейшей диагностики проблемы "Гостевой агент Windows Azure не работает" [обратитесь в службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).