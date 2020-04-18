---
title: Устранение неполадок при активации виртуальных машин Windows в Azure | Документация Майкрософт
description: Приводятся действия по устранению неполадок, возникающих при активации виртуальных машин Windows в Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: fd38f646b8dfc58839cd2645f7fadf7332693854
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605989"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Устранение неполадок при активации виртуальных машин Windows в Azure

Если при активации виртуальной машины Windows, созданной из пользовательского образа, в Azure возникли проблемы, то для их устранения можно воспользоваться сведениями, приведенными в этом документе. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Основные сведения о конечных точках сервера управления ключами Azure для активации продуктов Windows для виртуальных машин Azure

Azure использует различные конечные точки для активации KMS (Key Management Services) в зависимости от облачного региона, в котором находится VM. При работе с этим руководством по устранению неполадок используйте соответствующую конечную точку сервера управления ключами для своего региона.

* Регионы общедоступного облака Azure: kms.core.windows.net:1688.
* Регионы национального облака 21Vianet Azure для Китая: kms.core.chinacloudapi.cn:1688.
* Регионы национального облака Azure для Германии: kms.core.cloudapi.de:1688.
* Регионы национального облака Azure для US Gov организаций: kms.core.usgovcloudapi.net:1688.

## <a name="symptom"></a>Симптом

При попытке активировать виртуальную машину Windows в Azure появляется сообщение об ошибке примерно такого содержания:

**Ошибка: 0xC004F074 Программное обеспечение LicensingService сообщил, что компьютер не может быть активирован. Связаться с Key ManagementService (KMS) не удалось. Для получения дополнительной информации ознакомьтесь с журналом событий приложения.**

## <a name="cause"></a>Причина:

Как правило, неполадки при активации виртуальной машины в Azure возникают, если виртуальная машина Windows не настроена с помощью подходящего ключа установки клиента KMS или если возникает проблема при подключении виртуальной машины Windows к службе Azure KMS (kms.core.windows.net, порт 1688). 

## <a name="solution"></a>Решение

>[!NOTE]
>Если вы используете VPN от сайта к сайту и принудительное туннелирование, [см.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Если вы используете ExpressRoute и у вас есть маршрут по умолчанию опубликованы, см [Могу ли я заблокировать подключение к Интернету виртуальных сетей, подключенных к схемам ExpressRoute?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Шаг 1 Настройка соответствующего ключа настройки клиента KMS

Для VM, созданного на пользовательском изображении, необходимо настроить соответствующий ключ настройки клиента KMS для VM.

1. В командной строке с повышенными привилегиями выполните команду **slmgr.vbs /dlv**. В выходных данных проверьте значение Description (Описание), а затем определите, какой носитель лицензии использовался для создания — розничный (канал RETAIL) или корпоративный (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Если команда **slmgr.vbs /dlv** отображает канал RETAIL, то выполните следующие команды, чтобы задать [ключ установки клиента KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) для используемой версии Windows Server и принудительно повторить активацию: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Например, для Windows Server 2016 Datacenter необходимо выполнить следующую команду:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Шаг 2. Проверка сетевого подключения между виртуальной машиной и службой Azure KMS

1. Скачать и извлечь инструмент [PSping](https://docs.microsoft.com/sysinternals/downloads/psping) в локальную папку в VM, которая не активируется. 

2. Перейдите в меню "Пуск", найдите и щелкните правой кнопкой мыши Windows PowerShell, а затем выберите "Запуск от имени администратора".

3. Убедитесь, что в настройках виртуальной машины указан правильный сервер Azure KMS. Для этого выполните следующую команду:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Команда должна вернуть такие данные: "Задано имя компьютера со службой управления ключами: kms.core.windows.net:1688".

4. С помощью Psping проверьте наличие подключения к серверу KMS. Перейдите в папку, в которую был извлечен архив Pstools.zip, а затем выполните следующую команду:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   В предпоследней строке выходных данных должно отобразиться следующее: "отправлено = 4, получено = 4, потеряно = 0 (0% потерь)".

   Если значение "потеряно" больше ноля, то это значит, что виртуальная машина не имеет подключения к серверу KMS. В такой ситуации, если виртуальная машина находится в виртуальной сети и указан пользовательский DNS-сервер, необходимо убедиться, что DNS-сервер способен разрешать адрес kms.core.windows.net. Или укажите такой DNS-сервер, который точно разрешает kms.core.windows.net.

   Обратите внимание, что при удалении всех DNS-серверов из виртуальной сети виртуальные виртуальные виртуальные компании используют внутренний DNS-сервис Azure. Эта служба может разрешать kms.core.windows.net.
  
    Также убедитесь, что исходящий сетевой трафик в конечную точку KMS с портом 1688 не блокируется брандмауэром в VM.

5. Проверить с помощью [Network Watcher Next Hop,](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) что следующий тип хмеля от VM в вопросе к назначению IP 23.102.135.246 (для kms.core.windows.net) или IP соответствующей конечной точки KMS, которая применяется к вашему региону **Интернет**.  Если результатом является VirtualAppliance или VirtualNetworkGateway, вполне вероятно, что маршрут по умолчанию существует.  Свяжитесь с вашим сетевым админстратором и работайте с ним, чтобы определить правильный курс действий.  Это может быть [пользовательский маршрут,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) если это решение соответствует политикам организации.

6. После успешной проверки подключения к kms.core.windows.net выполните в командной строке с повышенными привилегиями Windows PowerShell следующую команду. Эта команда пытается выполнить активацию несколько раз.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Успешная попытка активации возвращает сведения, которые выглядят следующим образом:
    
    **Активация Windows (R), издание ServerDatacenter (12345678-1234-1234-1234-12345678) ...  Продукт активирован успешно.**

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Я создал Windows Server 2016 из Azure Marketplace. Нужно ли настраивать ключ KMS для активации Windows Server 2016? 

 
Нет. В образе из Azure Marketplace уже настроен подходящий ключ установки клиента KMS. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Процедура активации Windows будет такой же, если виртуальная машина использует программу преимуществ гибридного использования (HUB) Azure? 

 
Да. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Что произойдет, если истечет период активации Windows? 

 
Если льготный период истек, а ОС Windows еще не активирована, то в Windows Server 2008 R2 и более поздних версиях Windows отобразятся дополнительные уведомления об активации. Фоновый рисунок рабочего стола будет оставаться черным, а Центр обновления Windows будет устанавливать только обновления системы безопасности и критические обновления без возможности установить необязательные обновления. Ознакомьтесь с разделом "Notifications" (Уведомления) в нижней части страницы [Licensing Conditions](https://technet.microsoft.com/library/ff793403.aspx) (Условия лицензирования).   

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
