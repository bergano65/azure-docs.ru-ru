---
title: Разработка безопасного веб-приложения Документы Майкрософт
description: В этом простом примере приложения реализованы рекомендации по безопасности, которые улучшают ваше приложение и безопасность организации при разработке в Azure.
keywords: Нет
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
ms.openlocfilehash: 55c6d374c8a3c308323c0d003726492477e33ff8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811242"
---
# <a name="develop-a-secure-web-app"></a>Разработка безопасного веб-приложения

Этот пример представляет собой простое приложение Python, которое отображает веб-страницу, содержащую ссылки на ресурсы безопасности для разработки приложений на Azure. Приложение реализует рекомендации по безопасности, которые могут помочь улучшить ваше приложение и безопасность вашей организации при разработке приложений на Azure.

Следует последовательно выполнять действия, описанные в этой статье, чтобы убедиться, что компоненты приложения настроены должным образом. База данных, служба приложений Azure, экземпляр Azure Key Vault и экземпляр Azure Application Gateway зависят друг от друга.

Скрипты развертывания настраивают инфраструктуру. После запуска сценариев развертывания необходимо сделать ручную конфигурацию на портале Azure, чтобы связать компоненты и службы вместе.

Пример приложения предназначен для начинающих, разрабатывая приложения в Azure, которые хотят реализовать меры безопасности в своих приложениях.

При разработке и развертывании этого приложения вы узнаете, как:

