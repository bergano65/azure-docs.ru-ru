---
title: Консорциум Hyperledger Fabric на Сервисе Azure Kubernetes (AKS)
description: Как развернуть и настроить сеть консорциума Hyperledger Fabric в сервисе Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476446"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Консорциум Hyperledger Fabric на Сервисе Azure Kubernetes (AKS)

Для развертывания и настройки сети консорциума Hyperledger Fabric (HLF) в Azure Kubernetes Service (AKS) можно использовать шаблон Hyperledger Fabric (HLF).

После прочтения этой статьи вы узнаете:

- Получите рабочие знания Hyperledger Fabric и различных компонентов, которые составляют строительные блоки блокчейн-сети Hyperledger Fabric.
- Узнайте, как развернуть и настроить консорциум Hyperledger Fabric в службу Azure Kubernetes для сценариев производства.

## <a name="hyperledger-fabric-consortium-architecture"></a>Архитектура консорциума Hyperledger Fabric

Для создания сети Hyperledger Fabric на Azure необходимо развернуть службу заказа и организацию с помощью одноранговых узлов. Различные фундаментальные компоненты, которые создаются в рамках развертывания шаблона:

- **Узлы заказа:** узла, который отвечает за заказ транзакций в книге. Наряду с другими узлами, упорядоченные узлы образуют сервис заказа сети Hyperledger Fabric.

- **Одноранговые узлы**: узла, который в основном размещает книги и смарт-контракты, эти фундаментальные элементы сети.

- **Ткань CA**: Ткань CA является сертификат органа (CA) для Hyperledger Ткань. Fabric CA позволяет инициализировать и начать процесс сервера, в котором размещается сертификат. Это позволяет управлять удостоверениями и сертификатами. Каждый кластер AKS, развернутый в рамках шаблона, будет иметь стручок Fabric CA по умолчанию.

- **CouchDB или LevelDB**: Мировая база данных состояния для одноранговых узлов может храниться либо в LevelDB, либо в CouchDB. LevelDB — это база данных состояния по умолчанию, встроенная в одноранговый узла и хранит данные цепного кода как простые пары ключей и поддерживает только ключевые, ключевые диапазоны и составные ключевые запросы. CouchDB — это дополнительная альтернативная база данных состояния, которая поддерживает богатые запросы при моделировании значений данных цепного кода как JSON.

Шаблон развертывания раскручивает различные ресурсы Azure в вашей подписке. Развертываемые различные ресурсы Azure:

- **Кластер AKS**: кластер Azure Kubernetes, настроенный в рамках параметров ввода, предоставляемых заказчиком. Кластер AKS имеет различные стручки, настроенные для запуска компонентов сети Hyperledger Fabric. Различные стручки созданы являются:

  - **Ткань инструменты**: Ткань инструмент отвечает за настройку компонентов Hyperledger Ткань.
  - **Заказатель/равные стручки:** узлы сети HLF.
  - **Прокси**: прокси-капсула NGNIX, через которую клиентские приложения могут взаимодействовать с кластером AKS.
  - **Ткань CA**: стручок, который работает Ткань CA.
- **PostgreS'L**: Для поддержания идентификационных данных Fabric CA используется экземпляр PostgreS'L.

- **Хранилище ключей Azure:** Для сохранения учетных данных Fabric CA и корневых сертификатов, предоставленных клиентом, используется хранилище ключей: для обработки механики шаблона используется экземпляр хранилища.
- **Управляемый диск Azure:** Управляемый azure диск предназначен для постоянного хранения базы данных состояния гефрейсов и одноранговых узлов.
- **Public IP**: Публичная конечная точка IP кластера AKS, развернутая для взаимосвязанного с кластером.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Гиперледжер Ткань Blockchain настройки сети

Чтобы приступить к работе, вам понадобится подписка Azure, которая может поддерживать развертывание нескольких виртуальных машин и стандартные учетные записи хранения. Если у вас еще нет ее, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/).

Настройка сети Hyperledger Fabric Blockchain с использованием следующих шагов:

