---
title: Подключайте данные из Fortinet предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel Fortinet данных.
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
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611300"
---
# <a name="connect-your-fortinet-appliance"></a>Подключение устройства Fortinet

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания. Мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно подключить Azure Sentinel любое устройство Fortinet путем сохранения файлов журналов в качестве системного журнала общий формат событий (CEF). Благодаря интеграции с Azure Sentinel позволяет выполнять простую аналитики и запросы на данные журнала из Fortinet. Дополнительные сведения о том, как Azure Sentinel принимает данные CEF, см. в разделе [CEF, подключение устройств](connect-common-event-format.md).

> [!NOTE]
> Данные хранятся в рабочей области, на котором выполняется Azure Sentinel географическое расположение.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Шаг 1. Подключение устройства Fortinet с помощью агента

Для подключения устройства Fortinet Azure Sentinel, развертывание агента на выделенной виртуальной Машине или на локальном компьютере для поддержки связи между устройством и Azure Sentinel. Вы можете развернуть агент автоматически или вручную. Автоматическое развертывание доступно только в том случае, если выделенный машины новой виртуальной Машины, создаваемые в Azure.

Вы также можете развернуть агент вручную на существующей виртуальной Машины Azure, на виртуальной Машине в другом облаке или на локальном компьютере.

