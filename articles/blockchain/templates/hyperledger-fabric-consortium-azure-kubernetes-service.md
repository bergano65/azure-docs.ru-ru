---
title: Microsoft Kubernetes Service Fabric Consortium в службе Azure (AKS)
description: Развертывание и настройка сети консорциума Kubernetes для структуры Microsoft Azure
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 93f78f2c8bc32a2012e5635e0daec10b8c51d167
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901634"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Microsoft Kubernetes Service Fabric Consortium в службе Azure (AKS)

Для развертывания и настройки сети "консорциум по структуре" в Azure можно использовать шаблон Service Fabric (ХЛФ) в службе Kubernetes Azure (AKS).

После прочтения этой статьи вы узнаете:

- Получите опыт работы со структурой «Главная книга» и различные компоненты, которые формируют стандартные блоки блокчейн Network структуры.
- Узнайте, как развернуть и настроить консорциум по работе с Kubernetes в службе Azure для рабочих сценариев.

## <a name="hyperledger-fabric-consortium-architecture"></a>Архитектура консорциума по этой структуре в книге

Чтобы создать сеть структуры «Главная книга» в Azure, необходимо развернуть службу упорядочения и организацию с одноранговыми узлами. Ниже перечислены различные фундаментальные компоненты, создаваемые в рамках развертывания шаблона.

- **Узлы заказов**. узел, отвечающий за упорядочение транзакций в главной книге. Вместе с другими узлами упорядоченные узлы формируют службу упорядочения в сети структуры «Главная книга».

- **Одноранговые узлы**: узел, в котором главным образом размещаются книги учета и интеллектуальные контракты, эти фундаментальные элементы сети.

- **CA Fabric**. ЦС структуры — это центр сертификации (CA) для структуры "Главная книга". ЦС структуры позволяет инициализировать и запустить серверный процесс, на котором размещается центр сертификации. Она позволяет управлять удостоверениями и сертификатами. Каждый кластер AKS, развернутый как часть шаблона, по умолчанию будет иметь модуль ЦС Fabric.

- **CouchDB или левелдб**: база данных состояния мира для одноранговых узлов может храниться либо в левелдб, либо в CouchDB. Левелдб — это база данных состояния по умолчанию, внедренная в одноранговый узел и хранящая данные чаинкоде в виде простых пар "ключ-значение" и поддерживающих только запросы ключей, диапазонов ключей и составных ключевых запросов. CouchDB — это необязательная альтернативная база данных состояний, которая поддерживает расширенные запросы, когда чаинкоде значения данных моделируются как JSON.

Шаблон при развертывании поворачивает различные ресурсы Azure в подписке. Развернутые ресурсы Azure:

- **Кластер AKS**: кластер Kubernetes Azure, настроенный в соответствии с входными параметрами, предоставленными клиентом. В кластере AKS есть различные модули, настроенные для работы сетевых компонентов структуры «книга». Различные созданные модули Pod:

  - **Средства структуры**. средство структуры отвечает за настройку компонентов структуры в книге.
  - Модули **(одноранговые**модули): узлы сети хлф.
  - **Прокси**— модуль-посредник нгникс, через который клиентские приложения могут взаимодействовать с кластером AKS.
  - **CA Fabric**: модуль, на котором работает ЦС структуры.
- **PostgreSQL**: экземпляр PostgreSQL развертывается для поддержки удостоверений ЦС структуры.

- **Хранилище ключей Azure**. экземпляр хранилища ключей развернут для сохранения учетных данных ЦС структуры и корневых сертификатов, предоставляемых клиентом, который используется в случае повторной попытки развертывания шаблона. это необходимо для управления механикой шаблона.
- **Управляемый диск Azure**: управляемый диск Azure предназначен для постоянного хранения для главной базы данных и однорангового узла.
- **Общедоступный IP-адрес**: конечная точка общедоступного IP-адреса кластера AKS, развернутая для взаимодействия с кластером.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Настройка сети в Блокчейн Fabric