- Создайте экземпляр Azure Key Vault, храните и извлекайте из него секреты.
- Развертывайте базу данных Azure для PostgreS'L, направляйте безопасные пароли и разрешайте доступ к ним.
- Запустите контейнер Alpine Linux в веб-приложениях Azure для Linux и включите управляемые идентификаторы для ресурсов Azure.
- Создайте и направите экземпляр Azure Application Gateway с помощью брандмауэра, использующему [правила OWASP Top 10.](https://coreruleset.org/)
- Включите шифрование данных в пути и в состоянии покоя с помощью служб Azure.

После разработки и развертывания этого приложения, вы будете настроены следующий образец веб-приложения вместе с конфигурацией и мерами безопасности, которые описаны.

![Пример веб-приложения](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architecture

Приложение является типичным n-уровня приложение с тремя уровнями. Передний конец, задняя часть и уровень базы данных с интегрированными компонентами мониторинга и секретного управления показаны здесь:

![Архитектура приложения](./media/secure-web-app/architecture.png)

Архитектура состоит из следующих компонентов:

- [Шлюз приложений Azure.](../../application-gateway/index.yml) Обеспечивает шлюз и брандмауэр для архитектуры приложений.
- [Лазурные веб-приложения на Linux](../../app-service/containers/app-service-linux-intro.md). Обеспечивает время выполнения контейнера для запуска приложения Python в среде Linux.
- [Лазурный ключ Убежище](../../key-vault/index.yml). Хранит и шифрует секреты нашего приложения и управляет созданием политик доступа вокруг них.
- [База данных Azure для PostgreS'L](https://azure.microsoft.com/services/postgresql/). Безопасно хранит данные нашего приложения.
- [Центр безопасности Azure](../../security-center/index.yml) и [исследования приложений Azure](../../azure-monitor/app/app-insights-overview.md). Обеспечивает мониторинг и оповещения о работе нашего приложения.

## <a name="threat-model"></a>Модель рисков

Моделирование угроз — это процесс выявления потенциальных угроз безопасности для вашего бизнеса и приложения, а затем обеспечения надлежащего плана смягчения последствий.

В этом примере использовался [инструмент моделирования угроз Майкрософт](threat-modeling-tool.md) для реализации моделирования угроз для безопасного примера приложения. Спомощью компонентов и потоков данных можно определить проблемы и угрозы на ранних стадиях процесса разработки. Это экономит время и деньги позже.

Это модель угрозы для примера приложения:

![Модель рисков](./media/secure-web-app/threat-model.png)

Некоторые примеры угроз и потенциальных уязвимостей, генерирующих инструмент моделирования угроз, отображаются на следующем скриншоте. Модель угроз дает обзор выставленной поверхности атаки и побуждает разработчиков задуматься о том, как уменьшить проблемы.

![Выход модели угрозы](./media/secure-web-app/threat-model-output.png)

Например, впрыск с помощью предыдущей модели угроз смягчается за счет дезинфекции входов пользователей и использования сохраненных функций в базе данных Azure для PostgreS'L. Это смягчение предотвращает произвольное выполнение запросов во время чтения и записи данных.

Разработчики повышают общую безопасность системы, смягчая каждую из угроз на выходе модели угроз.

## <a name="deployment"></a>Развертывание

Следующие опции позволяют запустить Linux в службе приложений Azure:

- Выберите контейнер из списка предварительно построенных контейнеров Microsoft в Azure, созданных с помощью поддерживающих технологий (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Используйте пользовательский контейнер. Выберите свои собственные реестры контейнеров в качестве источника изображения и опирайтесь на многие доступные технологии, поддерживающие HTTP.

В этом примере вы полнить сценарий развертывания, который развернет webapp в службу приложений и создаст ресурсы.

Приложение может использовать различные модели развертывания, показанные ниже:

![Диаграмма потока данных развертывания](./media/secure-web-app/deployment.png)

Существует множество способов развертывания приложений в Azure, включая:

- Шаблоны Azure Resource Manager
- PowerShell
- Azure CLI
- Портал Azure
- Azure DevOps

Это приложение использовало:

- [Докер](https://docs.docker.com/) для создания и создания изображений контейнеров.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) для развертывания.
- [Docker Hub](https://hub.docker.com/) как реестр контейнеров.

## <a name="security-considerations"></a>Вопросы безопасности

### <a name="network"></a>Сеть

В примере приложения используется сквозное шифрование TLS/SSL для транзитных данных, входящих в сеть и выходящих из нее. Шлюз настроен с сертификатом, подписанным самостоятельно.
> [!IMPORTANT]
> В этой демонстрации используется сертификат самоподписанных. В производственной среде необходимо получить сертификаты от проверенного Органа по сертификации (CA).

Брандмауэр приложения также проверяет входящий трафик и предупреждает админов при обнаружении вредоносного трафика в сетевом трафике.
Приложение Gateway смягчает возможность обнаружения угроз инъекций DDoS и S'L, обнаруженных в модели угроз.

### <a name="identity"></a>Идентификация

Для вхинга на портал в примере приложения используется Multi-Factor Authentication для администраторов Active Directory (Azure AD), которым присваивается доступ к ресурсам.
Пример приложения использует управляемые идентификаторы для получения разрешений на чтение и извлечение секретов из Azure Key Vault, гарантируя, что приложению не нужно жестко закодировать учетные данные и маркеры для чтения секретов. Azure AD автоматически создает принципы службы, которые приложение должно считывать, и изменяет секреты при использовании управляемых идентификационных данных.

Управляемые идентификаторы ресурсов Azure и MFA затрудняют для противников получение привилегий и усиление их привилегий в системе. Эта угроза была отмечена в модели угроз.
Приложение использует OAuth, который позволяет пользователям, зарегистрированным в приложении OAuth, войти в приложение.

### <a name="storage"></a>Память

Данные в базе данных PostgreS'L автоматически шифруются базой данных Azure Database для PostgreS'L. База данных разрешает IP-адреса Службы App, так что только развернутое веб-приложение Службы App Может получить доступ к ресурсам базы данных с правильными учетными данными проверки подлинности.

### <a name="logging-and-auditing"></a>Ведение журналов и аудит

Приложение реализует журнал, используя Application Insights для отслеживания метрик, журналов и исключений, которые происходят. Эта запись предоставляет достаточно метаданных приложения для информирования разработчиков и членов команды операций о состоянии приложения. Он также предоставляет достаточно данных для отката назад в случае инцидентов безопасности.

## <a name="cost-considerations"></a>Рекомендации по стоимости

Если у вас еще нет учетной записи Azure, можно создать бесплатную учетную запись. Перейдите на [бесплатную страницу учетной записи,](https://azure.microsoft.com/free/) чтобы начать работу, узнайте, что вы можете сделать с бесплатной учетной записью Azure, и узнайте, какие продукты бесплатны в течение 12 месяцев.

Чтобы развернуть ресурсы в примере приложения с функциями безопасности, необходимо оплатить некоторые премиум-функции. Поскольку масштабы приложения и бесплатные уровни и пробные версии, предлагаемые Azure, должны быть обновлены в соответствии с требованиями приложения, ваши затраты могут возрасти. Используйте [калькулятор ценообразования](https://azure.microsoft.com/pricing/calculator/) Azure для оценки затрат.

## <a name="deploy-the-solution"></a>Развертывание решения

### <a name="prerequisites"></a>Предварительные требования

Чтобы запустить приложение, необходимо установить следующие инструменты:

- Редактор кода для изменения и просмотра кода приложения. [Visual Studio Code](https://code.visualstudio.com/) — это вариант с открытым исходным кодом.
- [Azure CLI](/cli/azure/install-azure-cli) на компьютере разработки.
- [Git](https://git-scm.com/) на вашей системе. Git используется для локального клонирования исходного кода.
- [jq](https://stedolan.github.io/jq/), инструмент UNIX для запроса JSON в удобной для пользователя форме.

Для развертывания ресурсов примерного приложения необходима подписка Azure. Если у вас нет подписки Azure, вы можете [создать бесплатную учетную запись](https://azure.microsoft.com/free/) для тестирования примера приложения.

После установки этих инструментов вы готовы развернуть приложение в Azure.

### <a name="environment-setup"></a>Настройка среды

Запустите сценарии развертывания для настройки среды и подписки:

1. Чтобы клонировать репозиторий исходного кода, используйте эту команду Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Чтобы перейти в каталог, используйте эту команду:

   ```shell
   cd tutorial-project/scripts
   ```

3. В папке скриптов есть файлы, которые специфичны для платформы, которую вы используете (Windows или Linux). Поскольку Azure CLI уже установлен, восвай в учетную запись Azure в запросе команды, запустив эту команду Azure CLI:

   ```azurecli-interactive
   az login
   ```

Браузер откроется, ввешайтесь с вашими учетными данными. После входе в систему можно приступить к развертыванию ресурсов из запроса команды.

Сценарии `deploy-powershell.ps1` развертывания `deploy-bash.sh` содержат код, развертывает весь приложение.
Развертывание решения:

1. Если вы находитесь на `deploy-powershell.ps1` PowerShell, `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` запустите файл, введя заменяя название региона и группы ресурсов подходящими регионами Azure и именем группы ресурсов
2. Если вы находитесь `deploy-bash.sh` на Linux `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`запустить файл, введя, вы, возможно, придется сделать файл исполняемым, введя`chmod +x deploy-bash.sh`

Следующие примеры демонстрируют фрагменты ключевых компонентов. Вы можете развертывать примеры по отдельности или с остальными компонентами, запустив файлы развертывания.

### <a name="implementation-guidance"></a>Рекомендации по реализации

Сценарий развертывания — это один скрипт, который можно разбить на четыре этапа. Каждая фаза развертывает и настраивает ресурс Azure, который есть в [диаграмме архитектуры,](#architecture)

Четыре этапа:

- Развертывание Убежища ключей Azure.
- Развертывание базы данных Azure для PostgreS'L.
- Развертывание веб-приложений Azure на Linux.
- Развертывание шлюза приложений с брандмауэром веб-приложений.

Каждый этап основывается на предыдущем, используя конфигурацию из ранее развернутых ресурсов.

Чтобы завершить этапы реализации, убедитесь, что вы установили инструменты, перечисленные в [Prerequisites.](#prerequisites)

#### <a name="deploy-azure-key-vault"></a>Развертывание хранилища ключей Azure

В этом разделе вы создаете и развертываете экземпляр Azure Key Vault, который используется для хранения секретов и сертификатов.

После завершения развертывания в Azure развернут экземпляр Azure Key Vault.

Развертывание Azure Key Vault с помощью Azure CLI:

1. Объявить переменные для Убежища ключей Azure.
2. Зарегистрируйте поставщика Azure Key Vault.
3. Создайте группу ресурсов для экземпляра.
4. Создайте экземпляр Azure Key Vault в группе ресурсов, созданной в шаге 3.

   ```powershell-interactive

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

Лучше всего использовать управляемые идентификаторы для ресурсов Azure в приложениях, которые используют Key Vault для доступа к ресурсам. Ваша поза безопасности увеличивается, когда ключи доступа к Key Vault не хранятся в коде или в конфигурации.

#### <a name="deploy-azure-database-for-postgresql"></a>Развертывание базы данных Azure для PostgreS'L

База данных Azure для PostgreS'L работает следующим образом, сначала создайте сервер базы данных, а затем создайте базу данных, на которой можно хранить схему и данные.

После завершения развертывания в Azure работает сервер PostgreS'L и база данных.

Развертывание базы данных Azure для PostgreS'L с помощью Azure CLI:

1. Откройте терминал с Azure CLI и настройкой подписки Azure.
2. Создайте безопасное имя пользователя и комбинацию паролей, которая используется для доступа к базе данных. (Они должны храниться в Хранилище ключей Azure для приложений, которые их используют.)
3. Создайте экземпляр сервера PostgreS'L.
4. Создайте базу данных на экземпляре сервера, созданном в шаге 3.
5. Запуск скриптов PostgreS'L на экземпляре PostgreS'L.

Приведенный ниже код опирается на секреты PGUSERNAME и PGPASSWORD, хранящиеся в Azure KeyVault, начиная с вышеразвернутого шага KeyVault.

   ```powershell-interactive
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

После развертывания базы данных необходимо хранить ее учетные данные и строку соединения в Azure Key Vault.
В папке скриптов есть `functions.sql` файл, содержащий код PL/pgS'L, который создает сохраненные функции при его запуске. Запуск этого файла параметры ввода для ограничения впрыска S'L.

PostgreS'L в комплекте `psql` с инструментом под названием, который используется для подключения к базе данных. Для `functions.sql`запуска необходимо подключиться к базе данных Azure для экземпляра PostgreS'L из локальной машины и запустить ее оттуда. Установка инструмента psql включена в установку по умолчанию для PostgreS'L на каждой операционной системе.
Для получения дополнительной информации, [см psql Документация](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell `psql` также включает в себя инструмент. Обупогную оболочку можно использовать непосредственно с портала Azure, выбрав значок «Обуд оболочки».

Для обеспечения удаленного доступа к экземпляру PostgreS'L необходимо авторизовать IP-адрес в PostgreS'L.
Вы позволяете включить этот доступ, перейдя на вкладку **безопасности подключения,** выбрав **Access client IP**и сохранив новые настройки.

![Авторизовать иС клиента](./media/secure-web-app/add-client-ip-postgres.png)

Если вы используете Обузл вместо локального psql-инструмента, выберите **Разрешить доступ к службам Azure** и изменить его значение на **ON,** чтобы обеспечить доступ к облачной оболочке.

Затем подключитесь к экземпляру, запустив ниженную команду psql с параметрами строки соединения из **вкладки строки соединения** экземпляра PostgreS'L на портале Azure.
Замените пустые скобки параметрами из лезвия строки подключения базы данных и паролем паролем от Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Выполнить следующий скрипт PL/pgS'L после того, как вы убедитесь, что вы подключены к базе данных. Скрипт создает сохраненные функции, используемые для вставки данных в базу данных.

```shell
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

Для получения дополнительной информации о том, как настроить TLS и сертификат органа (CA) проверки для PostgreS'L, [см. Наклажись TLS подключения в базе данных Azure для PostgreS'L](/azure/postgresql/concepts-ssl-connection-security).

Корневой сертификат включен в контейнер. Меры, предпринятые для получения сертификата:

1. Скачать файл сертификата из [органа сертификата](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Скачать и установить OpenSSL на вашей машине.](/azure/postgresql/concepts-ssl-connection-security)
3. Расшифруйте файл сертификата:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Подробнее о том, как настроить tLS безопасности для PostgreS'L здесь [Нанастройка TLS безопасности подключения](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Развертывание веб-приложений Azure на Linux

Вы можете легко создавать службы Linux поверх службы приложений Azure, поскольку Azure предоставляет набор предварительно построенных контейнеров и изображений для широко используемых языков, таких как Python, Ruby, C и Java. Azure также поддерживает пользовательские контейнеры, которые позволяют практически всем языкам программирования работать на платформе Службы приложений Azure.

Развертывание приложения — это простое приложение Python, которое работает на последнем дистрибутиве Ubuntu Linux. Он подключается к экземплярам Azure Key Vault и PostgreS'L, которые были созданы в предыдущих разделах для управления учетными данными и хранения данных, соответственно.

Следующий файл Docker предоставляется в корневой папке приложения:

```dockerfile
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

Dockerfile выше используется для создания контейнера, который размещается `mcr.microsoft.com/samples/basic-linux-app`в реестре контейнеров Azure в .

Код ниже:

1. Объявляет переменные и имена для экземпляра службы приложений.
2. Создает группу ресурсов для плана службы приложений.
3. Предусматривает веб-приложения Azure на экземпляре контейнеров Linux.
4. Позволяет регистраться для контейнера веб-приложений.
5. Устанавливает некоторые конфигурации приложений в настройках приложения контейнера.

   ```powershell-interactive
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

Этот скрипт создает назначенную идентификацию для экземпляра Службы приложений, которая может быть использована с MSI для взаимодействия с Azure Key Vault без секретов жесткого кодирования в коде или конфигурации.

Перейдите в экземпляр Azure Key Vault на портале, чтобы авторизовать **Add new access policy**назначенную идентификацию на вкладке политики доступа. В соответствии с **принципом Select,** поиск имени приложения, аналогичного названию созданного экземпляра службы приложений.
Директор службы, прикрепленный к приложению, должен быть виден. Выберите его и сохраните страницу политики доступа, как показано на следующем скриншоте.

Поскольку приложению требуется только получение ключей, выберите разрешение **«Получить»** в вариантах секретов, что позволяет получить доступ при одновременном уменьшении предоставленных привилегий.

![Политика доступа к Key Vault](./media/secure-web-app/kv-access-policy.png)

*Создание политики доступа к Ключу Убежища*

Сохраните политику доступа, а затем сохраните новое изменение на **вкладке Политики доступа** для обновления политик.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Развертывание шлюза приложений с включенным брандмауэром веб-приложений

В веб-приложениях не рекомендуется разоблачать услуги непосредственно внешнему миру в Интернете.
Правила балансировки нагрузки и брандмауэра обеспечивают большую безопасность и контроль над входящим трафиком и помогают управлять им.

Развертывание экземпляра портала приложений:

1. Создайте группу ресурсов для размещения шлюза приложения.
2. Обеспечение виртуальной сети для присоединения к шлюзу.
3. Создайте подсеть для шлюза в виртуальной сети.
4. Предоставление публичного IP-адреса.
5. Предоставление шлюза приложения.
6. Включить брандмауэр веб-приложений на шлюзе.

   ```powershell-interactive
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

Предыдущий сценарий:

1. Создает новый сертификат самостоятельного подписания на Azure.
2. Загружает самоподписанный сертификат в качестве базового64-кодированного файла.
3. Создает пароль для сертификата, подписанного самостоятельно.
4. Экспорт сертификата как файла PFX, подписанного паролем.
5. Хранит пароль сертификата в Хранилище ключей Azure.

В этом разделе развертывается шлюз приложения:

```powershell-interactive
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

После завершения развертывания у вас есть шлюз приложения с включенным брандмауэром веб-приложений.

Экземпляр шлюза предоставляет порт 443 для HTTPS. Эта конфигурация гарантирует, что наше приложение доступно только на порту 443 через HTTPS.

Блокировка неиспользуемых портов и ограничение экспозиции поверхности атаки является наилучшей практикой безопасности.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Добавление групп сетевой безопасности в экземпляр службы приложений

Экземпляры Службы приложений могут быть интегрированы с виртуальными сетями. Эта интеграция позволяет настроить их с политиками группы сетевой безопасности, которые управляют входящим и исходящим трафиком приложения.

1. Для включения этой функции в лопасти экземпляра службы приложений Azure ВНе россии под **настройками**выберите **Networking.** В правильном стеку, под **интеграцией VNet,** выберите **Нажмите здесь, чтобы настроить.**

   ![Новая виртуальная сетевая интеграция](./media/secure-web-app/app-vnet-menu.png)

    *Новая виртуальная сетевая интеграция для Службы приложений*

1. На следующей странице выберите **Добавить VNET (предварительный просмотр).**

1. В следующем меню выберите виртуальную сеть, созданную в развертывании, которое начинается с `hello-vnet`. Можно создать новую подсеть или выбрать существующую.
   В этом случае создайте новую подсеть. Установите **диапазон адресов** до **10.0.0.0/24** и назовите **подсеть-подсеть.**

   ![Виртуальная конфигурация сети App Service](./media/secure-web-app/app-vnet-config.png)

    *Виртуальная конфигурация сети для службы приложений*

Теперь, когда вы включили виртуальную сетевую интеграцию, вы можете добавить группы сетевой безопасности в наше приложение.

1. Используйте окно поиска, ищите **группы сетевой безопасности.** Выберите **группы безопасности сети** в результатах.

    ![Поиск групп сетевой безопасности](./media/secure-web-app/nsg-search-menu.png)

    *Поиск групп сетевой безопасности*

2. В следующем меню выберите **Добавить**. Введите **название** NSG и **группы ресурсов,** в которой она должна быть расположена. Эта NSG будет применяться к подсети шлюза приложения.

    ![Создание NSG](./media/secure-web-app/nsg-create-new.png)

    *Создание NSG*

3. После создания NSG выберите его. В лезвии, под **настройками,** выберите **правила входящие безопасности.** Настроили эти настройки, чтобы соединения, поступающие в шлюз приложения, через порт 443.

   ![Настройка NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Настройка NSG*

4. В правилах исходящего шлюза NSG добавьте правило, которое позволяет исходящие соединения `AppService`в экземпляр службы приложений, создав правило, нацеленное на тег службы:

   ![Добавление исходящих правил для NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Добавление исходящих правил для NSG*

    Добавьте еще одно правило исходящего, чтобы шлюз отправлял исходящие правила в виртуальную сеть.

   ![Добавить еще одно исходящие правила](./media/secure-web-app/nsg-outbound-vnet.png)

    *Добавить еще одно исходящие правила*

5. На лезвии подсетей NSG выберите **Associate,** выберите виртуальную сеть, созданную в развертывании, и выберите подсеть шлюза под названием **GW-subnet.** NSG применяется к подсети.

6. Создайте еще один NSG, как и на предыдущем этапе, на этот раз для экземпляра службы приложений. Назови его. Добавьте входящий правило для порта 443, как это было для шлюза приложения NSG.

   Если в экземпляре Службы поддержки приложений развернут экземпляр Службы поддержки, что не относится к данному приложению, вы можете добавить входящие правила, позволяющие зондировать здоровье службы Azure, открыв порты 454-455 в входяных группах безопасности вашей службы безопасности NSG. Вот конфигурация:

   ![Добавление правил для зондов здоровья службы Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Добавление правил для зондов здоровья службы Azure (только среда службы приложений)*

7. В правилах безопасности исходящих создайте новое правило безопасности исходящих, которое позволяет экземпляру службы app общаться с базой данных PostgreS'L. Настроить его так:

   ![Правило, позволяющее исходящие подключения PostgreS'L](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Добавить правило, позволяющее исходящие подключения PostgreS'L*

Чтобы ограничить поверхность атаки, измените настройки сети Службы App, чтобы получить доступ к приложению только для шлюза приложения.
Вы делаете это, зайдя во вкладку сети Службы App, выбрав вкладку **IP Restrictions** и создав правило размнозания, которое позволяет только IP-коду шлюза приложения напрямую получить доступ к службе.

Вы можете получить IP-адрес шлюза со страницы обзора. На вкладке **IP-адреса CIDR** введите `<GATEWAY_IP_ADDRESS>/32`IP-адрес в этом формате: .

![Разрешить только шлюз](./media/secure-web-app/app-allow-gw-only.png)

*Разрешить доступ к Службе App Service только ip-адреса шлюза*

#### <a name="implement-azure-active-directory-oauth"></a>Реализация Активного каталога Azure OAuth

Документы Azure, распространяемые на странице примера веб-приложений, являются ресурсами в нашем приложении, которые могут нуждаться в защите. Для реализации аутентификации для веб-, настольных и мобильных приложений можно использовать Active Directory Azure (Azure AD), используя различные потоки аутентификации.
Приложение использует **Login With Microsoft**, что позволяет приложению читать профили пользователей, которые были добавлены в список пользователей Azure AD.

На портале Azure найди приложение для использования необходимых учетных данных:

1. Выберите **Активный каталог Azure**или ищите его с помощью окна поиска.

2. Выберите **новую регистрацию:**

   ![Создание регистрации](./media/secure-web-app/ad-auth-create.png)

   *Создание регистрации приложения Azure AD*

3. На следующей странице введите имя приложения. Под **типами поддерживаемых учетных записей**выберите **учетные записи только в этом каталоге организации.**
    Под **Перенаправлением URI**введите базовый домен, на который приложение будет работать плюс один с конечным топором. Например: *GATEWAY_HASH*.cloudapp.net/token.

   ![Настройка регистрации приложений Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Настройка регистрации приложений Azure AD*

4. Вам предоставляется экран, который показывает зарегистрированное приложение и его информацию. Эту информацию необходимо добавить в экземпляр Azure Key Vault.
   1. Копируйте идентификатор приложения (клиента) `CLIENTID`и сохраните его в Key Vault как.
   2. Скопируйте перенаправление URI, вкоторыйев в `REDIRECTURI`предыдущий шаг, и сохраните его как.
   3. Копирование имени каталога по умолчанию Azure AD с *названием*формата `TENANT`.microsoftonline.com и сохраните его в Key Vault как.
   4. Перейдите на вкладку **«Сертификаты & секреты»** приложения Azure AD, созданного ранее, и выберите **новый секрет клиента,** как показано на следующем скриншоте. Установите дату истечения срока действия, а затем скопируйте генерируемое значение и сохраните его в Key Vault как. `CLIENTSECRET`

      ![Секрет авторизации Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Секрет авторизации Azure AD*

   5. Создайте безопасный случайный секретный ключ с помощью любого командного инструмента/онлайн-инструмента. Сохранить его в `FLASKSECRETKEY`Key Vault как . Платформа приложения использует этот ключ для создания сеансов.
        Чтобы узнать, как создать секретный ключ, см [Флос Сессии](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. После настройки ввоза необходимо добавить пользователей в ссылку Azure AD, чтобы они могли войти в систему ресурса. Чтобы добавить их, перейдите на вкладку **пользователей** в Azure AD, выберите **всех пользователей,** а затем выберите **нового пользователя** или **нового гостя.** Для тестирования можно добавить гостевого пользователя и пригласить пользователя в каталог. Или вы можете добавить нового пользователя, если домен, на который работает приложение, был проверен. В этом примере только пользователи, зарегистрированные в арендаторе Azure AD, могут быть зарегистрированы для доступа. Для получения информации о доступе мультитенантных входов, см.

   ![Добавление пользователей в домен по умолчанию](./media/secure-web-app/ad-auth-add-user.png)

   *Добавление пользователей в домен Active Directory по умолчанию*

После добавления конфигурации Azure AD и секретов в Key Vault пользователи могут быть проверены в приложении с помощью аутентификации Azure OAuth.
В коде приложения это обрабатывается Библиотекой подлинности активных каталогов Azure (ADAL).

После того, как секреты находятся в Key Vault и приложение имеет доступ к секретамhttps://GATEWAY_HASH.cloudapp.net)и базе данных, служба приложения может быть достигнута через URL приложения шлюза (, который вы можете получить от его лезвия.

Если при входе в Azure AD вы получаете ошибку, в с помехавую: «Пользователь не зарегистрирован в каталоге, в который вы пытаетесь войти», необходимо добавить пользователя. Чтобы добавить пользователя, перейдите на вкладку **пользователей** Azure AD и добавьте пользователя вручную, введя его данные или пригласив пользователя, введя его адрес электронной почты в качестве приглашенного пользователя в Azure AD в лезвии **Invite Guest.**

#### <a name="deploy-application-insights"></a>Развертывание Application Insights
Теперь, когда приложение развернуто и работает, необходимо обрабатывать ошибки, возникающие в приложении, а также журнал и сбор данных.
Сбор данных для регистрации и отслеживания обеспечивает представление событий аудита, которые происходят в приложении.

Application Insights — это служба, которая собирает журналы, которые могут быть сгенерированы пользователями или системой.

Для создания экземпляра Application Insights:

1. Поиск **информации о приложениях** с помощью окна поиска на портале Azure.
2. Выберите **Application Insights**. Предоставьте детали, показанные здесь, чтобы создать экземпляр.

   ![Создание экземпляра «Исследования приложений»](./media/secure-web-app/app-insights-data.png)

После завершения развертывания у вас есть экземпляр Application Insights.

После создания экземпляра Applications Insights необходимо узнать о ключе приборов, который позволяет ему отправлять журналы в облако. Вы делаете это, извлекая ключ Application Insights и используя его в библиотеках приложений, которые Azure предоставляет для исследования приложений. Наилучшей практикой является хранение ключей и секретов в Azure Key Vault, чтобы обеспечить их безопасность.

Для базового примера приложения после создания экземпляра Applications Insights необходимо узнать о ключе приборов, который позволяет ему отправлять журналы в облако.
В Key Vault `APPINSIGHTSKEY` установите секрет и установите его ценность в качестве ключа приборов. Это позволяет приложению отправлять журналы и метрики в Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Внедрение мультифакторной аутентификации для активного каталога Azure

Администраторы должны обеспечить защиту учетных записей подписок на портале. Подписка уязвима для атак, поскольку она управляет созданными ресурсами. Для защиты подписки включите многофакторную аутентификацию на вкладке **Azure Active Directory** подписки.

Azure AD работает на основе политик, которые применяются к пользователю или группам пользователей, которые соответствуют определенным критериям.
Azure создает политику по умолчанию, определяющую, что администраторам нужна двухфакторная аутентификация для вхинга на портал.
После включения этой политики вам может быть предложено войти в систему и войти обратно на портал Azure.

Для того, чтобы МИД для админ-подвоза:

1. Перейдите на вкладку **Active Directory Azure** на портале Azure
2. Под категорией безопасности выберите условный доступ. Появляется следующий экран:

   ![Условный доступ - Политики](./media/secure-web-app/ad-mfa-conditional-add.png)

Если вы не можете создать новую политику:

1. Перейдите на вкладку **МИД.**
2. Выберите **бесплатную пробную** ссылку Azure AD Premium, чтобы подписаться на бесплатную пробную версию.

   ![Бесплатная пробная версия Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Возвращение на условный экран доступа.

1. Выберите новую вкладку политики.
2. Введите имя политики.
3. Выберите пользователей или группы, для которых вы хотите включить MFA.
4. Под **управлением доступа**выберите вкладку **Гранта,** а затем выберите **многофакторную аутентификацию** (и другие параметры, если хотите).

   ![Требовать многофакторную идентификацию](./media/secure-web-app/ad-mfa-conditional-add.png)

Вы можете включить политику, выбрав флажок в верхней части экрана или сделайте это на вкладке **«Условный доступ».** Когда политика включена, пользователям необходимо, чтобы МИД вписался на портал.

Существует базовая политика, требующая МИД для всех администраторов Azure. Вы можете включить его сразу на портале. Включение этой политики может привести к аннулированию текущей сессии и заставить вас войти снова.

Если базовая политика не включена:

1. Выберите **Требование МИД для админов.**
2. Немедленно выберите **политику использования.**

   ![Немедленно выберите политику использования](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Используйте Azure Sentinel для мониторинга приложений и ресурсов

По мере роста приложения становится трудно агрегировать все сигналы безопасности и метрики, полученные от ресурсов, и делать их полезными в ориентированном на действия виде.

Azure Sentinel предназначен для сбора данных, обнаружения возможных типов угроз и обеспечения видимости инцидентов безопасности.
В то время как он ждет ручного вмешательства, Azure Sentinel может полагаться на заранее написанные игровые книги, чтобы начать оповещения и процессы управления инцидентами.

Пример приложения состоит из нескольких ресурсов, которые Azure Sentinel может контролировать.
Чтобы настроить Azure Sentinel, сначала необходимо создать рабочее пространство для анализа журналов, в которое хранятся все данные, собранные из различных ресурсов.

Для создания этого рабочего пространства:

1. В поле поиска на портале Azure ищите **аналитику журнала.** Выберите **Рабочие области Log Analytics**.

   ![Поиск рабочих областей анализа журналов](./media/secure-web-app/sentinel-log-analytics.png)

    *Поиск рабочих областей анализа журналов*

2. На следующей странице выберите **Добавить,** а затем укажите имя, группу ресурсов и местоположение рабочего пространства.
   ![Создание рабочей области Log Analytics с помощью PowerShell](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Создание рабочего пространства анализа журналов*

3. Используйте поле поиска для поиска **Azure Sentinel.**

   ![Поиск по фразе "Azure Sentinel"](./media/secure-web-app/sentinel-add.png)

    *Поиск Azure Sentinel*

4. Выберите **Добавить,** а затем выберите рабочее пространство Log Analytics, созданное ранее.

   ![Добавление рабочего пространства анализа журналов](./media/secure-web-app/sentinel-workspace-add.png)

    *Добавление рабочего пространства анализа журналов*

5. На **странице Разъемов данных Azure Sentinel - Под** **конфигурацией**выберите **разъемы данных.** Вы видите массив служб Azure, которые можно связать с экземпляром хранения журналов Analytics для анализа в Azure Sentinel.

   ![Разъемы данных «Аналитика журнала»](./media/secure-web-app/sentinel-connectors.png)

    *Добавление разъема данных в Azure Sentinel*

   Например, чтобы подключить шлюз приложения, следующие действия:

   1. Откройте лопасти экземпляра экземпляра Azure Application Gateway.
   2. В разделе **Мониторинг** выберите **Параметры диагностики**.
   3. Выберите **Диагностическую настройку добавления.**

      ![Добавление диагностики шлюзов приложений](./media/secure-web-app/sentinel-gateway-connector.png)

      *Добавление диагностики шлюзов приложений*

   4. На странице **«Диагностические настройки»** выберите созданное вами рабочее пространство «Аналитика журнала», а затем выберите все метрики, которые вы хотите собрать и отправить в Azure Sentinel. Щелкните **Сохранить**.

        ![Настройки разъема Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Настройки разъема Azure Sentinel*

  Метрики ресурса находятся в Azure Sentinel, где их можно заделать и исследовать.

   Добавьте те же метрики в диагностические настройки для Azure Key Vault, общедоступного IP-адреса, базы данных Azure для PostgreS'L и любых служб, поддерживающих диагностические журналы в вашей учетной записи.

После настройки метрик Azure Sentinel должен анализировать данные.

## <a name="evaluate-and-verify"></a>Оценка и проверка

После разработки и развертывания архитектуры необходимо убедиться, что код и развернутые службы соответствуют стандартам безопасности. Вот некоторые шаги, которые вы можете предпринять для проверки программного обеспечения:

- статический анализ кода;
- Сканирование уязвимостей
- Поиск и исправление уязвимостей в зависимостях приложений

Это основные строительные блоки для наилучшей практики в области безопасной разработки.

### <a name="static-code-analysis"></a>статический анализ кода;

Для примера приложения проверка с помощью статических инструментов анализа включает поиск уязвимостей в коде приложения с помощью таких методов, как проверка загрязнения и анализ потока данных. Инструменты статического анализа Python дают вам больше уверенности в том, что ваше приложение является безопасным.

**Выделение**

PyFlakes, библиотека Python, помогает удалять мертвый код и неиспользованные функции из приложений, как показано здесь:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting предоставляет подсказки и возможные изменения, которые могут сделать ваш код чище и менее подверженным ошибкам во время выполнения.

**PyLint**

PyLint обеспечил асамое значение для этого проекта. Он выполняет проверки стандарта кода, проверку ошибок и рефакторинг советы, чтобы убедиться, что код работает на сервере является безопасным. С помощью PyLint для обновления кода можно устранить ошибки и улучшить рейтинг PyLint, как показывают следующие изображения.

![Перед PyLint](./media/secure-web-app/before-pylint.png)

*Перед PyLint*

После исправления некоторых ошибок кода, найденных инструментами линтинга, у вас будет больше уверенности в том, что код не подвержен ошибкам. Исправление ошибок значительно снижает риски безопасности, которые могут возникнуть при развертывании кода в производственных средах.

![После Pylint](./media/secure-web-app/after-pylint.png)

*После PyLint*

### <a name="vulnerability-scanning"></a>Сканирование уязвимостей

Инструмент [OWASP в ЗАП](https://www.zaproxy.org/) является открытым исходным кодом веб-приложений сканер уязвимости, которые можно использовать для проверки образца приложения на наличие уязвимостей. Запуск инструмента в примере приложения показывает некоторые возможные ошибки и векторы атак.

![Инструмент ЗАП](./media/secure-web-app/zap-tool.png)

*Инструмент ЗАП*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Поиск и исправление уязвимостей в зависимостях приложений

Чтобы найти и исправить зависимости приложений, можно использовать [проверку зависимостей OWASP.](https://www.owasp.org/index.php/OWASP_Dependency_Check)

Безопасность — это аналогичное приложение, которое проверяет зависимости. Вы можете найти его на [GitHub](https://github.com/pyupio/safety). Сканирование уязвимостей, обнаруженных в хорошо известных базах данных уязвимостей.

![Безопасность](./media/secure-web-app/pysafety.png)

*Безопасности*

## <a name="next-steps"></a>Дальнейшие действия

Следующие статьи помогут вам разрабатывать, разрабатывать и развертывать безопасные приложения.

- [Конструирование](secure-design.md)
- [Разработка](secure-develop.md)
- [Развернуть](secure-deploy.md)