Сетевой график обоих вариантов см. в разделе [подключить источники данных](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Развертывание агента в Azure

1. На портале Azure Sentinel выберите **соединители данных** и выберите тип устройства.

1. В разделе **конфигурация агента Linux Syslog**:
   - Выберите **автоматического развертывания** Если вы хотите создать новый компьютер, который предустанавливается с агентом Azure Sentinel и включает в себя все настройки, необходимой, как описано выше. Выберите **авторазвертывания** > **агент автоматического развертывания**. Откроется страница покупки для выделенной виртуальной Машине, автоматически подключается к рабочей области. Виртуальная машина находится **стандартный D2s v3 (2 виртуальных ЦП, память объемом 8 ГБ)** и общедоступный IP-адрес.
      1. На **настраиваемое развертывание** странице, предоставляют подробные сведения, введите имя пользователя и пароль и, если вы согласны с указанными условиями, приобретите виртуальной Машины.
      1. Настройка устройства для отправки журналов, используя параметры, перечисленные на странице подключения. Для соединителя универсального общий формат событий используйте следующие параметры:
         - Протокол = UDP
         - Порт = 514
         - Средство = Local-4
         - Формат = CEF
   - Выберите **развертывания вручную** Если вы хотите использовать существующую виртуальную Машину в качестве выделенной машине Linux, на котором установлен агент Azure Sentinel. 
      1. В разделе **Загрузка и установка агента Syslog**выберите **виртуальной машины Azure Linux**. 
      1. На **виртуальных машин** выберите машину, вы хотите использовать и выберите **Connect**.
      1. На экране соединитель в разделе **Настройка и прямой Syslog**, задайте того, является ли ваш управляющую программу Syslog **rsyslog.d** или **syslog-ng**. 
      1. Скопируйте эти команды и запускайте их в устройстве:
          - Если вы выбрали rsyslog.d:
              
            1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel через порт 25226. Используйте следующую команду: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. Используйте следующую команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить на идентификатор GUID рабочей области.
            1. Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service rsyslog restart`
             
          - Если вы выбрали syslog-ng:

              1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel через порт 25226. Используйте следующую команду: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. Используйте следующую команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить на идентификатор GUID рабочей области.
              1. Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service syslog-ng restart`
      1. Перезапустите агент системного журнала с помощью следующей команды: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Убедитесь, что ошибки в журнале агента, выполнив следующую команду: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Развертывание агента на сервере Linux на предприятии

Если вы не используете Azure, вручную разверните агент Azure Sentinel для запуска на выделенном сервере Linux.

1. На портале Azure Sentinel выберите **соединители данных** и выберите тип устройства.
1. Чтобы создать выделенной виртуальной Машине Linux, в разделе **конфигурация агента Linux Syslog** выберите **развертывания вручную**.

    1. В разделе **Загрузка и установка агента Syslog**выберите **машины Linux в Azure не**.
    1. В **Direct agent** экран, на котором окне выберите **агент для Linux** скачайте агент или выполните следующую команду, чтобы скачать его на компьютере Linux: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. На экране «соединитель» в разделе **Настройка и прямой Syslog**, задайте того, является ли ваш управляющую программу Syslog **rsyslog.d** или **syslog-ng**.
       1. Скопируйте эти команды и запускайте их в устройстве:

          - Если вы выбрали rsyslog:

            1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel с помощью порт 25226. Используйте следующую команду: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. Используйте следующую команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить на идентификатор GUID рабочей области.
            1. Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service rsyslog restart`

          - Если вы выбрали syslog-ng:

            1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel через порт 25226. Используйте следующую команду: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. Используйте следующую команду: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` где {0} следует заменить на идентификатор GUID рабочей области.
            1. Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service syslog-ng restart`

      1. Перезапустите агент системного журнала с помощью следующей команды: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Убедитесь, что ошибки в журнале агента, выполнив следующую команду: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Шаг 2. Накат журналов Fortinet агенту системного журнала

Настройте Fortinet для пересылки сообщения системного журнала в формате CEF в рабочую область Azure с помощью системного журнала агента.

1. Откройте интерфейс командной строки на устройстве Fortinet и выполните следующие команды:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Заменить сервер **IP-адрес** IP-адрес агента.
    - Задайте **название** для использования средства, вы настроили в агенте. По умолчанию агент устанавливает это local4.
    - Задайте **порт syslog** для **514** или порт, который задается на агенте.
    - Чтобы включить в ранних версиях FortiOS поддерживает формат CEF, может потребоваться запустить набор команд **отключить csv**.
 
   > [!NOTE] 
   > Дополнительные сведения см. в статье [библиотеки документов Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Выберите версию и использовать **Handbook** и **Справочник по сообщениям журнала**.

 Для использования соответствующей схемы в Azure Monitor Log Analytics для событий Fortinet, поиск `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

Может занять до 20 минут, пока не журналов в Log Analytics. 

1. Убедитесь, что используется средство справа. Средство должны совпадать в устройстве и в Azure Sentinel. Вы можете проверить файл устройства, который вы используете в Azure Sentinel и изменить его в файле `security-config-omsagent.conf`. 

2. Убедитесь, что ваши журналы получают к правому порту системного журнала агента. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 514 -vv`. Эта команда показывает журналы, которые потоковой передачи из устройства к компьютеру системного журнала. Убедитесь, что журналы получаются из исходного устройства на нужный порт и средство справа.

3. Убедитесь, что журналы отправки соответствуют [форматы RFC 5424](https://tools.ietf.org/html/rfc542).

4. На компьютере запущен агент системного журнала, убедитесь, что порты 514 и 25226, открыт и осуществляет прослушивание с помощью команды `netstat -a -n:`. Дополнительные сведения об использовании этой команды см. в разделе [netstat(8) - странице руководства Linux](https://linux.die.net/man/8/netstat). Если он ожидает передачи данных правильно, вы увидите:

   ![Azure Sentinel порты](./media/connect-cef/ports.png) 

5. Убедитесь, что управляющая программа устанавливается для прослушивания по порту 514, на котором вы отправляете журналы.
    - Для rsyslog:<br>Убедитесь, что файл `/etc/rsyslog.conf` эта конфигурация включает в себя:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Дополнительные сведения см. в разделе [imudp: Входной модуль UDP системного журнала](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) и [imtcp: Входной модуль системного журнала TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Для syslog-ng:<br>Убедитесь, что файл `/etc/syslog-ng/syslog-ng.conf` эта конфигурация включает в себя:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Дополнительные сведения см. в разделе [imudp: Входной модуль UDP системного журнала](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) и [edition открытым исходным кодом syslog-ng 3,16 - руководство по администрированию](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Убедитесь, что обмен данными между управляющую программу Syslog и агент. Выполните следующую команду на компьютере агента системного журнала: `tcpdump -A -ni any  port 25226 -vv`. Эта команда показывает журналы, которые потоковой передачи из устройства к компьютеру системного журнала. Убедитесь, что журналы также получаются на агенте.

6. Если оба этих команд результаты без ошибок, проверьте см. в разделе, если поступают журналы в Log Analytics. Отображаются все события, передаваемые из этих устройств в необработанном виде в Log Analytics в разделе `CommonSecurityLog` типа.

7. Проверьте наличие ошибок или если журналы не поступающих, найдите в `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Если отображается надпись ошибок несоответствие формата журнала, перейдите на страницу `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` и просмотрите файл `security_events.conf`. Убедитесь, что ваши журналы соответствует формату регулярных выражений в этот файл.

8. Убедитесь, что размер по умолчанию сообщения системного журнала ограничена 2048 байт (2 КБ). Если журналы имеют слишком большую длину, обновите security_events.conf с помощью следующей команды: `message_length_limit 4096`

10. Если журналы Fortinet не, полученных агент, выполните следующую команду, в зависимости от того, какой тип управляющей программы системного журнала, вы используете, чтобы задать средство и журналы для поиска слова Fortinet в журналах:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Перезапустите управляющую программу системного журнала с помощью следующей команды: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Следующие шаги
В этой статье вы узнали, как подключиться к Azure Sentinel Fortinet устройств. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

