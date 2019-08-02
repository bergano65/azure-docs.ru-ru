---
title: Подключение данных Fortinet к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Fortinet к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679284"
---
# <a name="connect-your-fortinet-appliance"></a>Подключение устройства Fortinet

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания. Мы не рекомендуем использовать его для рабочих нагрузок в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в разделе Дополнительные [условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете подключить метку Azure к любому устройству Fortinet, сохранив файлы журнала в формате общего события syslog (CEF). Благодаря интеграции с Sentinel Azure можно легко выполнять анализ и запросы к данным файла журнала из Fortinet. Дополнительные сведения о том, как Azure Sentinel принимает данные CEF, см. в разделе [Connect CEF Appliances](connect-common-event-format.md).

> [!NOTE]
> Данные хранятся в географическом расположении рабочей области, в которой выполняется метка Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Шаг 1.: Подключение устройства Fortinet с помощью агента

Чтобы подключить устройство Fortinet к Azure Sentinel, разверните Агент на выделенной виртуальной машине или на локальном компьютере, чтобы обеспечить взаимодействие между устройством и Sentinel. 

Вы также можете развернуть агент вручную на существующей ВИРТУАЛЬНОЙ машине Azure, на виртуальной машине в другом облаке или на локальном компьютере.

> [!NOTE]
> Обязательно настройте безопасность компьютера в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть для согласования с политикой безопасности корпоративной сети и изменить порты и протоколы в управляющей программе в соответствии с вашими требованиями. 

Чтобы просмотреть схему сети обоих вариантов, см. раздел [Подключение к источникам данных](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Развертывание агента

1. На портале Sentinel Azure щелкните **Data Connectors (соединители данных** ), выберите **Fortinet** , а затем **откройте страницу соединителя**. 

1. В разделе **Загрузка и установка агента системного журнала**выберите тип компьютера (Azure или локально). 
1. На открывшемся экране **виртуальные машины** выберите компьютер, который вы хотите использовать, и нажмите кнопку **подключить**.
1. Если выбрана **Загрузка и установка агента для виртуальных машин Linux в Azure**, выберите компьютер и нажмите кнопку **подключить**. Если выбрана **Загрузка и установка агента для виртуальных машин Linux, отличных от Azure**, на экране **Direct Agent** выполните сценарий в разделе **скачать и подключить агент для Linux**.
1. На экране соединителя в разделе **Настройка и пересылка системного журнала**укажите, является ли управляющая программа syslog **rsyslog. d** или **syslog-ng**. 
1. Скопируйте эти команды и запустите их на устройстве:
   - Если вы выбрали rsyslog. d:
            
     1. Сообщите управляющей программе системного журнала о необходимости прослушивания local_4а устройства и отправки сообщений Syslog агенту Sentinel Azure с помощью порта 25226. Используйте следующую команду:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , который настраивает агент syslog для прослушивания порта 25226. Используйте эту команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить GUID рабочей области.
     1. Перезапустите управляющую программу системного журнала с помощью следующей команды:`sudo service rsyslog restart`
            
   - Если вы выбрали syslog-ng:

      1. Сообщите управляющей программе системного журнала о необходимости прослушивания local_4а устройства и отправки сообщений Syslog агенту Sentinel Azure с помощью порта 25226. Используйте следующую команду:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , который настраивает агент syslog для прослушивания порта 25226. Используйте эту команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить GUID рабочей области.
      1. Перезапустите управляющую программу системного журнала с помощью следующей команды:`sudo service syslog-ng restart`
1. Перезапустите агент системного журнала с помощью следующей команды:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Убедитесь, что в журнале агента нет ошибок, выполнив следующую команду:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Шаг 2.: Пересылка журналов Fortinet в агент системного журнала

Настройте Fortinet для пересылки сообщений системного журнала в формате CEF в рабочую область Azure с помощью агента системного журнала.

1. Откройте интерфейс командной строки на устройстве Fortinet и выполните следующие команды:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Замените **IP-адрес** сервера IP-адресом агента.
    - Задайте **facility_name** для использования устройства, настроенного в агенте. По умолчанию агент присваивает этому параметру значение local4.
    - Задайте для **порта системного журнала** значение **514** или порт, установленный на агенте.
    - Чтобы включить формат CEF в ранних версиях Фортиос, может потребоваться выполнить команду Set **CSV Disabled**.
 
   > [!NOTE] 
   > Для получения дополнительных сведений перейдите в [библиотеку документов Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Выберите свою версию и воспользуйтесь справочной ссылкой и **сообщением журнала**.

 Чтобы использовать соответствующую схему в Azure Monitor Log Analytics для событий Fortinet, выполните поиск по `CommonSecurityLog`запросу.


## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

После того, как журналы начнут отображаться в Log Analytics, может пройти до 20 минут. 

1. Убедитесь, что вы используете правильное средство. Это устройство должно быть одинаковым в устройстве и в Azure Sentinel. Вы можете проверить, какой файл ссуды вы используете в Azure Sentinel, и изменить его в `security-config-omsagent.conf`файле. 

2. Убедитесь, что журналы поступают на правильный порт в агенте системного журнала. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 514 -vv`. Эта команда отображает журналы, которые потоковая передача с устройства на компьютер syslog. Убедитесь, что журналы получены с исходного устройства на правильном порте и на правильном устройстве.

3. Убедитесь, что отправляемые журналы соответствуют [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. На компьютере, на котором выполняется агент syslog, убедитесь, что порты 514 и 25226 открыты и прослушиваются с помощью `netstat -a -n:`команды. Дополнительные сведения об использовании этой команды см. в разделе [netstat (8) — Справочная страница Linux](https://linux.die.net/man/8/netstat). Если он прослушивается правильно, вы увидите:

   ![Порты Sentinel Azure](./media/connect-cef/ports.png) 

5. Убедитесь, что управляющая программа настроена на прослушивание порта 514, на котором вы отправляете журналы.
    - Для rsyslog:<br>Убедитесь, что файл `/etc/rsyslog.conf` содержит следующую конфигурацию:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Дополнительные сведения см. в [разделе имудп: Входной модуль](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) UDP syslog и [имткп: Входной модуль](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)системного журнала TCP.

   - Для syslog-ng:<br>Убедитесь, что файл `/etc/syslog-ng/syslog-ng.conf` содержит следующую конфигурацию:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Дополнительные сведения см. в [разделе имудп: Входной модуль](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) и [syslog-ng с открытым исходным кодом 3,16 — администрирование](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Убедитесь в наличии связи между управляющей программой syslog и агентом. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 25226 -vv`. Эта команда отображает журналы, которые потоковая передача с устройства на компьютер syslog. Убедитесь, что журналы также получаются на агенте.

6. Если обе команды предоставили успешные результаты, проверьте Log Analytics поступающие журналы. Все события, переданные из этих устройств в поток, отображаются в необработанном виде в log Analytics в разделе `CommonSecurityLog` тип.

7. Чтобы проверить наличие ошибок или отсутствие поступающих журналов, найдите `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Если это говорит о том, что обнаружены ошибки несоответствия `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` формата журнала, перейдите к `security_events.conf`файлу и проверьте его. Убедитесь, что журналы соответствуют формату регулярного выражения, который вы видите в этом файле.

8. Убедитесь, что размер сообщения системного журнала по умолчанию ограничен 2048 байтами (2 КБ). Если журналы имеют слишком большую длину, обновите security_events. conf с помощью следующей команды:`message_length_limit 4096`

10. Если журналы Fortinet не получены агентом, выполните эту команду в зависимости от типа управляющей программы syslog, которую вы используете, чтобы настроить средство и настроить журналы для поиска слова Fortinet в журналах:
       - rsyslog. d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Перезапустите управляющую программу системного журнала с помощью следующей команды:`sudo service rsyslog restart`
       - syslog-ng:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Перезапустите управляющую программу системного журнала с помощью следующей команды:`sudo service syslog-ng restart`

## <a name="next-steps"></a>Следующие шаги
Из этой статьи вы узнали, как подключить устройства Fortinet к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