Чтобы приступить к работе, вам понадобится подписка Azure, которая может поддерживать развертывание нескольких виртуальных машин и стандартные учетные записи хранения. Если у вас еще нет ее, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).

Настройте Блокчейн Network для фабрики, выполнив следующие действия.

- [Развертывание заказа или одноранговой Организации](#deploy-the-ordererpeer-organization)
- [Создание консорциума](#build-the-consortium)
- [Выполнение собственных операций ХЛФ](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Развертывание заказа или одноранговой Организации

Чтобы начать работу с развертыванием сетевых компонентов ХЛФ, перейдите к [портал Azure](https://portal.azure.com). Выберите **создать ресурс > блокчейн** > поиска **в службе Kubernetes Azure**.

1. Выберите **создать** , чтобы начать развертывание шаблона. Откроется **служба создания фабрики Kubernetes в Azure** .

    ![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Введите сведения о проекте на странице " **основные** ".

    ![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Введите следующие сведения:
    - **Подписка**: выберите имя подписки, в которой вы хотите развернуть сетевые компоненты хлф.
    - **Группа ресурсов**. Создайте новую группу ресурсов или выберите существующую пустую группу ресурсов. Группа ресурсов будет содержать все ресурсы, развернутые в составе шаблона.
    - **Регион**: Выберите регион Azure, в котором вы хотите развернуть кластер Azure Kubernetes для компонентов хлф. Шаблон доступен во всех регионах, где AKS доступен для выбора региона, в котором ваша подписка не достигает предельной квоты виртуальной машины.
    - **Префикс ресурса**: префикс для именования развертываемых ресурсов. Длина префикса ресурса должна быть меньше шести символов, а сочетание символов должно содержать как цифры, так и буквы.
4. Выберите вкладку **Параметры структуры** , чтобы определить компоненты сети хлф, которые будут развернуты.

    ![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Введите следующие сведения:
    - **Название организации**: имя организации структуры, которая необходима для различных операций с плоскостью данных.
    - **Компонент Fabric Network**: выберите либо службу упорядочения, либо одноранговые узлы, основанные на сетевом компоненте блокчейн, который вы хотите настроить.
    - **Число узлов** . ниже приведены два типа узлов.
        - Служба упорядочения — выберите число узлов, для которых была предоставлена отказоустойчивость сети. Количество узлов заказа поддерживается только для трех, 5 и 7.
        - Одноранговые узлы. Вы можете выбрать 1-10 узлов в соответствии с вашими требованиями.
    - **База данных состояния мира однорангового узла**: выберите между Левелдб и каукбдб. Это поле отображается, когда пользователь выбирает одноранговый узел в раскрывающемся списке сетевой компонент структуры.
    - **Имя пользователя структуры**. Введите имя пользователя, используемое для проверки подлинности ЦС структуры.
    - **Пароль ЦС структуры**. Введите пароль для проверки подлинности ЦС структуры.
    - **Подтверждение пароля**: Подтвердите пароль ЦС структуры.
    - **Сертификаты**. Если вы хотите использовать собственные корневые сертификаты для инициализации ЦС структуры, выберите параметр Отправить корневой сертификат для центра сертификации. в противном случае по умолчанию центр сертификации создает самозаверяющие сертификаты.
    - **Корневой сертификат**: Отправьте корневой сертификат (открытый ключ), с помощью которого необходимо инициализировать ЦС Fabric. Поддерживаются сертификаты формата PEM, сертификаты должны быть действительны в часовом поясе UTC.
    - **Закрытый ключ корневого сертификата**. Отправьте закрытый ключ корневого сертификата. Если у вас есть PEM-сертификат, который объединяет открытый и закрытый ключи, отправьте его здесь.


6. Выберите вкладку **Параметры кластера AKS** , чтобы определить конфигурацию кластера Azure Kubernetes, которая является базовой инфраструктурой, в которой будут настраиваться сетевые компоненты структуры.

    ![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Введите следующие сведения:
    - **Имя кластера Kubernetes**. имя создаваемого кластера AKS. Это поле заполняется в соответствии с указанным префиксом ресурса, при необходимости можно изменить.
    - **Версия Kubernetes**. версия Kubernetes, которая будет развернута в кластере. В зависимости от региона, выбранного на вкладке « **основы** », доступные версии могут измениться.
    - **Префикс DNS**: префикс имени системы доменных имен (DNS) для кластера AKS. Вы будете использовать DNS для подключения к API Kubernetes при управлении контейнерами после создания кластера.
    - **Размер узла**. размер узла Kubernetes, который можно выбрать в списке номеров SKU виртуальной машины, доступных в Azure. Для оптимальной производительности рекомендуется использовать стандартные DS3 версии 2.
    - **Число узлов**: количество узлов Kubernetes, которые должны быть развернуты в кластере. Рекомендуется, чтобы количество узлов в параметрах Fabric не превышало, чем указано или больше.
    - **Идентификатор клиента субъекта-службы**. Введите идентификатор клиента существующего субъекта-службы или создайте новый, необходимый для проверки подлинности AKS. См. раздел, пошаговые инструкции по [созданию субъекта-службы](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Секрет клиента субъекта-службы**. Введите секрет клиента субъекта-службы, указанный в идентификаторе клиента субъекта-службы.
    - **Подтверждение секрета клиента**: подтвердите секрет клиента, указанный в секрете клиента субъекта-службы.
    - **Включить мониторинг контейнеров**: выберите Включение мониторинга AKS, которое позволяет журналам AKS отправлять данные в указанную рабочую область log Analytics.
    - **Рабочая область log Analytics**: Рабочая область log Analytics будет заполнена рабочей областью по умолчанию, созданной при включенном мониторинге.

8. После предоставления всех указанных выше сведений выберите вкладку **Проверка и создание** . Проверка и создание активирует проверку предоставленных значений.
9. После прохождения проверки можно нажать кнопку **создать**.
Развертывание обычно занимает 10-12 минут, в зависимости от размера и количества указанных узлов AKS.
10. После успешного развертывания вы получите уведомление через уведомления Azure в правом верхнем углу.
11. Выберите **Переход к группе ресурсов** , чтобы проверить все ресурсы, созданные в ходе развертывания шаблона. Все имена ресурсов будут начинаться с префикса, указанного в параметре **основы** .

## <a name="build-the-consortium"></a>Создание консорциума

Чтобы построить блокчейн Consortium после развертывания службы упорядочения и одноранговых узлов, необходимо выполнить последовательность действий ниже. **Создайте свой сетевой** сценарий (byn.sh), который поможет вам настроить консорциум, создать канал и установить чаинкоде.

> [!NOTE]
> Создание сценария сети (бин) является исключительно для использования в сценариях демонстрации и devtest. Для настройки производственного уровня мы рекомендуем использовать собственные API ХЛФ.

Все команды для выполнения скрипта бин можно выполнить с помощью интерфейса командной строки Azure bash (CLI). Вы можете войти в веб-версию оболочки Azure с помощью ![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) в правом верхнем углу портал Azure. В командной строке введите Bash и введите, чтобы переключиться на интерфейс командной строки bash.

Дополнительные сведения см. в разделе [оболочка Azure](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Шаблон "структура" в Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Скачайте файл byn.sh и Fabric-admin. YAML.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Задайте следующие переменные среды в Azure CLI оболочке bash**:

Задание сведений о канале и сведений об организации заказа

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Задание сведений о одноранговой Организации

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Создайте один файловый ресурс Azure для совместного использования различных общедоступных сертификатов в организациях одноранговых и последовательностей.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Команды управления каналами**

Чтобы создать канал, перейдите в раздел заказа организация AKS Cluster и выдайте команду.

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Команды управления консорциумом**

Выполните приведенные ниже команды в указанном порядке, чтобы добавить одноранговую организацию в канал и консорциум.

1. Перейдите к кластеру Peer AKS и отправьте службу-член (MSP) в хранилище файлов Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Перейдите в раздел "Организация Организации AKS кластера" и добавьте одноранговую организацию в канал и консорциум.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Вернитесь к одноранговой Организации и выдайте команду, чтобы присоединить одноранговые узлы в канале.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Аналогичным образом, чтобы добавить в канал другие одноранговые Организации, обновите переменные среды AKS в соответствии с требуемой одноранговой Организацией и выполните шаги 1 – 3.

**Команды управления чаинкоде**

Выполните приведенную ниже команду, чтобы выполнить операцию, связанную с чаинкоде. Эти команды выполняют все операции с демонстрационной чаинкоде. Эта демонстрационная версия чаинкоде имеет две переменные "a" и "b". При создании экземпляра чаинкоде инициализируется с 1000, а "b" инициализируется с помощью 2000. При каждом вызове чаинкоде 10 единиц передаются из "a" в "b". Операция запроса в чаинкоде показывает мировое состояние переменной "a".

Выполните следующие команды, выполняемые в кластере AKS для одноранговой Организации.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Команды операции чаинкоде**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Выполнение собственных операций ХЛФ

Чтобы помочь клиентам начать работу с собственными командами в ХЛФ Network на AKS. Предоставляется пример приложения, в котором для выполнения операций ХЛФ используется Fabric NodeJS SDK. Эти команды предназначены для создания нового удостоверения пользователя и установки собственного чаинкоде.

### <a name="before-you-begin"></a>Перед началом работы

Выполните следующие команды для первоначальной настройки приложения:

- Скачать файлы приложения
- Создать профиль подключения и профиль администратора
- Импорт удостоверения пользователя администратора

После завершения начальной настройки можно использовать пакет SDK для выполнения следующих операций:

- Создание удостоверения пользователя
- Операции чаинкоде

Описанные выше команды можно выполнять из Azure Cloud Shell.

### <a name="download-application-files"></a>Скачать файлы приложения

Первая установка для запуска приложения — Загрузка всех файлов приложения в папку.

**Создайте папку приложения и введите в папку**:

```bash
mkdir app
cd app
```
Выполните приведенную ниже команду, чтобы скачать все необходимые файлы и пакеты.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Эта команда требует времени для загрузки всех пакетов. После успешного выполнения команды вы увидите папку `node_modules` в текущем каталоге. Все необходимые пакеты загружаются в папку `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Создать профиль подключения и профиль администратора

Создание `profile` каталога в папке `app`

```bash
cd app
mkdir ./profile
```
Задайте эти переменные среды в Azure Cloud Shell.

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Выполните приведенную ниже команду, чтобы создать профиль подключения и профиль администратора организации.

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Он создаст профиль подключения и `profile` администрирования организации в папке профиля с именем `<orgname>-ccp.json` и `<orgname>-admin.json` соответственно.

Аналогичным образом создайте профиль подключения и профиль администратора для каждого заказа и одноранговой Организации.


### <a name="import-admin-user-identity"></a>Импорт удостоверения пользователя администратора

Последним шагом является импорт удостоверения пользователя администратора организации в бумажнике.

```bash
npm run importAdmin -- -o <orgName>

```
Приведенная выше команда выполняет Импортадмин. js для импорта удостоверения пользователя администратора в бумажник. Сценарий считывает удостоверение администратора из профиля администратора `<orgname>-admin.json` и импортирует его в бумажник для выполнения операций ХЛФ.

Скрипты используют бумажник файловой системы для хранения удостоверений. Он создает бумажник в соответствии с путем, указанным в поле ". бумажник" в профиле подключения. По умолчанию поле ". бумажник" инициализируется с помощью `<orgname>`. Это означает, что папка с именем `<orgname>` создается в текущем каталоге для хранения удостоверений. Если вы хотите создать бумажник на другом пути, измените поле ". бумажник" в профиле подключения перед выполнением команды "регистрация пользователя администратора" и любых других операций ХЛФ.

Аналогичным образом импортируйте удостоверение пользователя администратора для каждой организации.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Создание удостоверения пользователя

Выполните приведенные ниже команды в указанном порядке, чтобы создать новые удостоверения пользователей для ХЛФ Организации.

> [!NOTE]
> Перед началом работы с шагами создания удостоверения пользователя убедитесь, что начальная настройка приложения выполнена.

Задать следующие переменные среды в Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Регистрация и регистрация нового пользователя

Для регистрации и регистрации нового пользователя выполните приведенную ниже команду, которая выполняет registerUser. js. Он сохраняет удостоверение созданного пользователя в бумажнике.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Удостоверение пользователя "Администратор" используется для выдаче команды регистрации для нового пользователя. Поэтому перед выполнением этой команды обязательно необходимо, чтобы в бумажнике имелось удостоверение пользователя с правами администратора. В противном случае эта команда завершится ошибкой.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Операции чаинкоде


> [!NOTE]
> Перед началом выполнения любой операции чаинкоде убедитесь, что начальная настройка приложения выполнена.

Ниже перечислены чаинкоде конкретные переменные среды в Azure Cloud Shell:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Ниже перечислены чаинкоде операции, которые могут быть выполнены.

- Установка чаинкоде
- Создание экземпляра чаинкоде
- Вызвать чаинкоде
- Чаинкоде запросов

### <a name="install-chaincode"></a>Установка чаинкоде

Выполните приведенную ниже команду, чтобы установить чаинкоде в одноранговой Организации.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Он установит чаинкоде на всех одноранговых узлах организации, установленной в `ORGNAME` переменной среды. Если в канале есть две или более одноранговых Организации и вы хотите установить чаинкоде на всех этих узлах, выполните эти команды отдельно для каждой одноранговой Организации.

Выполните следующие действия.

- Задайте для параметра `ORGNAME` значение `<peerOrg1Name>` и выполните команду `installCC`.
- Задайте для параметра `ORGNAME` значение `<peerOrg2Name>` и выполните команду `installCC`.

  Выполните его для каждой одноранговой Организации.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Создание экземпляра чаинкоде

Выполните приведенную ниже команду, чтобы создать экземпляр чаинкоде на одноранговом узле.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Имя функции перед созданием экземпляра и список аргументов, разделенных запятыми, в `<instantiateFunc>` и `<instantiateFuncArgs>` соответственно. Например, в [фабркар чаинкоде](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), чтобы создать экземпляр чаинкоде Set `<instantiateFunc>` для `"Init"` и `<instantiateFuncArgs>` в пустую строку `""`.

> [!NOTE]
> Выполните команду один раз из любой одноранговой Организации в канале.
> После успешной отправки транзакции в заказ, заказ распределяет эту транзакцию всем одноранговым организациям в канале. Таким образом, экземпляр чаинкоде создается на всех одноранговых узлах во всех одноранговых организациях в канале.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Вызвать чаинкоде

Выполните приведенную ниже команду, чтобы вызвать функцию чаинкоде:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Передайте имя функции вызова и список аргументов, разделенных запятыми, в `<invokeFunction>` и `<invokeFuncArgs>` соответственно. Продолжение работы с примером чаинкоде фабкар для вызова функции Инитледжер с набором `<invokeFunction>` `"initLedger"` и `<invokeFuncArgs>` `""`.

> [!NOTE]
> Выполните команду один раз из любой одноранговой Организации в канале.
> После успешной отправки транзакции в заказ, заказ распределяет эту транзакцию всем одноранговым организациям в канале. Таким образом, состояние мира обновляется на всех одноранговых узлах всех одноранговых организаций в канале.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Чаинкоде запросов

Выполните приведенную ниже команду, чтобы запросить чаинкоде:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Передайте имя функции запроса и список аргументов, разделенных запятыми, в `<queryFunction>` и `<queryFuncArgs>` соответственно. Опять же, `fabcar` чаинкоде в качестве ссылки, чтобы запрашивать все автомобили в наборе Штатов `<queryFunction>` для `"queryAllCars"` и `<queryArgs>` в `""`.

Дополнительные сведения о аргументах, передаваемых в команде, см. в справке по командам.

```bash
npm run queryCC -- -h

```
