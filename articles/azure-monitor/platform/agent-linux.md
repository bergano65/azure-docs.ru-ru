---
title: Подключение компьютеров Linux к azure Monitor (ru) Документы Майкрософт
description: В этой статье описывается, как подключить компьютеры Linux, размещенные в других облаках или на месте, к Azure Monitor с агентом Log Analytics для Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637524"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Подключение компьютеров Linux к Azure Monitor

Для мониторинга и управления виртуальными машинами или физическими компьютерами в локальном центре обработки данных или другой облачной среде с помощью Azure Monitor необходимо развернуть агент Log Analytics и настроить его для отчета в рабочее пространство Log Analytics. Агент также поддерживает гибридную рабочую роль Runbook для службы автоматизации Azure.

Агент Log Analytics для Linux может быть установлен с помощью одного из следующих методов. Подробнее об использовании каждого метода рассказывается далее в этой статье.

* [Вручную загрузите и установите](#install-the-agent-manually) агент. Это необходимо, если компьютер Linux не имеет доступа к Интернету и будет общаться с Azure Monitor или Azure Automation через [шлюз Log Analytics.](gateway.md) 
* [Установите агент для Linux с помощью обертки-скрипта,](#install-the-agent-using-wrapper-script) размещенного на GitHub. Это рекомендуемый метод для установки и обновления агента, когда компьютер имеет подключение к Интернету, непосредственно или через прокси-сервер.

Дополнительные сведения о поддерживаемой конфигурации см. в разделах о [поддерживаемых операционных системах Linux](log-analytics-agent.md#supported-linux-operating-systems) и [требованиях к сетевым брандмауэрам](log-analytics-agent.md#network-requirements).

>[!NOTE]
>Агент Log Analytics для Linux невозможно настроить для отправки отчетов в несколько рабочих областей Log Analytics. Он может быть настроен только для отчета как в группе управления менеджером по управлению операциями System Center, так и в рабочее пространство Log Analytics, или либо по отдельности.

## <a name="agent-install-package"></a>Пакет установки агента

Агент Log Analytics для Linux состоит из нескольких пакетов. Файл релиза содержит следующие пакеты, которые доступны при `--extract` запуске расслоения оболочки с параметром:

**Пакет** | **Version** | **Описание**
----------- | ----------- | --------------
omsagent | 1.12.15 | Агент аналитики журнала для Linux
omsconfig | 1.1.1 | Агент конфигурации для агента Log Analytics
omi | 1.6.3 | Открытая инфраструктура управления (OMI) - легкий CIM Server. *Обратите внимание, что OMI требует корневого доступа для выполнения задания, необходимого для функционирования службы*
scx | 1.6.3 | Поставщики OMI CIM для метрик производительности операционной системы
apache-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера Apache HTTP Server для OMI. Устанавливается только при обнаружении сервера Apache HTTP Server
mysql-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера MySQL для OMI. Устанавливается только при обнаружении сервера MySQL или MariaDB
docker-cimprov | 1.0.0 | Поставщик Docker для OMI. Устанавливается только при обнаружении Docker

### <a name="agent-installation-details"></a>Детали установки агента

После установки агента Log Analytics для пакетов Linux применяются следующие дополнительные изменения конфигурации в масштабах всей системы. Эти артефакты удаляются после удаления пакета omsagent.

* Создается непривилегированный пользователь с именем `omsagent` . Daemon работает под этим учетным данным. 
* Sudoers *включают* файл создается в `/etc/sudoers.d/omsagent`. Это разрешает `omsagent` перезапустить syslog и omsagent daemons. Если sudo *включить* директивы не поддерживаются в установленной версии sudo, эти записи будут написаны на `/etc/sudoers`.
* Конфигурация системного журнала настраивается для перенаправления подмножества событий агенту. Для получения дополнительной информации [см.](data-sources-syslog.md)

На контролируемом компьютере Linux агент `omsagent`указан как . `omsconfig`является агентом Log Analytics для агента конфигурации Linux, который ищет новую конфигурацию боковой системы портала каждые 5 минут. Новая и обновленная конфигурация применяется к `/etc/opt/microsoft/omsagent/conf/omsagent.conf`файлам конфигурации агента, расположенным по адресу.

## <a name="obtain-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области

Перед установкой агента Log Analytics для Linux требуется получить идентификатор и ключ для рабочей области Log Analytics. Эта информация необходима во время настройки агента, чтобы правильно настроить его и убедиться, что он может успешно общаться с Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Щелкните **Все службы** в левом верхнем углу на портале Azure. В поле поиска введите **Log Analytics**. По мере ввода символов список отфильтруется соответствующим образом. Выберите **Рабочие области Log Analytics**.

2. В списке рабочих областей Log Analytics выберите созданную ранее рабочую область. (Возможно, она называется **DefaultLAWorkspace**.)

3. Выберите **Расширенные настройки:**

    ![Меню дополнительных параметров Log Analytics на портале Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Выберите **Подключенные источники**, а затем выберите **Серверы с Linux**.

5. Необходимые значения указаны справа от полей **Идентификатор рабочей области** и **Первичный ключ**. Скопируйте их и вставьте в любой удобный для вас редактор.

## <a name="install-the-agent-manually"></a>Установка агента вручную

Агент Log Analytics для Linux предоставляется в комплекте сценария для самостоятельного извлечения и установки оболочки. Этот пакет содержит пакеты Debian и RPM для каждого из компонентов агента. Его можно установить непосредственно или извлечь отдельные пакеты. Один пучок предназначен для x64 и один для архитектуры x86. 

> [!NOTE]
> Для использующих VMs-сообщения Azure мы рекомендуем установить на них агент с помощью [расширения Azure Log Analytics VM](../../virtual-machines/extensions/oms-linux.md) для Linux. 

1. [Скачать](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) и передать соответствующий пакет (x64 или x86) на Linux VM или физический компьютер, используя scp/sftp.

2. Установите пакет, `--install` используя аргумент. Чтобы на борту в рабочее пространство Log `-w <WorkspaceID>` `-s <workspaceKey>` Analytics во время установки, предоставьте и параметры, скопированные ранее.

    >[!NOTE]
    >Вы должны использовать `--upgrade` аргумент, если какие-либо зависимые пакеты, такие как omi, scx, omsconfig или их старые версии установлены, как это было бы в случае, если агент управления операциями центра системы для Linux уже установлен. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Чтобы настроить агента Linux для установки и подключения к рабочему пространству Log Analytics через шлюз Log Analytics, запустите следующую команду, обеспечивающую прокси, идентификатор рабочего пространства и ключевые параметры рабочего пространства. Эта конфигурация может быть указана `-p [protocol://][user:password@]proxyhost[:port]`на командной строке, включив. Свойство *proxyhost* принимает полностью квалифицированное доменное имя или IP-адрес сервера шлюза Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Если требуется аутентификация, необходимо указать имя пользователя и пароль. Пример: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Чтобы настроить компьютер Linux для подключения к рабочей области Log Analytics в облаке Azure Government, запустите следующую команду, предоставляющую идентификатор рабочего пространства и основной ключ, скопированный ранее.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Если вы хотите установить пакеты агента и настроить их для отчета в определенное рабочее пространство Log Analytics на более позднее время, запустите следующую команду:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Если вы хотите извлечь пакеты агента из пакета без установки агента, запустите следующую команду:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Установите агент с помощью обертки скрипт

Следующие шаги настраивают настройку агента для анализа журналов в облаке Azure и Azure Government с помощью скрипта обертки для компьютеров Linux, которые могут общаться напрямую или через прокси-сервер для загрузки агента, размещенного на GitHub, и установки агента.  

Если компьютеру Linux необходимо общаться через прокси-сервер с журналом Analytics, `-p [protocol://][user:password@]proxyhost[:port]`эта конфигурация может быть указана в командной строке, включив. Свойство *протокола* принимает `http` или, `https`и *свойство proxyhost* принимает полно квалифицированное доменное имя или адрес IP proxy сервера. 

Например: `https://proxy01.contoso.com:30443`

Если в любом случае требуется аутентификация, необходимо указать имя пользователя и пароль. Например: `https://user01:password@proxy01.contoso.com:30443`

1. Чтобы настроить компьютер Linux для подключения к рабочей области Log Analytics, запустите следующую команду, предоставляющую идентификатор рабочего пространства и основной ключ. Следующая команда скачивает агент, проверяет его контрольную сумму и устанавливает его.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Следующая команда включает параметр `-p` прокси-сервера и пример синтаксиса, если для прокси-сервера требуется проверка подлинности.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Чтобы настроить компьютер Linux для подключения к рабочей области Log Analytics в облаке Azure для государственных организаций, выполните следующую команду, указав идентификатор рабочей области и первичный ключ, скопированные ранее. Следующая команда скачивает агент, проверяет его контрольную сумму и устанавливает его. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Следующая команда включает параметр `-p` прокси-сервера и пример синтаксиса, если для прокси-сервера требуется проверка подлинности.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Перезапустите агент, выполнив следующую команду. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Обновление предыдущей версии

Обновление от предыдущей версии, начиная с версии 1.0.0-47, поддерживается в каждом выпуске. Выполните установку `--upgrade` с параметром для обновления всех компонентов агента до последней версии.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [управление и обслуживание агента Log Analytics для Windows и Linux,](agent-manage.md) чтобы узнать о том, как перенастроить, обновить или удалить агента из виртуальной машины.

- Если во время установки или настройки агента возникают какие-либо проблемы, см. статью об [устранении неполадок с агентом Linux для Log Analytics](agent-linux-troubleshoot.md).
