---
title: Разработка безопасного веб-приложения | Документация Майкрософт
description: В этом простом примере приложения реализованы рекомендации по обеспечению безопасности, которые улучшают приложение и уровень безопасности Организации при разработке в Azure.
keywords: строч
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 640900458eccc36afe58cb148ffd7b94b43be879
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934920"
---
# <a name="develop-a-secure-web-app"></a>Разработка безопасного веб-приложения

Этот пример представляет собой простое приложение Python, которое отображает веб-страницу со ссылками на ресурсы безопасности для разработки приложений в Azure. Приложение реализует рекомендации по обеспечению безопасности, которые помогут улучшить приложение и уровень безопасности Организации при разработке приложений в Azure.

Необходимо последовательно выполнить действия, описанные в этой статье, чтобы убедиться, что компоненты приложения настроены должным образом. База данных, служба приложений Azure, экземпляр Azure Key Vault и экземпляр шлюза приложений Azure зависят друг от друга.

Скрипты развертывания настроили инфраструктуру. После запуска скриптов развертывания необходимо выполнить некоторые настройки вручную в портал Azure, чтобы связать компоненты и службы вместе.

Пример приложения предназначен для начинающих разработку приложений в Azure, которые хотят реализовать меры безопасности в своих приложениях.

При разработке и развертывании этого приложения вы узнаете, как:

