---
title: Подключайте данные CEF предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данные CEF.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 51fd1195942a7bae86bb4cc0af9df3146d6e45c2
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921910"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Подключите решение внешнего с использованием Cef

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel можно подключиться с внешним решением, дающий возможность сохранения файлов журналов в системном журнале. Если ваше устройство позволяет сохранить журналы как Syslog общий формат событий (CEF), интеграция с Azure Sentinel позволяет легко выполнять аналитики и запросы по данным.

> [!NOTE] 
> Данные хранятся в рабочей области, на котором выполняется Azure Sentinel географическое расположение.

## <a name="how-it-works"></a>Принцип работы

Соединение между Azure Sentinel и вашим устройством CEF происходит в три этапа:

1. На устройстве, необходимо задать эти значения, таким образом, устройство отправляет необходимые журналы в необходимом формате агенту Azure Sentinel Syslog. Эти параметры можно изменить в устройстве, до тех пор, пока вы также изменить их в управляющей программы системного журнала на агенте Azure Sentinel.
    - Протокол = UDP
    - Порт = 514
    - Средство = Local-4
    - Формат = CEF
2. Агент системного журнала собирает данные и отправляет их в Log Analytics, где он анализируется и насыщенные безопасно.
3. Агент сохраняет данные в рабочую область Log Analytics, поэтому его можно запрашивать при необходимости, с помощью панелей мониторинга, правила корреляции и анализа.


## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Шаг 1. Подключиться к вашим устройством CEF через выделенной виртуальной Машине Azure