- [Развертывание организации заказа/равного](#deploy-the-ordererpeer-organization)
- [Создание консорциума](#build-the-consortium)
- [Запуск нативных операций HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Развертывание организации заказа/равного

Чтобы начать развертывание компонентов сети HLF, перейдите на [портал Azure.](https://portal.azure.com) Выберите **Создать ресурс > Blockchain** > поиск **hyperledger Fabric на службе Azure Kubernetes.**

1. Выберите **создать** для начала развертывания шаблона. Создание Hyperledger Fabric на дисплеях **службы Azure Kubernetes.**

    ![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Введите сведения о проекте на странице **Basics.**

    ![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Введите следующие сведения:
    - **Подписка**: Выберите имя подписки, где вы хотите развернуть компоненты сети HLF.
    - **Группа ресурсов:** либо создайте новую группу ресурсов, либо выберите существующую пустую группу ресурсов, группа ресурсов будет удерживать все ресурсы, развернутые в составе шаблона.
    - **Регион**: Выберите область Azure, где требуется развернуть кластер Azure Kubernetes для компонентов HLF. Шаблон доступен во всех регионах, где доступна AKS Убедитесь в том, что вы выбираете регион, в котором ваша подписка не будет ограничена квотой Virtual Machine (VM).
    - **Приставка ресурсов**: префикс для именования развернутых ресурсов. Приставка ресурса должна быть меньше шести символов в длину, а комбинация символов должна включать как цифры, так и буквы.
4. Выберите **вкладку Fabric Settings** для определения компонентов сети HLF, которые будут развернуты.

    ![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Введите следующие сведения:
    - **Название организации**: Название организации Fabric, которая требуется для различных операций плоскости данных. Название организации должно быть уникальным для развертывания. 
    - **Компонент сети Fabric**: Выберите либо службу заказа, либо узлов Peer на основе компонента сети Blockchain, который вы хотите настроить.
    - **Количество узлов** - Следующие типы узлов:
        - Сервис заказа - выберите количество узлов для обеспечения переносимости неисправностей в сети. Только 3,5 и 7 являются поддерживаемым количеством узлов заказа.
        - Одноранговые узлы - вы можете выбрать 1-10 узлов в зависимости от вашего требования.
    - **База данных всемирного состояния узлов peer:** Выберите между LevelDB и CoucbDB. Это поле отображается, когда пользователь выбирает одноранговый узла в сетевом компоненте Fabric.
    - **Имя пользователя Fabric**: Введите имя пользователя, которое используется для проверки подлинности Fabric CA.
    - **Ткань CA пароль**: Введите пароль для проверки подлинности Fabric CA.
    - **Подтвердите пароль:** Подтвердите пароль Fabric CA.
    - **Сертификаты**: Если вы хотите использовать свои собственные корневые сертификаты для инициализации Fabric CA, то выберите корневой сертификат Upload для опции Fabric CA, по умолчанию Fabric CA создает сертификаты, подписанные самостоятельно.
    - **Корневой сертификат**: Загрузить корневой сертификат (открытый ключ), с которым Fabric CA необходимо инициализировать. Сертификаты формата .pem поддерживаются, сертификаты должны быть действительны в часовом поясе UTC.
    - **Частный ключ корневого сертификата:** Загрузить закрытый ключ корневого сертификата. Если у вас есть сертификат .pem, который имеет как государственный, так и частный ключ вместе взятые, загрузите его и здесь.


6. Выберите **вкладку кластера AKS** для определения конфигурации кластера Azure Kubernetes, которая является основной инфраструктурой, на которой будут настроены компоненты сети Fabric.

    ![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Введите следующие сведения:
    - **Kubernetes кластера имя**: Название кластера AKS, который создается. Это поле предварительно заселено на основе приставки ресурса, при условии, что вы можете изменить при необходимости.
    - **Kubernetes версия**: Версия Kubernetes, которые будут развернуты на кластере. В зависимости от региона, выбранного во вкладке **Basics,** доступные поддерживаемые версии могут изменяться.
    - **Префикс DNS**: Система доменных имен (DNS) — приставка для кластера AKS. При управлении контейнерами после создания кластера при управлении контейнерами будет использоваться DNS для подключения к API Kubernetes.
    - **Размер узла**: Размер узла Kubernetes, вы можете выбрать из списка единицы хранения vM Stock (SKUs), доступной на Azure. Для оптимальной производительности мы рекомендуем Standard DS3 v2.
    - **Количество узлов**: Количество узлов Kubernetes, которые будут развернуты в кластере. Мы рекомендуем держать этот узлы по крайней мере равныили или больше, чем количество узлов HLF, указанных в настройках Fabric.
    - **Основной идентификатор клиента**службы: Введите идентификатор клиента существующего директора службы или создайте новый, который необходим для проверки подлинности AKS. См., шаги по [созданию основного сервиса](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Секрет клиента службы:** Введите секрет клиента основного сервиса, предоставленного в идентификаторе основного клиента службы.
    - **Подтвердите секрет клиента:** Подтвердите секрет клиента, предоставленную в сервисе основной клиентской тайной.
    - **Включите мониторинг контейнеров**: Выберите для включения мониторинга AKS, который позволяет журналам AKS продвигаться к указанному рабочему пространству Log Analytics.
    - **Рабочее пространство log Analytics:** Рабочее пространство аналитики журнала будет заполнено рабочим пространством по умолчанию, которое создается при включении мониторинга.

8. После предоставления всех вышеперечисленных деталей выберите **обзор и создайте** вкладку. Проверка и создание вызывает проверку для предоставленных значений.
9. Как только проверка проходит, можно выбрать **создать.**
Развертывание обычно занимает 10-12 минут, может варьироваться в зависимости от размера и количества указанных узлов AKS.
10. После успешного развертывания вы будете уведомлены через уведомления Azure в правом верхнем углу.
11. Выберите **Перейти в группу ресурсов,** чтобы проверить все ресурсы, созданные в рамках развертывания шаблона. Все имена ресурсов будут начинаться с префикса, предоставленного в **настройках Basics.**

## <a name="build-the-consortium"></a>Создание консорциума

Чтобы построить блокчейн-консорциум, развернув службу заказа и одноранговые узлы, необходимо последовательно выполнять нижестоящие шаги. **Создайте** сценарий сети (byn.sh), который поможет вам с настройкой консорциума, созданием канала и установкой цепного кода.

> [!NOTE]
> При условии, что сценарий Build Your Network (byn) строго используется для сценариев демо/отклонения. Для установки производственного класса мы рекомендуем использовать родные AAP HLF.

Все команды для выполнения сценария Byn могут быть выполнены через интерфейс командования Azure Bash (CLI). Вы можете войти в веб-версию оболочки Azure через ![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) вариант в правом верхнем углу портала Azure. На запросе команды, введите Bash и введите, чтобы переключиться на Bash CLI.

Дополнительную информацию можно узнать из [оболочки Azure.](https://docs.microsoft.com/azure/cloud-shell/overview)

![Hyperledger Ткань на Лазурный Kubernetes сервис шаблон](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Скачать byn.sh и ткани-admin.yaml файл.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Установите ниже переменных среды на оболочке Azure CLI Bash:**

Установка информации о канале и информации об организации заказа

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Установка информации об организации сверстников

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Создайте один общий доступ к файлам Azure для обмена различными общедоступными сертификатами между организациями-одноранговыми и заказниками.

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
**Команды по управлению каналами**

Перейти к организации заказа AKS кластера и выдать команду для создания нового канала

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Команды по управлению консорциумом**

Выполняйте ниже команд в данном порядке, чтобы добавить одноранговую организацию в канал и консорциум.

1. Перейдите в кластер peer Organization AKS и загрузите ее службу обслуживания членов (MSP) в хранилище файлов Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Перейти к группе организации заказов AKS и добавить одноранговую организацию в канал и консорциум.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Вернитесь к организации одноранговой системы и выдали команду, чтобы соединить одноранговые узлы в канале.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Аналогичным образом, чтобы добавить больше одноранговых организаций в канал, обновите переменные среды одноранговых аКС в соответствии с требуемой организацией одноранговых узлов и выполните шаги 1 к 3.

**Команды управления цепным кодом**

Выполните ниже команды для выполнения цепного кода, связанных с операцией. Эти команды выполняют все операции на демо-цепочке. Этот демо-цепной код имеет две переменные "a" и "b". При мгновенном воспроизведении цепного кода "a" инициализируется с 1000, а "b" инициализирован с 2000. При каждом вызове цепного кода 10 единиц переносятся с "а" на "б". Операция запроса на цепном коде показывает мировое состояние переменной "a".

Выполняйте следующие команды, выполняемые в кластере одноранговой организации AKS.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Команды операции Цепного кода**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Запуск нативных операций HLF

Чтобы помочь клиентам начать выполнение нативных команд Hyperledger в сети HLF на AKS. Образец приложения предоставляется, что использует ткань NodeJS SDK для выполнения операций HLF. Команды предоставляются для создания нового удостоверения пользователя и установки собственного цепного кода.

### <a name="before-you-begin"></a>Перед началом

Следуйте ниже команды для первоначальной настройки приложения:

- Скачать файлы приложений
- Создание профиля соединения и профиля администратора
- Идентификация пользователя импортного админ

После завершения первоначальной настройки можно использовать SDK для выполнения нижеприведенных операций:

- Поколение идентификации пользователя
- Операции цепного кода

Вышеупомянутые команды могут выполняться из Облачной оболочки Azure.

### <a name="download-application-files"></a>Скачать файлы приложений

Первая настройка для запуска приложения заключается в загрузке всех файлов приложения в папку.

**Создайте папку приложения и введите папку:**

```bash
mkdir app
cd app
```
Выполните ниже команды, чтобы загрузить все необходимые файлы и пакеты:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Эта команда требует времени, чтобы загрузить все пакеты. После успешного выполнения команды можно `node_modules` увидеть папку в текущем каталоге. Все необходимые пакеты загружаются в `node_modules` папку.

### <a name="generate-connection-profile-and-admin-profile"></a>Создание профиля соединения и профиля администратора

Создание `profile` каталога `app` внутри папки

```bash
cd app
mkdir ./profile
```
Установите эти переменные среды в облачную оболочку Azure

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Выполнение ниже команды для создания профиля соединения и профиля администратора организации

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Он создаст профиль подключения и `profile` администратора организации в папке профиля с именем `<orgname>-ccp.json` и `<orgname>-admin.json` соответственно.

Аналогичным образом, создайте профиль соединения и профиль администратора для каждого упорядоченного и одноранговой организации.


### <a name="import-admin-user-identity"></a>Идентификация пользователя импортного админ

Последним шагом является импорт пользовательского удостоверения пользователя организации в бумажнике.

```bash
npm run importAdmin -- -o <orgName>

```
Вышеупомянутая команда выполняет импортAdmin.js для импорта идентификационных данных пользователя admin в кошелек. Скрипт считывает идентификацию администратора из `<orgname>-admin.json` профиля администратора и импортирует его в кошельке для выполнения операций HLF.

Скрипты используют кошелек файловой системы для хранения идентификационных данных. Он создает кошелек в рамках пути, указанного в поле ".кошелек" в профиле соединения. По умолчанию поле ".кошелек" `<orgname>`инициализировано `<orgname>` с помощью, что означает, что в текущем каталоге создается папка с именем для хранения идентификационных данных. Если вы хотите создать кошелек на каком-то другом пути, измените поле ".wallet" в профиле соединения перед запуском регистрации пользователя администратора и любых других операций HLF.

Аналогичным образом, импортируйте идентификацию пользователя админа для каждой организации.

Обратитесь за помощью к команде для получения более подробной информации о аргументах, передаваемых в команде.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Поколение идентификации пользователя

Выполняйте ниже команд в данном порядке для создания новых идентификаторов пользователей для организации HLF.

> [!NOTE]
> Прежде чем начать с шагов генерации идентификации пользователя, убедитесь, что начальная настройка приложения выполнена.

Установка ниже переменных среды на лазурной облачной оболочке

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Регистрация и регистрация нового пользователя

Чтобы зарегистрировать и зарегистрировать нового пользователя, выполните ниже приведенную команду, которая выполняет registerUser.js. Это сохраняет генерируемую личность пользователя в кошельке.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Идентификация пользователя Admin используется для выдачи команды регистра для нового пользователя. Таким образом, до выполнения этой команды необходимо иметь в кошельке иждивению пользователя admin. В противном случае эта команда выйдет из строя.

Обратитесь за помощью к команде для получения более подробной информации о аргументах, передаваемых в команде

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Операции цепного кода


> [!NOTE]
> Прежде чем приначинать операцию цепного кода, убедитесь, что первоначальная настройка приложения выполнена.

Установите под цепочкой конкретные переменные среды в оболочке Azure Cloud:

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

Операции ниже цепного кода могут быть выполнены:

- Установка цепного кода
- Мгновенный цепной код
- Вызывать цепной код
- Цепной код запроса

### <a name="install-chaincode"></a>Установка цепного кода

Выполните ниже команды для установки цепного кода на организации одноранговой.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Он установит цепной код на всех одноранговых `ORGNAME` узлах организации, установленных в переменной среды. Если в вашем канале есть две или более одноранговых организаций и вы хотите установить сетевой код на всех из них, выполняйте команды отдельно для каждой организации.

Следуйте за шагами:

- `ORGNAME` Установите `<peerOrg1Name>` и `installCC` выдали команду.
- `ORGNAME` Установите `<peerOrg2Name>` и `installCC` выдали команду.

  Выполните его для каждой организации одноранговой.

Обратитесь в командную справку для получения более подробной информации о аргументах, передаваемых в команде.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Мгновенный цепной код

Выполните ниже команды для мгновенного цепного кода на одноранговой.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Передайте имя функции мгновенного и запятую разделенный список аргументов в `<instantiateFunc>` и `<instantiateFuncArgs>` соответственно. Например, в [цепочке fabrcar,](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)чтобы мгновенно `<instantiateFunc>` установить `"Init"` `<instantiateFuncArgs>` цепной `""`код и к пустой строке.

> [!NOTE]
> Выполните команду на этот раз от какой-либо одной организации одноранговой в канале.
> После успешной отправки транзакции в orderer, заетриватель распространяет эту транзакцию среди всех одноранговых организаций канала. Таким образом, цепной код мгновенно используется на всех узлов хунанах на всех одноранговых организациях канала.

Обратитесь за помощью к команде для получения более подробной информации о аргументах, передаваемых в команде

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Вызывать цепной код

Выполните ниже команды, чтобы вызвать функцию цепного кода:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Pass invoke имя функции и запятая разделенный список аргументов в `<invokeFunction>` и `<invokeFuncArgs>` соответственно. Продолжая с fabcar цепной код, например, чтобы `<invokeFunction>` вызвать `<invokeFuncArgs>` `""`initLedger функция установлена `"initLedger"` и .

> [!NOTE]
> Выполните команду на этот раз от какой-либо одной организации одноранговой в канале.
> После успешной отправки транзакции в orderer, заетриватель распространяет эту транзакцию среди всех одноранговых организаций канала. Таким образом, мировое состояние обновляется на всех одноранговых узлах всех одноранговых организаций в канале.

Обратитесь за помощью к команде для получения более подробной информации о аргументах, передаваемых в команде

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Цепной код запроса

Выполните ниже команды для запроса цепного кода:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Передайте имя функции запроса и запятую разделенный список аргументов в `<queryFunction>` и `<queryFuncArgs>` соответственно. Опять `fabcar` же, принимая цепной код в качестве ссылки, `<queryArgs>` `""`чтобы задать запрос все автомобили в мире состояние установить `<queryFunction>` `"queryAllCars"` и .

Обратитесь за помощью к команде для получения более подробной информации о аргументах, передаваемых в команде

```bash
npm run queryCC -- -h

```