- Создайте экземпляр Azure Key Vault, сохраните и извлеките из него секреты.
- Разверните базу данных Azure для PostgreSQL, настройте защищенные пароли и Авторизуйте доступ к нему.
- Запустите контейнер Alpine Linux в веб-приложениях Azure для Linux и включите управляемые удостоверения для ресурсов Azure.
- Создайте и настройте экземпляр шлюза приложений Azure с брандмауэром, использующим набор [правил OWASP Top 10](https://coreruleset.org/).
- Включение шифрования передаваемых и неактивных данных с помощью служб Azure.

После разработки и развертывания этого приложения вы настроили следующий пример веб-приложения вместе с описанными мерами конфигурации и безопасности.

![Пример веб-приложения](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Архитектура
Приложение представляет собой типичное n-уровневое приложение с тремя уровнями. Внешний интерфейс, серверная часть и уровень базы данных с интегрированными компонентами мониторинга и управления секретами показаны здесь:

![Архитектура приложения](./media/secure-web-app/architecture.png)

Архитектура состоит из следующих компонентов:

- [Шлюз приложений Azure.](../../application-gateway/index.yml) Предоставляет шлюз и брандмауэр для нашей архитектуры приложения.
- [Веб-приложения Azure на платформе Linux](../../app-service/containers/app-service-linux-intro.md). Предоставляет среду выполнения контейнера для запуска приложения Python в среде Linux.
- [Azure Key Vault.](../../key-vault/index.yml) Сохраняет и шифрует секреты нашего приложения и управляет созданием политик доступа, связанных с ними.
- [База данных Azure для PostgreSQL](https://azure.microsoft.com/services/postgresql/). Обеспечивает безопасное хранение данных приложения.
- [Центр безопасности Azure](../../security-center/index.yml) и [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Обеспечивает мониторинг и оповещения для работы нашего приложения.

## <a name="threat-model"></a>Модель рисков
Моделирование угроз — это процесс выявления потенциальных угроз безопасности для вашего бизнеса и приложения и последующего обеспечения надлежащего плана по устранению рисков.

В этом примере используется [Microsoft Threat Modeling Tool](threat-modeling-tool.md) для реализации моделирования угроз для безопасного примера приложения. Благодаря диаграмме компонентов и потоков данных можно определить проблемы и угрозы на ранних этапах процесса разработки. Это экономит время и деньги позже.

Это модель угроз для примера приложения:

![Модель рисков](./media/secure-web-app/threat-model.png)

В следующем снимке экрана показаны некоторые примеры угроз и потенциальные уязвимости, которые создает средство моделирования угроз. Модель угроз предоставляет обзор уязвимой уязвимой зоны и предлагает разработчикам подумать о том, как устранить проблемы.

![Выходные данные модели угроз](./media/secure-web-app/threat-model-output.png)

Например, внедрение кода SQL в предыдущем выводе модели угроз устраняется путем очистки входных данных пользователя и с помощью хранимых функций в базе данных Azure для PostgreSQL. Это предотвращает произвольное выполнение запросов во время операций чтения и записи данных.

Разработчики улучшают общую безопасность системы за счет уменьшения всех угроз в выходных данных модели угроз.

## <a name="deployment"></a>Развертывание
Следующие параметры позволяют запускать Linux в службе приложений Azure:

- Выберите контейнер из списка предварительно созданных контейнеров Майкрософт в Azure, созданных с помощью поддерживающих технологий (Python, Ruby, PHP, Java, Node. js и .NET Core).
- Используйте пользовательский контейнер, созданный пользователем. Выберите свои собственные реестры контейнеров в качестве источника образа и создайте на основе множества доступных технологий, поддерживающих HTTP.

В этом примере вы запустите скрипт развертывания, который развернет webapp в службе приложений и создаст ресурсы.

Приложение может использовать различные модели развертывания, показанные ниже:

![Схема потока данных развертывания](./media/secure-web-app/deployment.png)

Существует множество способов развертывания приложений в Azure, в том числе:

- Шаблоны Azure Resource Manager
- PowerShell
- Azure CLI
- портала Azure
- Azure DevOps

Используемое приложение:

- [DOCKER](https://docs.docker.com/) для создания и сборки образов контейнеров.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) для развертывания.
- [DOCKER Hub](https://hub.docker.com/) в качестве реестра контейнеров.

## <a name="security-considerations"></a>Замечания по безопасности

### <a name="network"></a>Сеть
Пример приложения использует сквозное шифрование SSL для транзитных данных, передаваемых в сеть и из нее. Шлюз настроен с помощью самозаверяющего сертификата.
> [!IMPORTANT]
> В этой демонстрации используется самозаверяющий сертификат. В рабочей среде следует получать сертификаты из проверенного центра сертификации (ЦС).

Брандмауэр приложения также проверяет входящий трафик и оповещает администраторов при обнаружении вредоносного трафика в сетевом трафике.
Шлюз приложений уменьшает возможность обнаружения угроз от атак DDoS и атак путем внедрения кода SQL в модели угроз.

### <a name="identity"></a>идентификации
Чтобы войти на портал, в примере приложения используется многофакторная идентификация для администраторов Azure Active Directory (Azure AD), которым назначен доступ к ресурсам.
Пример приложения использует управляемые удостоверения для получения разрешений на чтение и извлечение секретов из Azure Key Vault, гарантируя, что приложению не требуется жестко запрограммировать учетные данные и маркеры для чтения секретов. Azure AD автоматически создает субъекты-службы, необходимые приложению для чтения и изменения секретов при использовании управляемых удостоверений.

Управляемые удостоверения для ресурсов Azure и MFA затрудняют злоумышленникову получать привилегии и расширяют свои привилегии в системе. Эта угроза указывала в модели угроз.
Приложение использует OAuth, что позволяет пользователям, зарегистрированным в приложении OAuth, входить в приложение.

### <a name="storage"></a>Служба хранилища
База данных Azure для PostgreSQL автоматически шифрует данные в базе данных PostgreSQL. База данных авторизует IP-адреса службы приложений таким образом, что только развернутое веб-приложение службы приложений сможет получить доступ к ресурсам базы данных с использованием правильных учетных данных проверки подлинности.

### <a name="logging-and-auditing"></a>Ведение журналов и аудит
Приложение реализует ведение журнала с помощью Application Insights для контроля метрик, журналов и исключений, которые происходят. Это ведение журнала предоставляет достаточно метаданных приложения, чтобы информировать разработчиков и участников группы операций о состоянии приложения. Он также предоставляет достаточно данных для возврата в случае инцидентов безопасности.

## <a name="cost-considerations"></a>Рекомендации по стоимости
Если у вас еще нет учетной записи Azure, вы можете создать ее бесплатно. Перейдите на [страницу бесплатной учетной записи](https://azure.microsoft.com/free/) , чтобы начать работу, Узнайте, что можно делать с бесплатной учетной записью Azure, и Узнайте, какие продукты доступны в течение 12 месяцев.

Чтобы развернуть ресурсы в примере приложения с помощью функций безопасности, необходимо заплатить за некоторые функции уровня "Премиум". По мере того как приложение масштабируется, а бесплатные уровни и пробные версии, предлагаемые Azure, должны быть обновлены в соответствии с требованиями приложения, ваши затраты могут увеличиться. Используйте [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/) Azure для оценки затрат.

## <a name="deploy-the-solution"></a>Развертывание решения
### <a name="prerequisites"></a>предварительные требования
Чтобы запустить приложение, необходимо установить следующие средства:

- Редактор кода для изменения и просмотра кода приложения. [Visual Studio Code](https://code.visualstudio.com/) является параметром с открытым исходным кодом.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) на компьютере разработчика.
- [Git](https://git-scm.com/) в вашей системе. Git используется для локального клонирования исходного кода.
- [JQ](https://stedolan.github.io/jq/), средство UNIX для запроса JSON в удобном для пользователя виде.

Для развертывания ресурсов примера приложения необходима подписка Azure. Если у вас нет подписки Azure, можно [создать бесплатную учетную запись](https://azure.microsoft.com/free/) для тестирования примера приложения.

После установки этих средств все готово к развертыванию приложения в Azure.

### <a name="environment-setup"></a>Настройка среды
Выполните скрипты развертывания, чтобы настроить среду и подписку:

1. Чтобы клонировать репозиторий исходного кода, используйте следующую команду git:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Чтобы переместиться в каталог, используйте следующую команду:

   ```
   cd tutorial-project/scripts
   ```

3. В папке Scripts находятся файлы, относящиеся к используемой платформе (Windows или Linux). Когда Azure CLI уже установлен, войдите в учетную запись Azure из командной строки, выполнив следующую команду CLI:

   ``` azurecli
   az login
   ```

Откроется браузер, выполните вход с использованием своих учетных данных. После входа в систему можно начать развертывание ресурсов из командной строки.

Скрипты `deploy-powershell.ps1` развертывания и `deploy-bash.sh` содержат код для развертывания всего приложения.
Чтобы развернуть решение, выполните следующие действия.

1. Если вы используете PowerShell, запустите `deploy-powershell.ps1` файл, введя `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` замену имени региона и группы ресурсов с подходящими регионами Azure и именем группы ресурсов.
2. Если вы используете Linux, запустите `deploy-bash.sh` файл `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`, выполнив команду, может потребоваться сделать файл исполняемым, введя команду`chmod +x deploy-bash.sh`

В следующих примерах демонстрируются фрагменты ключевых компонентов. Вы можете развернуть примеры по отдельности или с остальными компонентами, запустив файлы развертывания.

### <a name="implementation-guidance"></a>Рекомендации по реализации
Скрипт развертывания — это один сценарий, который можно разделить на четыре этапа. Каждый этап развертывает и настраивает ресурс Azure, который находится на [схеме архитектуры](#architecture).

Четыре этапа:

- Развертывание Azure Key Vault.
- Разверните базу данных Azure для PostgreSQL.
- Развертывание веб-приложений Azure в Linux.
- Развертывание шлюза приложений с брандмауэром веб-приложения.

Каждый этап строится на основе предыдущего с помощью конфигурации из ранее развернутых ресурсов.

Чтобы выполнить действия по реализации, убедитесь, что установлены средства, перечисленные в разделе [Предварительные требования](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Развертывание Azure Key Vault
В этом разделе вы создадите и развернете экземпляр Azure Key Vault, который используется для хранения секретов и сертификатов.

После завершения развертывания вы получите Azure Key Vault экземпляр, развернутый в Azure.

Развертывание Azure Key Vault с помощью Azure CLI.

1. Объявите переменные для Azure Key Vault.
2. Зарегистрируйте поставщик Azure Key Vault.
3. Создайте группу ресурсов для экземпляра.
4. Создайте экземпляр Azure Key Vault в группе ресурсов, созданной на шаге 3.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
Рекомендуется использовать управляемые удостоверения для ресурсов Azure в приложениях, которые используют Key Vault для доступа к ресурсам. Уровень безопасности увеличивается, когда ключи доступа Key Vault не хранятся в коде или в конфигурации.

#### <a name="deploy-azure-database-for-postgresql"></a>Развертывание базы данных Azure для PostgreSQL
База данных Azure для PostgreSQL работает следующим образом. сначала создайте сервер базы данных, а затем создайте базу данных, в которой будет храниться схема и данные.

После завершения развертывания у вас будет сервер PostgreSQL и база данных, работающая в Azure.

Чтобы развернуть базу данных Azure для PostgreSQL с помощью Azure CLI, выполните следующие действия.

1. Откройте терминал с Azure CLI и настройте подписку Azure.
2. Создайте сочетание имени и пароля безопасного пользователя, которое используется для доступа к базе данных. (Они должны храниться в Azure Key Vault для приложений, которые их используют.)
3. Создайте экземпляр сервера PostgreSQL.
4. Создайте базу данных на экземпляре сервера, созданном на шаге 3.
5. Выполнение скриптов PostgreSQL в экземпляре PostgreSQL.

Приведенный ниже код полагается на секреты ПГУСЕРНАМЕ и ПГПАССВОРД, хранящиеся в Azure KeyVault, из предыдущего шага развертывание KeyVault.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

После развертывания базы данных необходимо сохранить ее учетные данные и строку подключения в Azure Key Vault.
В папке Scripts есть `functions.sql` файл, содержащий код PL/pgSQL, который создает хранимые функции при ее запуске. Выполнение этого файла позволяет параметризовать входные данные, чтобы ограничить внедрение кода SQL.

PostgreSQL входит в состав средства с именем `psql` , которое используется для подключения к базе данных. Для запуска `functions.sql`необходимо подключиться к экземпляру базы данных Azure для PostgreSQL с локального компьютера и запустить его отсюда. Установка средства psql включена в установку по умолчанию для PostgreSQL в каждой операционной системе.
Дополнительные сведения см. в [документации по psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell также включает `psql` средство. Вы можете использовать Cloud Shell непосредственно из портал Azure, щелкнув значок Cloud Shell.

Чтобы включить удаленный доступ к экземпляру PostgreSQL, необходимо авторизовать IP-адрес в PostgreSQL.
Чтобы включить этот доступ, перейдите на вкладку **Безопасность подключения** , выберите **Добавить IP-адрес клиента**и сохраните новые параметры.

![Авторизация IP-адреса клиента](./media/secure-web-app/add-client-ip-postgres.png)

Если вы используете Cloud Shell вместо локального средства PSQL, выберите **Разрешить доступ к службам Azure** и измените значение **на включено** , чтобы разрешить доступ к Cloud Shell.

Затем подключитесь к экземпляру, выполнив следующую команду psql с параметрами строки подключения на вкладке **строки подключения** экземпляра PostgreSQL в портал Azure.
Замените пустые фигурные скобки параметрами из колонки строка подключения базы данных и пароля паролем Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Запустите следующий сценарий PL/pgSQL после того, как вы убедитесь, что вы подключены к базе данных. Скрипт создает хранимые функции, используемые для вставки данных в базу данных.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


Дополнительные сведения о настройке SSL и проверки центра сертификации для PostgreSQL см. [в статье Настройка SSL-подключений в базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

Корневой сертификат входит в контейнер. Ниже приведены действия по получению сертификата.

1. Скачайте файл сертификата из [центра сертификации](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Скачайте и установите OpenSSL на своем компьютере](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).
3. Декодирование файла сертификата:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Дополнительные сведения о настройке безопасности SSL для PostgreSQL см. в статье [Настройка безопасности SSL-соединений](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Развертывание веб-приложений Azure в Linux
Вы можете легко создавать службы Linux поверх службы приложений Azure, так как Azure предоставляет набор готовых контейнеров и образов для широко используемых языков, таких как Python, Ruby C#, и Java. Azure также поддерживает пользовательские контейнеры, которые могут позволить практически всем языкам программирования работать на платформе службы приложений Azure.

Развертываемое приложение — это простое приложение Python, которое работает на последнем Ubuntu Linux дистрибутиве. Он подключается к экземплярам Azure Key Vault и PostgreSQL, созданным в предыдущих разделах для управления учетными данными и хранения данных соответственно.

В корневой папке приложения указан следующий файл docker:

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Приведенный выше Dockerfile используется для создания контейнера, размещенного в реестре контейнеров Azure по адресу `mcr.microsoft.com/samples/basic-linux-app`.

Приведенный ниже код:

1. Объявляет переменные и имена для экземпляра службы приложений.
2. Создает группу ресурсов для плана службы приложений.
3. Подготавливает веб-приложения Azure на экземпляре контейнеров Linux.
4. Включает ведение журнала для контейнера веб-приложения.
5. Задает некоторые конфигурации приложений в параметрах приложения контейнера.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

Этот скрипт создает назначенное удостоверение для экземпляра службы приложений, который можно использовать с MSI для взаимодействия с Azure Key Vault без жестко кодированных секретов в коде или конфигурации.

Перейдите на Azure Key Vault экземпляр на портале, чтобы авторизовать назначенное удостоверение на вкладке Политика доступа. Выберите **Добавить новую политику доступа**. В разделе **Выбор субъекта**найдите имя приложения, похожее на имя созданного экземпляра службы приложений.
Субъект-служба, присоединенная к приложению, должна быть видимой. Выберите страницу и сохраните политику доступа, как показано на следующем снимке экрана.

Так как приложению необходимо получить только ключи, выберите разрешение **Get** в параметрах секреты, чтобы разрешить доступ, одновременно уменьшая предоставленные привилегии.

![Политика доступа к Key Vault](./media/secure-web-app/kv-access-policy.png)

*Создание политики доступа Key Vault*

Сохраните политику доступа, а затем сохраните новое изменение на вкладке **политики доступа** , чтобы обновить политики.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Развертывание шлюза приложений с включенным брандмауэром веб-приложения
В веб-приложениях не рекомендуется предоставлять службы непосредственно внешнему миру в Интернете.
Правила балансировки нагрузки и брандмауэра обеспечивают более безопасный и контроль над входящим трафиком и помогают управлять им.

Чтобы развернуть экземпляр шлюза приложений, выполните следующие действия.

1. Создайте группу ресурсов, в которой будет размещен шлюз приложений.
2. Подготавливает виртуальную сеть для подключения к шлюзу.
3. Создайте подсеть для шлюза в виртуальной сети.
4. Подготавливает общедоступный IP-адрес.
5. Подготавливает шлюз приложений.
6. Включите брандмауэр веб-приложения на шлюзе.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Предыдущий скрипт:

1. Создает новый самозаверяющий сертификат в Azure.
2. Скачивает самозаверяющий сертификат как файл в кодировке Base64.
3. Создает пароль для самозаверяющего сертификата.
4. Экспортирует сертификат в виде PFX-файла, подписанного паролем.
5. Сохраняет пароль сертификата в Azure Key Vault.

В этом разделе развертывается шлюз приложений:

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

После завершения развертывания вы получите шлюз приложений с включенным брандмауэром веб-приложения.

Экземпляр шлюза предоставляет порт 443 для HTTPS. Такая конфигурация гарантирует, что наше приложение будет доступно только через порт 443 по протоколу HTTPS.

Блокирование неиспользуемых портов и ограничение уязвимости в области атаки является рекомендуемой практикой безопасности.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Добавление групп безопасности сети в экземпляр службы приложений

Экземпляры службы приложений можно интегрировать с виртуальными сетями. Такая интеграция позволяет настроить для них политики групп безопасности сети, которые управляют входящим и исходящим трафиком приложения.

1. Чтобы включить эту функцию, в колонке экземпляра службы приложений Azure в разделе **Параметры**выберите **сеть**. В области справа в разделе **Интеграция виртуальной сети**выберите **щелкните здесь, чтобы настроить**.

   ![Новая интеграция виртуальной сети](./media/secure-web-app/app-vnet-menu.png)

    *Новая интеграция виртуальной сети для службы приложений*
1. На следующей странице выберите **добавить виртуальную сеть (Предварительная версия)** .

1. В следующем меню выберите виртуальную сеть, созданную в развертывании, которое начинается `hello-vnet`с. Можно либо создать новую подсеть, либо выбрать существующую.
   В этом случае создайте новую подсеть. Задайте для **диапазона адресов** значение **10.0.3.0/24** и укажите имя **подсети App-Subnet**.

   ![Конфигурация виртуальной сети службы приложений](./media/secure-web-app/app-vnet-config.png)

    *Конфигурация виртуальной сети для службы приложений*

Теперь, когда вы включили интеграцию с виртуальной сетью, к нашему приложению можно добавить группы безопасности сети.

1. Используйте поле поиска, чтобы найти **группы безопасности сети**. В результатах выберите **группы безопасности сети** .

    ![Поиск групп безопасности сети](./media/secure-web-app/nsg-search-menu.png)

    *Поиск групп безопасности сети*

2. В следующем меню выберите **Добавить**. Введите **имя** NSG и **группу ресурсов** , в которой он должен находиться. Эта NSG будет применена к подсети шлюза приложений.

    ![Создание NSG](./media/secure-web-app/nsg-create-new.png)

    *Создание NSG*

3. После создания NSG выберите его. В колонке в разделе **Параметры**выберите **правила безопасности для входящего трафика**. Настройте эти параметры, чтобы разрешить подключения к шлюзу приложений через порт 443.

   ![Настройка NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Настройка NSG*

4. В правилах исходящего трафика для шлюза NSG добавьте правило, разрешающее исходящие подключения к экземпляру службы приложений, создав правило, предназначенное `AppService`для тега службы:

   ![Добавление правил для исходящего трафика для NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Добавление правил для исходящего трафика для NSG*

    Добавьте еще одно правило для исходящего трафика, чтобы разрешить шлюзу отправку правил исходящего трафика в виртуальную сеть.

   ![Добавить другое правило для исходящего трафика](./media/secure-web-app/nsg-outbound-vnet.png)

    *Добавить другое правило для исходящего трафика*

5. В колонке подсети NSG выберите **сопоставить**, выберите виртуальную сеть, созданную в развертывании, и выберите подсеть шлюза с именем **GW-Subnet**. NSG применяется к подсети.

6. Создайте еще один NSG, как на предыдущем шаге, на этот раз для экземпляра службы приложений. Присвойте ему имя. Добавьте правило входящего трафика для порта 443, как и для шлюза приложений NSG.

   При наличии экземпляра службы приложений, развернутого на Среда службы приложений экземпляре, который не используется для этого приложения, можно добавить правила для входящего трафика, чтобы разрешить проверки работоспособности служб Azure, открыв порты 454-455 в группах безопасности входящих данных службы приложений NSG. Ниже приведена конфигурация.

   ![Добавление правил для проверок работоспособности служб Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Добавление правил для проверок работоспособности служб Azure (только Среда службы приложений)*

7. В правилах безопасности для исходящего трафика создайте новое правило безопасности для исходящего трафика, которое позволит экземпляру службы приложений взаимодействовать с базой данных PostgreSQL. Настройте его следующим образом:

   ![Правило, разрешающее исходящие подключения PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Добавление правила, разрешающего исходящие подключения PostgreSQL*

Чтобы ограничить область атак, измените параметры сети службы приложений, чтобы разрешить доступ к приложению только шлюзу приложений.
Для этого перейдите на вкладку Сеть службы приложений, перейдите на вкладку **ограничения IP-адресов** и создайте правило разрешения, которое разрешает прямой доступ к службе только IP-адресу шлюза приложений.

IP-адрес шлюза можно получить на странице обзора. На вкладке **CIDR. IP-адрес** введите IP-адрес в следующем формате: `<GATEWAY_IP_ADDRESS>/32`.

![Разрешить только шлюз](./media/secure-web-app/app-allow-gw-only.png)

*Разрешить доступ к службе приложений только IP-адресу шлюза*


#### <a name="implement-azure-active-directory-oauth"></a>Реализация Azure Active Directory OAuth

Документы Azure, распространяемые на странице Пример веб-приложения, являются ресурсами в нашем приложении, которые могут требовать защиты. Вы можете использовать Azure Active Directory (Azure AD) для реализации проверки подлинности для веб-, настольных и мобильных приложений с помощью различных потоков проверки подлинности.
Приложение использует **Вход с помощью Microsoft**, что позволяет приложению читать профили пользователей, добавленных в список пользователей Azure AD одного клиента.

В портал Azure настройте приложение для использования необходимых учетных данных:

1. Выберите **Azure Active Directory**или найдите его с помощью поля поиска.

2. Выберите **Новая регистрация**:

   ![Создание регистрации](./media/secure-web-app/ad-auth-create.png)

   *Создание регистрации приложения Azure AD*

3. На следующей странице введите имя приложения. В разделе **Поддерживаемые типы учетных записей**выберите **учетные записи только в этом каталоге Организации**.
    В разделе **URI перенаправления**введите базовый домен, на котором будет выполняться приложение, плюс одно с конечной точкой маркера. Пример: *GATEWAY_HASH*. cloudapp.NET/Token.

   ![Настройка регистрации приложений Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Настройка регистрации приложений Azure AD*

4. Появится экран, на котором отображается зарегистрированное приложение и сведения о нем. Эти сведения необходимо добавить в экземпляр Azure Key Vault.
   1. Скопируйте идентификатор приложения (Client) и сохраните его в Key Vault как `CLIENTID`.
   2. Скопируйте URI перенаправления, введенный на предыдущем шаге, и сохраните его как `REDIRECTURI`.
   3. Скопируйте имя каталога по умолчанию Azure AD с форматом *Name*. microsoftonline.com и сохраните его в Key Vault как `TENANT`.
   4. Перейдите на вкладку **сертификаты & секреты** в созданном ранее приложении Azure AD и выберите **новый секрет клиента**, как показано на следующем снимке экрана. Задайте дату окончания срока действия, а затем скопируйте созданное значение и сохраните его в Key Vault как `CLIENTSECRET`.

      ![Секрет авторизации Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Секрет авторизации Azure AD*

   5. Создайте безопасный случайный секретный ключ с помощью любого средства командной строки или сети. Сохраните его в Key Vault как `FLASKSECRETKEY`. Платформа приложений использует этот ключ для создания сеансов.
        Сведения о создании секретного ключа см. в разделе [Flask Sessions](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. После настройки входа необходимо добавить пользователей в ссылку Azure AD, чтобы разрешить им входить в ресурс. Чтобы добавить их, перейдите на вкладку **Пользователи** в Azure AD, выберите **все пользователи**, а затем выберите **Новый пользователь** или **Новый гостевой пользователь**. Для тестирования можно добавить гостевого пользователя и пригласить пользователя в каталог. Или можно добавить нового пользователя, если домен, в котором выполняется приложение, проверен. В этом примере для доступа можно зарегистрировать только пользователей, зарегистрированных в клиенте Azure AD. Сведения о доступе для входа с помощью клиента см. в документации.

   ![Добавление пользователей в домен по умолчанию](./media/secure-web-app/ad-auth-add-user.png)

   *Добавление пользователей в домен Azure Active Directory по умолчанию*

После добавления конфигурации и секретов Azure AD в Key Vault пользователи могут пройти проверку подлинности в приложении с помощью аутентификации Azure OAuth.
В коде приложения эта библиотека обрабатывается библиотекой проверки подлинности Azure Active Directory (ADAL).

После того, как секреты находятся в Key Vault и приложение имеет доступ к секретам и базе данных, служба приложения может быть доступна через URL-адрес приложения шлюза https://GATEWAY_HASH.cloudapp.net) (, который можно получить из колонки.

Если при входе в Azure AD появляется сообщение об ошибке "пользователь не зарегистрирован в каталоге, в который вы пытаетесь войти", необходимо добавить пользователя. Чтобы добавить пользователя, перейдите на вкладку " **Пользователи** " Azure AD и добавьте пользователя вручную, введя свои сведения или приглашая пользователя, введя свой адрес электронной почты в качестве гостевого пользователя в Azure AD в колонке " **пригласить гостей** ".

#### <a name="deploy-application-insights"></a>Развертывание Application Insights
Теперь, когда приложение развернуто и работает, необходимо выполнить обработку ошибок, происходящих в приложении, а также сбор данных журнала и трассировки.
Сбор данных ведения журнала и трассировки обеспечивает представление событий аудита, происходящих в приложении.

Application Insights — это служба, собирающая журналы, которые могут создаваться пользователями или системой.

Создание экземпляра Application Insights:

1. Выполните поиск **Application Insights** с помощью поля поиска в портал Azure.
2. Выберите **Application Insights**. Укажите сведения, показанные здесь, чтобы создать экземпляр.

   ![Создание экземпляра Application Insights](./media/secure-web-app/app-insights-data.png)

После завершения развертывания у вас будет экземпляр Application Insights.

После создания экземпляра Application Insights необходимо сделать приложение осведомленным о ключе инструментирования, позволяющем передавать журналы в облако. Это можно сделать, извлекая ключ Application Insights и используя его в библиотеках приложений, которые Azure предоставляет для Application Insights. Рекомендуется хранить ключи и секреты в Azure Key Vault, чтобы обеспечить их безопасность.

Для базового примера приложения после создания экземпляра Application Insights необходимо сделать приложение осведомленным о ключе инструментирования, позволяющем передавать журналы в облако.
В Key Vault задайте `APPINSIGHTSKEY` секрет и задайте его значение в качестве ключа инструментирования. Это позволит приложению отправить журналы и метрики в Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Реализация многофакторной проверки подлинности для Azure Active Directory
Администраторам необходимо обеспечить защиту учетных записей подписки на портале. Подписка уязвима для атак, так как она управляет созданными ресурсами. Чтобы защитить подписку, включите многофакторную проверку подлинности на вкладке **Azure Active Directory** подписки.

Azure AD работает на основе политик, применяемых к пользователям или группам пользователей, которые соответствуют определенным критериям.
Azure создает политику по умолчанию, указывающую, что администраторам требуется двухфакторная проверка подлинности для входа на портал.
После включения этой политики вам может быть предложено выйти из портал Azure и снова войти в нее.

Включение MFA для входа администратора.

1. Перейдите на вкладку **Azure Active Directory** в портал Azure
2. В категории Безопасность выберите условный доступ. Появится следующий экран:

   ![Условный доступ — политики](./media/secure-web-app/ad-mfa-conditional-add.png)

Если не удается создать новую политику, выполните следующие действия.

1. Перейдите на вкладку **MFA** .
2. Чтобы подписываться на бесплатную пробную версию, выберите ссылку на **бесплатную пробную версию** Azure AD Premium.

   ![Azure AD Premium бесплатной пробной версии](./media/secure-web-app/ad-trial-premium.png)

Вернитесь к экрану условного доступа.

1. Перейдите на вкладку Новая политика.
2. Введите имя политики.
3. Выберите пользователей или группы, для которых требуется включить MFA.
4. В разделе **элементы управления доступом**перейдите на вкладку **предоставление** , а затем выберите требовать многофакторную **проверку** подлинности (и другие параметры при необходимости).

   ![Требовать многофакторную идентификацию](./media/secure-web-app/ad-mfa-conditional-add.png)

Политику можно включить, установив флажок в верхней части экрана, или сделать это на вкладке **Условный доступ** . Если политика включена, пользователям требуется MFA для входа на портал.

Существует базовая политика, которая требует MFA для всех администраторов Azure. Вы можете немедленно включить его на портале. Включение этой политики может сделать недействительным текущий сеанс и принудительно выполнить вход.

Если базовая политика не включена:
1.  Выберите параметр **требовать MFA для администраторов**.
2.  Выберите **параметр использовать политику немедленно**.

   ![Выберите параметр использовать политику немедленно.](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Использование Sentinel Azure для мониторинга приложений и ресурсов

По мере роста приложения становится трудно объединить все сигналы и метрики безопасности, полученные от ресурсов, и сделать их полезными в действии, ориентированном на действия.

Azure Sentinel предназначен для получения данных, выявления возможных типов угроз и предоставления сведений об инцидентах безопасности.
Хотя он ожидает вмешательства вручную, Azure Sentinel может полагаться на предварительно написанный модули PlayBook, чтобы запускать оповещения и процессы управления инцидентами.

Пример приложения состоит из нескольких ресурсов, которые может отслеживать метка Azure Sentinel.
Чтобы настроить метку Azure, сначала необходимо создать рабочую область Log Analytics, в которой будут храниться все данные, собранные из различных ресурсов.

Чтобы создать эту рабочую область, сделайте следующее:

1. В поле поиска в портал Azure найдите **log Analytics**. Выберите **Рабочие области Log Analytics**.

   ![Поиск рабочих областей Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Поиск рабочих областей Log Analytics*

2. На следующей странице выберите **Добавить** , а затем укажите имя, группу ресурсов и расположение для рабочей области.
   ![Создание рабочей области Log Analytics с помощью PowerShell](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Создание рабочей области Log Analytics с помощью PowerShell*

3. Используйте поле поиска, чтобы найти **метку Azure**.

   ![Поиск Sentinel Azure](./media/secure-web-app/sentinel-add.png)

    *Поиск Sentinel Azure*

4. Выберите **Добавить** , а затем выберите созданную ранее рабочую область log Analytics.

   ![Добавление рабочей области Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Добавление рабочей области Log Analytics*

5. На странице **Azure Sentinel — соединители данных** в разделе **Конфигурация**выберите пункт **соединители данных**. Вы увидите массив служб Azure, который можно связать с экземпляром хранилища Log Analytics для анализа в Azure Sentinel.

   ![Log Analytics соединителей данных](./media/secure-web-app/sentinel-connectors.png)

    *Добавление соединителя данных в Azure Sentinel*

   Например, чтобы подключить шлюз приложений, сделайте следующее:

   1. Откройте колонку экземпляр шлюза приложений Azure.
   2. В разделе **Мониторинг** выберите **Параметры диагностики**.
   3. Выберите **Добавить параметр диагностики**.

      ![Добавление диагностики шлюза приложений](./media/secure-web-app/sentinel-gateway-connector.png)

      *Добавление диагностики шлюза приложений*

   4. На странице **параметры диагностики** выберите созданную рабочую область log Analytics, а затем выберите все метрики, которые требуется получить и отправить в Azure Sentinel. Щелкните **Сохранить**.

        ![Параметры соединителя Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Параметры соединителя Azure Sentinel*

  Метрики ресурса находятся в Azure Sentinel, где их можно запрашивать и исследовать.

   Добавьте те же метрики в параметрах диагностики для Azure Key Vault, общедоступного IP-адреса, базы данных Azure для PostgreSQL и любых служб, поддерживающих журналы диагностики в вашей учетной записи.

После настройки метрик Sentinel Azure будет иметь данные для анализа.

## <a name="evaluate-and-verify"></a>Оценка и проверка
После разработки и развертывания архитектуры необходимо убедиться, что код и развернутые службы соответствуют стандартам безопасности. Ниже приведены некоторые действия, которые можно выполнить для проверки программного обеспечения.

- Статический анализ кода
- Сканирование уязвимостей
- Поиск и устранение уязвимостей в зависимостях приложений

Это основные стандартные блоки для рекомендаций по безопасной разработке.

### <a name="static-code-analysis"></a>Статический анализ кода
Для примера приложения проверка с помощью статических средств анализа включает в себя поиск уязвимостей в коде приложения с помощью таких методов, как проверка таинт и анализ потока данных. Средства статического анализа Python позволяют более уверенно обеспечить безопасность приложения.

**Linting**

Пифлакес, библиотека Python linting, помогает удалить неиспользуемый код и неиспользуемые функции из приложений, как показано ниже:

![пифлакес](./media/secure-web-app/pyflakes.png)

Linting предоставляет указания и возможные изменения, которые могут сделать код понятным и менее подверженным ошибкам во время выполнения.

**PyLint**

PyLint предоставил наибольшее значение для этого проекта. Он выполняет стандартные проверки кода, проверку ошибок и советы по рефакторингу, чтобы обеспечить безопасность кода, выполняемого на сервере. Используя PyLint для обновления кода, можно устранить ошибки и улучшить оценку PyLint, как показано на следующих рисунках.

![До PyLint](./media/secure-web-app/before-pylint.png)

*До PyLint*

После исправления некоторых ошибок в коде, обнаруженных средствами linting, вы получите более уверенность в том, что код не подвержен ошибкам. Устранение ошибок значительно снижает риски безопасности, которые могут возникнуть при развертывании кода в рабочей среде.

![После pylint](./media/secure-web-app/after-pylint.png)

*После PyLint*

### <a name="vulnerability-scanning"></a>Сканирование уязвимостей
Средство [ZAP OWASP](https://www.zaproxy.org/) является средством проверки уязвимости веб-приложения с открытым исходным кодом, которое можно использовать для проверки наличия уязвимостей в примере приложения. Запуск средства в примере приложения открывает некоторые возможные ошибки и векторы атак.

![Средство ZAP](./media/secure-web-app/zap-tool.png)

*Средство ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Поиск и устранение уязвимостей в зависимостях приложений
Для поиска и исправления зависимостей приложений можно использовать [проверку зависимостей OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

Безопасность — это аналогичное приложение, которое проверяет зависимости. Его можно найти на сайте [GitHub](https://github.com/pyupio/safety). Проверка безопасности на наличие уязвимостей в известных базах данных уязвимостей.

![Потокобезопасным](./media/secure-web-app/pysafety.png)

*Потокобезопасным*

## <a name="next-steps"></a>Следующие шаги
Следующие статьи помогут в проектировании, разработке и развертывании безопасных приложений.

- [Design](secure-design.md) (Конструктор)
- [Разработка](secure-develop.md)
- [Развертывание](secure-deploy.md)
