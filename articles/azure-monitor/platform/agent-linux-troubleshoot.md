---
title: Устранение неполадок агента Azure Log Analytics для Linux | Документация Майкрософт
description: Описание симптомов, причин и решений для наиболее распространенных проблем с помощью агента Log Analytics для Linux в Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 1c9d5b214d0c79f84372ba679db1cbd4a7ad9858
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372596"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Устранение неполадок с агентом Log Analytics для Linux 

Эта статья содержит справочные сведения по устранению ошибок, которые могут возникнуть в работе агента Log Analytics для Linux в Azure Monitor и предлагает возможные решения для их устранения.

Если ни одна из рекомендаций не поможет устранить проблему, вы можете использовать следующие каналы поддержки:

* Клиенты с поддержкой Premier могут открыть запрос на поддержку на странице [Premier](https://premier.microsoft.com/).
* Клиенты с соглашением на поддержку Azure могут открыть запрос на поддержку на [портале Azure](https://manage.windowsazure.com/?getsupport=true).
* Для диагностики проблем с OMI можно применить инструкции в [руководстве по устранению неполадок OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Можно [сообщить о проблеме в GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Посетите страницу отзывов в Log Analytics, чтобы просмотреть отправленные предложения и сообщения об ошибках [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) или оставить новый отзыв.  

## <a name="important-log-locations-and-log-collector-tool"></a>Расположение важных журналов и сборщик журналируемых данных

 Файл | Путь
 ---- | -----
 Файл журнала агента Log Analytics для Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log `
 Файл журнала конфигурации агента Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Мы рекомендуем вам использовать наше средство сборщика журналируемых данных, чтобы получать важные журналы, которые помогут в устранении неполадок и предоставят данные для отправки запроса в GitHub. Дополнительные сведения об этом средстве и его использовании вы найдете [здесь](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Важные файлы конфигурации

 Категория | Расположение файла
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf`, `/etc/rsyslog.conf` или `/etc/rsyslog.d/95-omsagent.conf`
 Производительность, Nagios, Zabbix, выходные данные Log Analytics и общие настройки агента | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Дополнительные конфигурации | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Измененные файлы конфигурации для счетчиков производительности и системный журнал будут перезаписаны при настройке сбора в [меню "Данные" в дополнительных параметрах Log Analytics](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) для рабочей области на портале Azure. Чтобы отключить конфигурацию для всех агентов, отключите сбор в разделе **Дополнительные параметры** Log Analytics, а для одного агента выполните следующую команду:  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>Коды ошибок установки

| Код ошибки | Значение |
| --- | --- |
| NOT_DEFINED | Подключаемый модуль auoms auditd не будет установлен, так как не установлены необходимые зависимости | Установить auoms не удалось, установите пакет auditd. |
| 2 | Набору оболочки предоставлен недопустимый параметр. Запустите `sudo sh ./omsagent-*.universal*.sh --help` для информации об использовании |
| 3 | Набору оболочки не предоставлен параметр. Выполните `sudo sh ./omsagent-*.universal*.sh --help`, чтобы получить сведения об использовании. |
| 4. | Недопустимый тип пакета ИЛИ недопустимые настройки прокси-сервера; пакеты omsagent-*rpm*.sh можно установить только в системах на базе RPM, а пакеты omsagent-*deb*.sh — только в системах на базе Debian. Мы рекомендуем использовать универсальный установщик из [последнего выпуска](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Кроме того, просмотрите и проверьте настройки прокси-сервера. |
| 5 | Набор оболочки должен выполняться от имени привилегированного пользователя, ИЛИ получена ошибка 403 во время подключения. Выполните команду с использованием `sudo`. |
| 6 | Недопустимая архитектура пакета, ИЛИ получена ошибка 200 во время подключения; пакеты omsagent-*x64.sh можно установить только в 64-разрядных системах, а пакеты omsagent-* x86.sh — только в 32-разрядных системах. Скачайте правильный пакет для используемой архитектуры из [последнего выпуска](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Не удалось установить пакет OMS. Просмотрите выходные данные команды, чтобы определить причину сбоя. |
| 19 | Не удалось установить пакет OMI. Просмотрите выходные данные команды, чтобы определить причину сбоя. |
| 20 | Не удалось установить пакет SCX. Просмотрите выходные данные команды, чтобы определить причину сбоя. |
| 21 | Не удалось установить комплекты поставщика. Просмотрите выходные данные команды, чтобы определить причину сбоя. |
| 22 | Не удалось установить объединенный пакет. Просмотрите выходные данные команды, чтобы найти причину сбоя. |
| 23 | Пакет SCX или OMI уже установлен. Используйте `--upgrade` вместо `--install`, чтобы установить пакет оболочки. |
| 30 | Внутренняя ошибка пакета. Сообщите об [ошибке на сайте Github](https://github.com/Microsoft/OMS-Agent-for-Linux/issues), предоставив подробные сведения из выходных данных. |
| 55 | Неподдерживаемый openssl версии и не могут подключиться к Azure Monitor или dpkg является заблокированы или отсутствуют curl программы. |
| 61 | Отсутствует библиотека ctypes для Python. Установите библиотеку ctypes Python или соответствующий пакет (python-ctypes). |
| 62 | Отсутствует программа tar, установите tar. |
| 63 | Отсутствует программа sed, установите sed. |
| 64 | Отсутствует программа curl, установите curl. |
| 65 | Отсутствует программа gpg, установите gpg. |

## <a name="onboarding-error-codes"></a>Коды ошибок подключения

| Код ошибки | Значение |
| --- | --- |
| 2 | Скрипту omsadmin предоставлен недопустимый параметр. Выполните `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h`, чтобы получить сведения об использовании. |
| 3 | Скрипту omsadmin предоставлена недопустимая конфигурация. Выполните `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h`, чтобы получить сведения об использовании. |
| 4. | Скрипту omsadmin предоставлена недопустимый прокси-сервер. Проверьте прокси-сервер и изучите [документацию по использованию прокси-сервера HTTP](log-analytics-agent.md#network-firewall-requirements). |
| 5 | Ошибка 403 HTTP получен из Azure Monitor. Изучите выходные данные скрипта omsadmin, чтобы получить подробные сведения. |
| 6 | Ошибка HTTP Non-200, полученных из Azure Monitor. Изучите выходные данные скрипта omsadmin, чтобы получить подробные сведения. |
| 7 | Не удалось подключиться к Azure Monitor. Изучите выходные данные скрипта omsadmin, чтобы получить подробные сведения. |
| 8 | Ошибка подключения к рабочей области Log Analytics. Изучите выходные данные скрипта omsadmin, чтобы получить подробные сведения. |
| 30 | Внутренняя ошибка скрипта. Сообщите об [ошибке на сайте Github](https://github.com/Microsoft/OMS-Agent-for-Linux/issues), предоставив подробные сведения из выходных данных. |
| 31 | Ошибка при создании идентификатора агента. Сообщите об [ошибке на сайте Github](https://github.com/Microsoft/OMS-Agent-for-Linux/issues), предоставив подробные сведения из выходных данных. |
| 32 | Ошибка создания сертификата. Изучите выходные данные скрипта omsadmin, чтобы получить подробные сведения. |
| 33 | Ошибка при создании метаконфигурации для omsconfig. Сообщите об [ошибке на сайте Github](https://github.com/Microsoft/OMS-Agent-for-Linux/issues), предоставив подробные сведения из выходных данных. |
| 34 | Скрипт создания метаконфигурации отсутствует. Повторите попытку подключения с помощью `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Включение ведения журнала отладки
### <a name="oms-output-plugin-debug"></a>Отладка подключаемого модуля выходных данных OMS
 FluentD позволяет указывать уровни ведения журнала для конкретных подключаемых модулей, в том числе разные уровни ведения журнала для входных и выходных данных. Чтобы изменить уровень ведения журнала для выходных данных OMS, измените общую конфигурацию агента в файле `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 В конфигурации подключаемого модуля выходных данных OMS, почти в конца файла, измените свойство `log_level`, заменив `info` значением `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Ведение журнала отладки позволяет просматривать групповые отправки данных в Azure Monitor, по типу, количество элементов данных и время, необходимое для отправки:

*Пример включенного журнала отладки.*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Подробные выходные данные
Вместо подключаемого модуля выходных данных OMS элементы данных можно отправлять непосредственно в `stdout`, который отображается в файле журнала агента Log Analytics для Linux.

В файле общей конфигурации агента Log Analytics в разделе `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` закомментируйте подключаемый модуль выходных данных OMS, добавив в начало каждой строки символ `#`.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Ниже раздела подключаемого модуля раскомментируйте следующий раздел, удалив `#` перед каждой строкой:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Проблема  Не удается подключиться через прокси-сервера в Azure Monitor

### <a name="probable-causes"></a>Возможные причины
* При подключении указан недопустимый прокси-сервер.
* Azure Monitor и конечные точки службы автоматизации Azure не добавлены в список разрешений в вашем центре обработки данных 

### <a name="resolution"></a>Способы устранения:
1. Повторно подключитесь к Azure Monitor с агентом Log Analytics для Linux, используя следующую команду с параметром `-v` включена. Она позволяет подробные выходные данные агента могут подключиться через прокси-сервер в Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Просмотрите раздел [Обновление параметров прокси-сервера](agent-manage.md#update-proxy-settings), чтобы убедиться в правильности настройки агента для обмена данными через прокси-сервер.    
* Внимательно проверьте, что следующие конечные точки Azure Monitor, список разрешений:

    |Ресурс агента| порты; | Направление |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Порт 443| Исходящий и входящий |  
    |*.oms.opinsights.azure.com | Порт 443| Исходящий и входящий |  
    |*.blob.core.windows.net | Порт 443| Исходящий и входящий |  
    |*.azure-automation.net | Порт 443| Исходящий и входящий | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Проблема При попытке подключения возникает ошибка 403

### <a name="probable-causes"></a>Возможные причины
* На сервере Linux установлены неправильные время и дата. 
* Используется недопустимый идентификатор или ключ рабочей области.

### <a name="resolution"></a>Способы устранения:

1. Проверьте время на своем сервере Linux с помощью команды. Если время отличается от текущего на 15 минут, подключение завершится сбоем. Чтобы исправить это, обновите дату и (или) часовой пояс на сервере Linux. 
2. Убедитесь, что установлена последняя версия агента Log Analytics для Linux.  В последней версии вы получаете уведомления, если разница во времени приводит к сбою подключения.
3. Выполните повторное подключение, используя правильный идентификатор и ключ рабочей области и следуя инструкциям по установке, приведенным ранее в этой статье.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Проблема Сразу после подключения в файле журнала появляются ошибки 500 и 404
Это известная проблема, которая возникает при первой передаче данных Linux в рабочую область Log Analytics. Это не влияет на отправляемые данные и не мешает работе службы.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Проблема На портале Azure не отображаются данные

### <a name="probable-causes"></a>Возможные причины

- Сбой при подключении к Azure Monitor
- Подключение к Azure Monitor заблокировано
- Выполняется резервное копирование данных агента Log Analytics для Linux

### <a name="resolution"></a>Способы устранения:
1. Проверьте, если адаптации Azure Monitor прошла успешно, проверьте, если существует следующий файл: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Повторно подключитесь, используя инструкции командной строки `omsadmin.sh`
3. Если используется прокси-сервер, см. описанные выше шаги по разрешению прокси-сервера.
4. Иногда сбой подключения между агентом Log Analytics для Linux и службой связан с тем, что в буфере достигнут максимальный размер запросов к данным агента (50 МБ). Перезапустите агент с помощью следующей команды: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Эта проблема исправлена в агенте версии 1.1.0-28 и выше.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Проблема Не отображаются перенаправленные сообщения системного журнала 

### <a name="probable-causes"></a>Возможные причины
* В конфигурации сервера Linux не включен сбор данных об отправленных средствах и (или) данных об уровнях ведения журнала.
* Системный журнал не перенаправляется на сервер Linux должным образом.
* Количество сообщений, перенаправленных за одну секунду, слишком большое, и агент Log Analytics для Linux в базовой конфигурации не может их обработать.

### <a name="resolution"></a>Способы устранения:
* Убедитесь, что в рабочем пространстве Log Analytics для системного журнала предоставлены все средства и правильно настроены уровни ведения журнала. Изучите статью о [настройке сбора системного журнала на портале Azure](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal).
* Настройте получение перенаправленных сообщений в управляющих программах обмена сообщениями системных журналов (`rsyslog`, `syslog-ng`).
* Отключите блокировку сообщений в параметрах брандмауэра на сервере системного журнала.
* Смоделируйте отправку сообщения системного журнала в Log Analytics с помощью команды `logger`.
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Проблема Возникает ошибка address already in use in omsagent log file (адрес уже используется в файле журнала агента OMS).
В omsagent.log. отображается сообщение `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>`.

### <a name="probable-causes"></a>Возможные причины
Это сообщение означает, что диагностическое расширение Linux (LAD) устанавливается параллельно с расширением Log Analytics для виртуальной машины Linux и системный журнал с omsagent используют один и тот же порт.

### <a name="resolution"></a>Способы устранения:
1. Войдите с правами привилегированного пользователя и выполните следующие команды (обратите внимание, что значение 25224 указано только для примера и в вашей среде LAD может использовать другой порт):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    После этого измените нужный файл конфигурации `rsyslogd` или `syslog_ng`, изменив настройки для LAD таким образом, чтобы запись выполнялась через порт 25229.

2. Если виртуальная машина выполняет `rsyslogd`, нужно редактировать файл `/etc/rsyslog.d/95-omsagent.conf` (если он существует) или `/etc/rsyslog`. Если виртуальная машина выполняет `syslog_ng`, нужно редактировать файл `/etc/syslog-ng/syslog-ng.conf`.
3. Перезапустите omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Перезапустите службу системного журнала.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Проблема Не удается удалить omsagent с помощью действия очистки

### <a name="probable-causes"></a>Возможные причины

* Установлено диагностическое расширение для Linux
* Диагностическое расширение для Linux было установлено, а затем удалено, но вы по-прежнему получаете сообщение об ошибке со сведениями о том, что omsagent используется mdsd и не может быть удален.

### <a name="resolution"></a>Способы устранения:
1. Удалите диагностическое расширение Linux (LAD).
2. Удалите файлы диагностического расширения Linux с компьютера, если они есть в расположениях `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` и `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Проблема Не отображаются данные Nagios 

### <a name="probable-causes"></a>Возможные причины
* У пользователя omsagent нет разрешений на чтение данных из файла журнала Nagios.
* Источник и фильтр Nagios не раскомментированы в файле omsagent.conf.

### <a name="resolution"></a>Способы устранения:
1. Предоставьте пользователю omsagent право на чтение из файла Nagios, выполнив эти [инструкции](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. В файле общей конфигурации агента Log Analytics, в области кода `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` убедитесь, что **обе** строки источника и фильтра Nagios раскомментированы.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Проблема Не отображаются данные Linux 

### <a name="probable-causes"></a>Возможные причины
* Сбой при подключении к Azure Monitor
* Подключение к Azure Monitor заблокировано
* Виртуальная машина перезапущена
* Пакет OMI вручную обновлен до более новой версии, чем установленная из пакета агента Log Analytics для Linux
* Сообщение об ошибке *Не найден класс* для журналов ресурсов DSC в файле журнала `omsconfig.log`
* Выполняется резервное копирование данных агента Log Analytics
* DSC записывает ошибку *Current configuration does not exist. Execute Start-DscConfiguration command with -Path parameter to specify a configuration file and create a current configuration first.* (Текущая конфигурация не существует. Сначала выполните команду Start-DscConfiguration с параметром -Path, чтобы указать файл конфигурации и создать текущую конфигурацию.) в файл журнала `omsconfig.log`, но в журнале нет записей об операциях `PerformRequiredConfigurationChecks`.

### <a name="resolution"></a>Способы устранения:
1. Установите все зависимости, например пакет auditd.
2. Проверьте, если подключение к Azure Monitor прошла успешно, проверьте, если существует следующий файл: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Если подключение не удалось установить, повторите попытку, используя [инструкции](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) для команды omsadmin.sh.
4. Если используется прокси-сервер, выполните описанные выше действия по устранению неполадок для прокси-сервера.
5. В некоторых системах распространения Azure управляющая программа сервера OMI (omid) не запускается после перезагрузки виртуальной машины. Это приводит к отсутствию данных из систем аудита, отслеживания изменений или управления обновлениями. В качестве обходного пути запустите сервер omi вручную, выполнив команду `sudo /opt/omi/bin/service_control restart`.
6. Если вы обновите пакет OMI вручную до более новой версии, его необходимо перезапустить вручную, чтобы агент Log Analytics продолжил работу. Этот шаг необходим для некоторых дистрибутивов, в которых сервер OMI не запускается автоматически после обновления. Выполните `sudo /opt/omi/bin/service_control restart`, чтобы перезапустить OMI.
7. Если для ресурса DSC в файле omsconfig.log отобразится сообщение об ошибке *Не найден класс*, выполните `sudo /opt/omi/bin/service_control restart`.
8. В некоторых случаях, когда агент Log Analytics для Linux не может взаимодействовать с Azure Monitor на агенте резервного копирования данных по размеру полный буфер: (50 МБ). В этом случае перезапустите агент с помощью команды `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Эта проблема исправлена в агенте версии 1.1.0-28 и более новых версий.
    >

* Если файл журнала `omsconfig.log` не содержит сведений о том, что в системе периодически выполняются операции `PerformRequiredConfigurationChecks`, это может указывать на проблемы со службой или заданием cron. Убедитесь, что задание cron есть в разделе `/etc/cron.d/OMSConsistencyInvoker`. При необходимости выполните следующие команды, чтобы создать задание cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Кроме того, убедитесь, что служба cron запущена. Чтобы проверить состояние этой службы, в Debian, Ubuntu и SUSE используйте `service cron status`, а в RHEL, CentOS и Oracle Linux — `service crond status`. Если службы нет, установите для нее двоичные файлы и запустите службу с помощью команд:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Проблема При настройке на портале сбора данных для счетчиков производительности системного журнала или Linux не применяются указанные параметры

### <a name="probable-causes"></a>Возможные причины
* Агент Log Analytics для Linux не применяет последнюю конфигурацию
* Не применены изменения настроек, внесенные на портале

### <a name="resolution"></a>Способы устранения:
**Базовая информация:** агент Log Analytics `omsconfig` для агента конфигурации Linux каждые пять минут выполняет поиск новых параметров конфигурации на стороне портала. Найденная конфигурация переносится в файлы конфигурации агента Log Analytics для Linux в следующем расположении: /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Иногда агент Log Analytics для Linux не может установить связь со службой конфигурации портала, в результате чего последние изменения не применяются.
  1. Убедитесь, что агент `omsconfig` установлен, выполнив `dpkg --list omsconfig` или `rpm -qi omsconfig`.  Если он не установлен, заново установите последнюю версию агента Log Analytics для Linux.

  2. Убедитесь, что `omsconfig` агент может взаимодействовать с Azure Monitor, выполнив следующую команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Эта команда возвращает конфигурацию, которую агент получает от портала, в том числе параметры системного журнала, счетчиков производительности Linux и пользовательских журналов. Если выполнить эту команду не удается, используйте команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Эта команда принудительно выполняет агента omsconfig поговорить с Azure Monitor и получить последнюю конфигурацию.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Проблема Не отображаются данные пользовательских журналов 

### <a name="probable-causes"></a>Возможные причины
* Сбой при подключении к Azure Monitor.
* Не установлен флажок **Apply the following configuration to my Linux Servers** (Применить следующую конфигурацию к моим серверам Linux).
* Агент omsconfig не получил от портала последнюю конфигурацию пользовательских журналов.
* Пользователь агента Log Analytics для Linux `omsagent` не имеет доступа к настраиваемому журналу из-за неправильных разрешений или отсутствия файла.  Могут отображаться следующие сообщения об ошибках:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Известная проблема состояния гонки исправлена в агенте Log Analytics для Linux версии 1.1.0-217.

### <a name="resolution"></a>Способы устранения:
1. Убедитесь, подключение к Azure Monitor выполнено успешно, проверив, если существует следующий файл: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Если его нет, выполните одно из следующих действий:  

  1. Повторите попытку подключения, используя [инструкции](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) для команды omsadmin.sh.
  2. Убедитесь, что в разделе **Дополнительные параметры** на портале Azure установлен флажок **Apply the following configuration to my Linux Servers** (Применить следующую конфигурацию к моим серверам Linux).  

2. Убедитесь, что `omsconfig` агент может взаимодействовать с Azure Monitor, выполнив следующую команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Эта команда возвращает конфигурацию, которую агент получает от портала, в том числе параметры системного журнала, счетчиков производительности Linux и пользовательских журналов. Если выполнить эту команду не удается, используйте команду `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Эта команда принудительно выполняет агента omsconfig поговорить с Azure Monitor и получить последнюю конфигурацию.

**Базовая информация.** Вместо привилегированного пользователя (`root`) для выполнения агента Log Analytics для Linux используется пользователь `omsagent`. В большинстве случаев этому пользователю необходимо предоставить явное разрешение на чтение определенных файлов. Чтобы предоставить разрешения пользователю `omsagent`, выполните следующие команды:

1. Добавьте пользователя `omsagent` в определенную группу `sudo usermod -a -G <GROUPNAME> <USERNAME>`.
2. Предоставьте универсальный доступ на чтение требуемого файла `sudo chmod -R ugo+rx <FILE DIRECTORY>`.

Есть известная проблема с состоянием гонки в агенте Log Analytics для Linux версий старше 1.1.0-217. После обновления агента до последней версии выполните приведенную далее команду, чтобы получить последнюю версию подключаемого модуля выходных данных `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Проблема Вы пытаетесь повторно подключиться к новой рабочей области
Перед попыткой повторно подключить агент к новой рабочей области необходимо очистить конфигурацию агента Log Analytics. Чтобы удалить из агента старую конфигурацию, выполните пакет оболочки с помощью `--purge`.

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
или

```
sudo sh ./onboard_agent.sh --purge
```

Продолжите повторную установку подключения, применив параметр `--purge`.

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Для расширения агента Log Analytics на портале Azure зафиксировано состояние сбоя: Provisioning failed (Сбой подготовки)

### <a name="probable-causes"></a>Возможные причины
* Агент log Analytics удален из операционной системы.
* Служба агента Log Analytics не работает, отключена или не настроена.

### <a name="resolution"></a>Способы устранения: 
Выполните описанные ниже действия, чтобы устранить проблему.
1. Удалите расширение с помощью портала Azure.
2. Установите агент в соответствии с [этими инструкциями](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Перезапустите агент, выполнив следующую команду: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Подождите несколько минут, пока для состояния не отобразится значение **Provisioning succeeded** (Подготовка выполнена успешно).


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Проблема Агент Log Analytics требует выполнить обновление

### <a name="probable-causes"></a>Возможные причины

Устарели пакеты агента Log Analytics на узле.

### <a name="resolution"></a>Способы устранения: 
Выполните описанные ниже действия, чтобы устранить проблему.

1. Проверьте наличие новой версии на [этой странице](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Скачайте скрипт установки (здесь указан пример для версии 1.4.2-124):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Обновите пакеты, выполнив команду `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