Вам необходимо развернуть агент на выделенном компьютере Linux (виртуальной Машине или на локальном компьютере) для поддержки связи между устройством и Azure Sentinel. Вы можете развернуть агент автоматически или вручную. Автоматическое развертывание на основании шаблонов Resource Manager и может использоваться только в том случае, если выделенный компьютер Linux является новой виртуальной Машины, создаваемой в Azure.

 ![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Вместо этого вы можете развернуть агент вручную на существующей виртуальной машине Azure, на виртуальной машине в другом облаке или на локальном компьютере. 

 ![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Развертывание агента в Azure


1. На портале Azure Sentinel щелкните **соединители данных** и выберите тип устройства. 

1. В разделе **конфигурация агента Linux Syslog**:
   - Выберите **автоматического развертывания** Если вы хотите создать новый компьютер, который устанавливается с агентом Azure Sentinel и включает в себя все необходимые настройки, как описано выше. Выберите **авторазвертывания** и нажмите кнопку **агент автоматического развертывания**. Вы перейдете на страницу покупки для выделенной виртуальной Машине Linux, в которой автоматически подключается к рабочей области, является. Виртуальная машина находится **стандартный D2s v3 (2 виртуальных ЦП, память объемом 8 ГБ)** и общедоступный IP-адрес.
      1. В **настраиваемое развертывание** странице предоставляют подробные сведения и выберите имя пользователя и пароль и если вы согласны с указанными условиями, приобрести виртуальную Машину.
      1. Настройка устройства для отправки журналов, используя параметры, перечисленные на странице подключения. Для соединителя универсального общий формат событий используйте следующие параметры:
         - Протокол = UDP
         - Порт = 514
         - Средство = Local-4
         - Формат = CEF
   - Выберите **развертывания вручную** Если вы хотите использовать существующую виртуальную Машину в качестве выделенной машине Linux, на который должен быть установлен агент Azure Sentinel. 
      1. В разделе **Загрузка и установка агента Syslog**выберите **виртуальной машины Azure Linux**. 
      1. В **виртуальных машин** открывшемся экране выберите машину, вы хотите использовать и нажмите кнопку **Connect**.
      1. На экране «соединитель» в разделе **Настройка и прямой Syslog**, задайте того, является ли ваш управляющую программу Syslog **rsyslog.d** или **syslog-ng**. 
      1. Скопируйте эти команды и запускайте их в устройстве:
          - Если вы выбрали rsyslog.d:
              
            1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel с помощью порт 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Где {0} следует заменить на идентификатор GUID рабочей области.
            
            1. Перезапустите управляющую программу системного журнала `sudo service rsyslog restart`<br> Дополнительные сведения см. в разделе [rsyslog документации](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Если вы выбрали syslog-ng:

              1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel с помощью порт 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Где {0} следует заменить на идентификатор GUID рабочей области.

              3. Перезапустите управляющую программу системного журнала `sudo service syslog-ng restart` <br>Дополнительные сведения см. в разделе [syslog-ng документации](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. Перезапустите агент системного журнала с помощью следующей команды: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Убедитесь, что ошибки в журнале агента, выполнив следующую команду: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 Чтобы использовать соответствующей схемы в Log Analytics для события CEF, поиск `CommonSecurityLog`.


### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Развернуть агент на локальном сервере Linux

Если вы не используете Azure, вручную разверните агент Azure Sentinel для запуска на выделенном сервере Linux.


1. На портале Azure Sentinel щелкните **соединители данных** и выберите тип устройства.
1. Чтобы создать выделенной виртуальной Машине Linux, в разделе **конфигурация агента Linux Syslog** выберите **развертывания вручную**.
   1. В разделе **Загрузка и установка агента Syslog**выберите **машины Linux в Azure не**. 
   1. В **Direct agent** экран, на котором окне выберите **агент для Linux** скачайте агент или выполните следующую команду, чтобы скачать его на компьютере Linux:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. На экране «соединитель» в разделе **Настройка и прямой Syslog**, задайте того, является ли ваш управляющую программу Syslog **rsyslog.d** или **syslog-ng**. 
      1. Скопируйте эти команды и запускайте их в устройстве:
         - Если вы выбрали rsyslog:
           1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel с помощью порт 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Где {0} следует заменить на идентификатор GUID рабочей области.
           3. Перезапустите управляющую программу системного журнала `sudo service rsyslog restart`
         - Если вы выбрали syslog-ng:
            1. Сообщите управляющую программу Syslog для прослушивания по помещению local_4 и отправлять сообщения системного журнала агента Azure Sentinel с помощью порт 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Скачайте и установите [файл конфигурации security_events](https://aka.ms/asi-syslog-config-file-linux) , предназначенный для настройки агента системного журнала на порт 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Где {0} следует заменить на идентификатор GUID рабочей области.
            3. Перезапустите управляющую программу системного журнала `sudo service syslog-ng restart`
      1. Перезапустите агент системного журнала с помощью следующей команды: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Убедитесь, что ошибки в журнале агента, выполнив следующую команду: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
  
 Чтобы использовать соответствующей схемы в Log Analytics для события CEF, поиск `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

Может потребоваться до 20 минут, пока не журналов в Log Analytics. 

1. Убедитесь, что используется средство справа. Средство должны совпадать в устройстве и в Azure Sentinel. Вы можете проверить файл устройства, который вы используете в Azure Sentinel и изменить его в файле `security-config-omsagent.conf`. 

2. Убедитесь, что ваши журналы получают к правому порту системного журнала агента. На компьютере агента системного журнала, выполните следующую команду: `tcpdump -A -ni any  port 514 -vv` Эта команда показывает журналы, которые выполняет потоковую передачу с устройства к компьютеру системного журнала. Убедитесь, что журналы получаются из исходного устройства на нужный порт и правом помещения.

3. Убедитесь, что журналы отправки соответствуют [форматы RFC 5424](https://tools.ietf.org/html/rfc542).

4. На компьютере с агентом системного журнала, убедитесь, что эти порты 514, 25226 открыт и осуществляет прослушивание с помощью команды `netstat -a -n:`. Дополнительные сведения об использовании этой команды см. в разделе [netstat(8) - странице руководства Linux](https://linux.die.netman/8/netstat). Если он ожидает должным образом, вы увидите следующее:

   ![Azure Sentinel порты](./media/connect-cef/ports.png) 

5. Убедитесь, что управляющая программа устанавливается для прослушивания по порту 514, на котором вы отправляете журналы.
    - Для rsyslog:<br>Убедитесь, что файл `/etc/rsyslog.conf` эта конфигурация включает в себя:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Дополнительные сведения см. в разделе [imudp: Модуль ввода UDP системного журнала](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) и [imtcp: Модуль TCP входные данные системного журнала](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Для syslog-ng:<br>Убедитесь, что файл `/etc/syslog-ng/syslog-ng.conf` эта конфигурация включает в себя:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Дополнительные сведения см. в разделе [imudp: UDP системного журнала входных данных модуль] (Дополнительные сведения см. в разделе [3,16 выпуска откройте источник для syslog-ng - руководство по администрированию](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Убедитесь, что обмен данными между управляющую программу Syslog и агент. На компьютере агента системного журнала, выполните следующую команду: `tcpdump -A -ni any  port 25226 -vv` Эта команда показывает журналы, которые выполняет потоковую передачу с устройства к компьютеру системного журнала. Убедитесь, что журналы также получаются на агенте.

6. Если оба этих команд результаты без ошибок, проверьте см. в разделе, если поступают журналы в Log Analytics. Отображаются все события, передаваемые из этих устройств в необработанном виде в Log Analytics в разделе `CommonSecurityLog` типа.

7. Проверьте наличие ошибок или если журналы не поступающих, найдите в `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Если отображается надпись ошибок несоответствие формата журнала, перейдите на страницу `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` и просмотрите файл `security_events.conf`и убедитесь, что журналы соответствует формату регулярных выражений, появится в этом файле.

8. Убедитесь, что размер по умолчанию сообщения системного журнала ограничена 2048 байт (2 КБ). Если журналы имеют слишком большую длину, обновите security_events.conf, с помощью следующей команды: `message_length_limit 4096`


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel CEF устройств. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

