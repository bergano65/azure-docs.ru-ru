---
title: Подключение данных Cisco к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Cisco к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: e4df594128a119f38c66796d7b00a30420a2a0bd
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679326"
---
# <a name="connect-your-cisco-asa-appliance"></a>Подключение устройства Cisco ASA 

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете подключить метку Azure к любому устройству Cisco ASA. Cisco ASA изначально интегрируется с Sentinel-маркером Azure для приема данных, поэтому даже несмотря на то, что устройство Cisco не сохраняет журналы как CEF, Azure Sentinel принимает их таким же образом, как и журналы CEF. Интеграция с Azure Sentinel позволяет легко выполнять анализ и запросы к данным файла журнала из Cisco ASA. 

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>Шаг 1.: Подключение устройства Cisco ASA с помощью агента

Чтобы подключить устройство Cisco ASA к Azure Sentinel, необходимо развернуть агент на выделенном компьютере (виртуальной машине или в локальной среде) для поддержки взаимодействия между устройством и Sentinel. 

Вместо этого вы можете развернуть агент вручную на существующей виртуальной машине Azure, на виртуальной машине в другом облаке или на локальном компьютере.

> [!NOTE]
> Обязательно настройте безопасность компьютера в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть для согласования с политикой безопасности корпоративной сети и изменить порты и протоколы в управляющей программе в соответствии с вашими требованиями. 

Чтобы просмотреть схему сети обоих вариантов, см. раздел [Подключение к источникам данных](connect-data-sources.md).

### <a name="deploy-the-agent-on-your-machine"></a>Развертывание агента на компьютере

1. На портале Sentinel Azure щелкните **Data Connectors (соединители данных** ) и выберите **Cisco ASA** , а затем **откройте страницу соединителя**. 

1. В разделе **Загрузка и установка агента системного журнала**выберите тип компьютера (Azure или локально). 
1. На открывшемся экране **виртуальные машины** выберите компьютер, который вы хотите использовать, и нажмите кнопку **подключить**.
1. Если выбрана **Загрузка и установка агента для виртуальных машин Linux в Azure**, выберите компьютер и нажмите кнопку **подключить**. Если выбрана **Загрузка и установка агента для виртуальных машин Linux, отличных от Azure**, на экране **Direct Agent** выполните сценарий в разделе **скачать и подключить агент для Linux**.
1. На экране соединителя в разделе **Настройка и пересылка системного журнала**укажите, является ли управляющая программа syslog **rsyslog. d** или **syslog-ng**. 
1. Скопируйте эти команды и запустите их на устройстве:
    - Если вы выбрали rsyslog. d:
              
       1. Сообщите управляющей программе системного журнала о необходимости прослушивания local_4а устройства и отправки сообщений Syslog агенту Sentinel Azure с помощью порта 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , который настраивает агент syslog для прослушивания порта 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Где {0} следует заменить GUID рабочей области.
            
      1. Перезапуск управляющей программы системного журнала`sudo service rsyslog restart`
             
    - Если вы выбрали syslog-ng:

        1. Сообщите управляющей программе системного журнала о необходимости прослушивания local_4а устройства и отправки сообщений Syslog агенту Sentinel Azure с помощью порта 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
        2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , который настраивает агент syslog для прослушивания порта 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Где {0} следует заменить GUID рабочей области.

        3. Перезапуск управляющей программы системного журнала`sudo service syslog-ng restart`
1. Перезапустите агент системного журнала с помощью следующей команды:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Убедитесь, что в журнале агента нет ошибок, выполнив следующую команду:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>Шаг 2.: Пересылка журналов Cisco ASA в агент системного журнала

Cisco ASA не поддерживает CEF, поэтому журналы отправляются в виде системного журнала, и агент Sentinel Azure знает, как их анализировать, как если бы они были журналами CEF. Настройте Cisco ASA для пересылки сообщений Syslog в рабочую область Azure с помощью агента системного журнала:

Перейдите к разделу [Отправка сообщений системного журнала на внешний сервер syslog](https://aka.ms/asi-syslog-cisco-forwarding)и следуйте инструкциям по настройке подключения. При появлении запроса используйте следующие параметры:
- Задайте для параметра **порт** значение 514 или порт, заданный в агенте.
- Задайте для **syslog_ip** IP-адрес агента.
- Установите для регистратора средство, заданное в агенте. По умолчанию агент устанавливает для этого устройства значение 4.

Чтобы использовать соответствующую схему в Log Analytics для событий Cisco, выполните поиск по `CommonSecurityLog`запросу.

## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

1. Убедитесь, что вы используете правильное средство. Это устройство должно быть одинаковым в устройстве и в Azure Sentinel. Вы можете проверить, какой файл ссуды вы используете в Azure Sentinel, и изменить его в `security-config-omsagent.conf`файле. 

2. Убедитесь, что журналы поступают на правильный порт в агенте системного журнала. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 514 -vv`Эта команда отображает журналы, которые передаются с устройства на компьютер syslog. Убедитесь, что журналы получены с исходного устройства в правильном и правом портах.

3. Убедитесь, что отправляемые журналы соответствуют [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. На компьютере, на котором выполняется агент системного журнала, убедитесь, что порты 514, 25226 открыты и ожидают использования команды `netstat -a -n:`. Дополнительные сведения об использовании этой команды см. в разделе [netstat (8) — Справочная страница Linux](https://linux.die.net/man/8/netstat). Если он прослушивается правильно, вы увидите следующее:

   ![Порты Sentinel Azure](./media/connect-cef/ports.png) 

5. Убедитесь, что управляющая программа настроена на прослушивание порта 514, на котором вы отправляете журналы.
    - Для rsyslog:<br>Убедитесь, что файл `/etc/rsyslog.conf` содержит следующую конфигурацию:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Дополнительные сведения см. в [разделе имудп: Входной модуль](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) UDP syslog и [имткп: Входной модуль syslog для TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Для syslog-ng:<br>Убедитесь, что файл `/etc/syslog-ng/syslog-ng.conf` содержит следующую конфигурацию:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Дополнительные сведения см. в разделе [syslog-ng с открытым исходным кодом 3,16-администрирование](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Убедитесь в наличии связи между управляющей программой syslog и агентом. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 25226 -vv`Эта команда отображает журналы, которые передаются с устройства на компьютер syslog. Убедитесь, что журналы также получаются на агенте.

6. Если обе команды предоставили успешные результаты, проверьте Log Analytics поступающие журналы. Все события, переданные из этих устройств в поток, отображаются в необработанном виде в log Analytics в разделе `CommonSecurityLog` тип.

7. Чтобы проверить наличие ошибок или отсутствие поступающих журналов, найдите `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Если это говорит о том, что обнаружены ошибки несоответствия `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` формата журнала, перейдите к `security_events.conf`файлу и проверьте, соответствуют ли журналы формату регулярного выражения, который вы видите в этом файле.

8. Убедитесь, что размер сообщения системного журнала по умолчанию ограничен 2048 байтами (2 КБ). Если журналы имеют слишком большую длину, обновите security_events. conf с помощью следующей команды:`message_length_limit 4096`




## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить устройства Cisco ASA к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

