---
title: Подключение компьютеров Linux к Azure Monitor | Документация Майкрософт
description: В этой статье описывается подключение компьютеров Linux, размещенных в других облаках или в локальной среде, к Azure Monitor с помощью агента Log Analytics для Linux.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/24/2019
ms.openlocfilehash: c06a7551a5c0f14be94ed14072b81c189e359aa8
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75542002"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Подключение компьютеров Linux к Azure Monitor

Чтобы отслеживать виртуальные машины или физические компьютеры и управлять ими в локальном центре обработки данных или в другой облачной среде с Azure Monitor, необходимо развернуть агент Log Analytics и настроить его для передачи отчетов в рабочую область Log Analytics. Агент также поддерживает гибридную рабочую роль Runbook для службы автоматизации Azure.

Агент Log Analytics для Linux можно установить с помощью одного из следующих методов. Подробнее об использовании каждого метода рассказывается далее в этой статье.

* [Скачайте и установите агент вручную](#install-the-agent-manually) . Это необходимо, если компьютер Linux не имеет доступа к Интернету и будет взаимодействовать с Azure Monitor или службой автоматизации Azure через [шлюз log Analytics](gateway.md). 
* [Установите агент для Linux с помощью скрипта-оболочки,](#install-the-agent-using-wrapper-script) размещенного на GitHub. Этот метод рекомендуется использовать для установки и обновления агента, если компьютер подключен к Интернету напрямую или через прокси-сервер.

Дополнительные сведения о поддерживаемой конфигурации см. в разделах о [поддерживаемых операционных системах Linux](log-analytics-agent.md#supported-linux-operating-systems) и [требованиях к сетевым брандмауэрам](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>Агент Log Analytics для Linux невозможно настроить для отправки отчетов в несколько рабочих областей Log Analytics. Он может быть настроен только для одновременного отчета как к группе управления System Center Operations Manager, так и к Log Analytics рабочей области одновременно либо по отдельности.

## <a name="agent-install-package"></a>Пакет установки агента

Агент Log Analytics для Linux состоит из нескольких пакетов. Файл выпуска содержит следующие пакеты, доступные для запуска пакета оболочки с параметром `--extract`:

**Пакет** | **Версия** | **Описание**
----------- | ----------- | --------------
omsagent | 1.12.15 | Агент Log Analytics для Linux
omsconfig | 1.1.1 | Агент конфигурации для агента Log Analytics
omi | 1.6.3 | Открытая инфраструктура управления (OMI) — Облегченный сервер CIM. *Обратите внимание, что OMI требует наличия корневого доступа для выполнения задания cron, необходимого для функционирования службы.*
scx | 1.6.3 | Поставщики OMI CIM для метрик производительности операционной системы
apache-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера Apache HTTP Server для OMI. Устанавливается только при обнаружении сервера Apache HTTP Server
mysql-cimprov | 1.0.1 | Поставщик мониторинга производительности сервера MySQL для OMI. Устанавливается только при обнаружении сервера MySQL или MariaDB
docker-cimprov | 1.0.0 | Поставщик Docker для OMI. Устанавливается только при обнаружении Docker

### <a name="agent-installation-details"></a>Сведения об установке агента

После установки агента Log Analytics для пакетов Linux применяются следующие дополнительные изменения конфигурации на уровне системы. Эти артефакты удаляются после удаления пакета omsagent.

* Создается непривилегированный пользователь с именем `omsagent` . Управляющая программа запускается с этими учетными данными. 
* Файл, *включаемый* в sudo, создается в `/etc/sudoers.d/omsagent`. Это разрешает `omsagent` перезапустить управляющие программы syslog и omsagent. Если в установленной версии sudo не поддерживаются директивы sudo *include* , эти записи будут записаны в `/etc/sudoers`.
* Конфигурация системного журнала настраивается для перенаправления подмножества событий агенту. Дополнительные сведения см. в разделе [Настройка сбора данных системного журнала](data-sources-syslog.md).

На отслеживаемом компьютере Linux агент указан как `omsagent`. `omsconfig` — агент конфигурации агента Log Analytics для Linux, который выполняет поиск новой конфигурации на стороне портала каждые 5 минут. Новая и обновленная конфигурация применяется к файлам конфигурации агента, расположенным в `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области

Перед установкой агента Log Analytics для Linux требуется получить идентификатор и ключ для рабочей области Log Analytics. Эти сведения необходимы во время установки агента для правильной настройки и обеспечения возможности взаимодействия с Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Щелкните **Все службы** в левом верхнем углу на портале Azure. В поле поиска введите **Log Analytics**. По мере ввода символов список отфильтруется соответствующим образом. Выберите **Рабочие области Log Analytics**.

2. В списке рабочих областей Log Analytics выберите созданную ранее рабочую область. (Возможно, она называется **DefaultLAWorkspace**.)

3. Выберите **Дополнительные параметры**.

    ![Меню дополнительных параметров Log Analytics на портале Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Выберите **Подключенные источники**, а затем выберите **Серверы с Linux**.

5. Необходимые значения указаны справа от полей **Идентификатор рабочей области** и **Первичный ключ**. Скопируйте их и вставьте в любой удобный для вас редактор.

## <a name="install-the-agent-manually"></a>Установка агента вручную

Агент Log Analytics для Linux предоставляется в виде самораспаковывающегося и устанавливаемого пакета сценариев оболочки. Этот пакет содержит пакеты Debian и RPM для каждого из компонентов агента. Его можно установить непосредственно или извлечь отдельные пакеты. Один пакет предоставляется для архитектуры x64 и One для архитектур x86. 

Для виртуальных машин Azure рекомендуется установить агент на них с помощью [расширения виртуальной машины azure log Analytics](../../virtual-machines/extensions/oms-linux.md) для Linux. 

1. Перенесите соответствующий пакет (x86 или x64) на виртуальную машину Linux или физический компьютер с помощью SCP или SFTP.

2. Установите пакет с помощью аргумента `--install`. Чтобы подключить рабочую область Log Analytics во время установки, укажите параметры `-w <WorkspaceID>` и `-s <workspaceKey>`, скопированные ранее.

    >[!NOTE]
    >Необходимо использовать аргумент `--upgrade`, если установлены зависимые пакеты, такие как OMI, SCX, omsconfig или их старые версии, как и в случае, если агент System Center Operations Manager для Linux уже установлен. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Чтобы настроить агент Linux для установки и подключения к рабочей области Log Analytics через шлюз Log Analytics, выполните следующую команду, указав параметры прокси-сервера, идентификатора рабочей области и ключа рабочей области. Эту конфигурацию можно указать в командной строке, включив `-p [protocol://][user:password@]proxyhost[:port]`. Свойство *проксихост* принимает полное доменное имя или IP-адрес сервера шлюза log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Если требуется проверка подлинности, необходимо указать имя пользователя и пароль. Пример. 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Чтобы настроить компьютер Linux для подключения к рабочей области Log Analytics в облаке Azure для государственных организаций, выполните следующую команду, указав идентификатор рабочей области и первичный ключ, скопированные ранее.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Если вы хотите установить пакеты агента и настроить его для передачи в определенную рабочую область Log Analytics позже, выполните следующую команду:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Если требуется извлечь пакеты агента из пакета, не устанавливая агент, выполните следующую команду:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Установка агента с помощью скрипта-оболочки

Ниже описано, как настроить установку агента для Log Analytics в Azure для государственных организаций Azure с помощью скрипта оболочки для компьютеров Linux, которые могут обмениваться данными напрямую или через прокси-сервер для загрузки агента, размещенного на GitHub, и установки агента.  

Если компьютер Linux должен взаимодействовать через прокси-сервер для Log Analytics, эту конфигурацию можно указать в командной строке, включив `-p [protocol://][user:password@]proxyhost[:port]`. Свойство *Protocol* принимает `http` или `https`, а свойство *проксихост* принимает полное ДОМЕНное имя или IP-адрес прокси-сервера. 

Например: `https://proxy01.contoso.com:30443`

Если в любом случае требуется проверка подлинности, необходимо указать имя пользователя и пароль. Например: `https://user01:password@proxy01.contoso.com:30443`

1. Чтобы настроить компьютер Linux для подключения к рабочей области Log Analytics, выполните следующую команду, указав идентификатор рабочей области и первичный ключ. Следующая команда скачивает агент, проверяет его контрольную сумму и устанавливает его.
    
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

Обновление с предыдущей версии, начиная с версии 1.0.0-47, поддерживается в каждом выпуске. Выполните установку с параметром `--upgrade`, чтобы обновить все компоненты агента до последней версии.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [управлением и обслуживанием агента log Analytics для Windows и Linux](agent-manage.md) , чтобы узнать о том, как перенастроить, обновить или удалить агент с виртуальной машины.

- Если во время установки или настройки агента возникают какие-либо проблемы, см. статью об [устранении неполадок с агентом Linux для Log Analytics](agent-linux-troubleshoot.md).
