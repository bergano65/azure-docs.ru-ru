---
title: Интеграция имеющихся приложений с MongoDB с помощью API для MongoDB в Azure Cosmos DB и открытого компонента Service Broker для Azure (OSBA)
description: Из этой статьи вы узнаете, как интегрировать имеющееся приложение Java и MongoDB с помощью API для MongoDB в Azure Cosmos DB и открытого компонента Service Broker для Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, открытый компонент Service Broker, открытый компонент Service Broker для Azure
ms.openlocfilehash: 3146d11f33809391d93305d63bad757ed281fb70
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977021"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Интеграция имеющихся приложений с MongoDB с помощью API для MongoDB в Azure Cosmos DB и открытого компонента Service Broker для Azure (OSBA)

Azure Cosmos DB — это многомодельная глобально распределенная служба баз данных. Она также обеспечивает совместимость проводного протокола с несколькими API-интерфейсами NoSQL, в том числе для MongoDB. API-интерфейс Cosmos DB для MongoDB позволяет использовать Cosmos DB с имеющимся приложением MongoDB без необходимости изменения драйверов в целях реализации базы данных приложения. Кроме того, можно предоставить службу Cosmos DB с помощью открытого компонента Service Broker для Azure.

В этой статье вам потребуется имеющееся приложение Java, которое использует базу данных MongoDB. Вы обновите его для использования базы данных Cosmos DB с помощью открытого компонента Service Broker для Azure.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, необходимо выполнить следующее:
    
* [Создать кластер Службы Azure Kubernetes (AKS)](kubernetes-walkthrough.md).
* [Установить и настроить открытый компонент Service Broker для Azure](integrate-azure.md) в вашем кластере AKS. 
* Установить [интерфейс командной строки каталога услуг](https://svc-cat.io/docs/install/) и настроить его для запуска команд `svcat`.
* У вас должна быть база данных [MongoDB](https://www.mongodb.com/). Например, вы можете запустить MongoDB на своем компьютере [для разработки](https://docs.mongodb.com/manual/administration/install-community/) или на [виртуальной машине Azure](../virtual-machines/linux/install-mongodb.md).
* Иметь возможность подключения базы данных MongoDB, такой как [оболочка Mongo](https://docs.mongodb.com/manual/mongo/), а также отправки запросов к ней.

## <a name="get-application-code"></a>Получение кода приложения
    
В этой статье мы воспользуемся [примером приложения Spring Music из Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music), чтобы продемонстрировать приложение, использующее базу данных MongoDB.
    
Клонируйте приложение с GitHub и перейдите в его каталог:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Подготовьте приложение для использования вашей базы данных MongoDB.

Пример приложения Spring Music предоставляет множество возможностей для источников данных. В этой статье вы настроите его для использования имеющейся базы данных MongoDB. 

Добавьте следующий YAML в конец *src/main/resources/application.yml*. Это дополнение создает профиль с именем *mongodb* и настраивает URI и имя базы данных. Замените URI на сведения о подключении к имеющейся базе данных MongoDB. Добавление URI, содержащего имя пользователя и пароль, непосредственно к этому файлу предназначено только для **разработки**, и его **никогда не следует добавлять в систему управления версиями**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Когда вы запускаете приложение и указываете ему использовать профиль *mongodb*, оно подключается к вашей базе данных MongoDB и использует ее для хранения данных приложения.

Чтобы создать приложение:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Запустите приложение и укажите ему использовать профиль *mongodb*.

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

В браузере перейдите по адресу http://localhost:8080.

![Приложение Spring Music с данными по умолчанию](media/music-app.png)

Обратите внимание, что приложение заполнено некоторыми [данными по умолчанию](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Взаимодействуйте с ними, удалив несколько имеющихся альбомов и создав несколько новых.

Вы можете убедиться, что приложение использует базу данных MongoDB, подключившись к ней и запросив базу данных *musicdb*.

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

В предыдущем примере используется [оболочка Mongo](https://docs.mongodb.com/manual/mongo/) для подключения к базе данных MongoDB и обращения к ней. Вы также можете убедиться, что изменения сохраняются, остановив приложение, перезапустив его и снова открыв его в браузере. Обратите внимание, что внесенные изменения не исчезнут.


## <a name="create-a-cosmos-db-database"></a>Создание базы данных Cosmos DB

Чтобы создать базу данных Cosmos DB в Azure с помощью открытого компонента Service Broker, выполните команду `svcat provision`.

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Приведенная ниже команда подготавливает базу данных Cosmos DB в Azure в группе ресурсов *MyResourceGroup* в регионе *eastus*. Дополнительные сведения о *resourceGroup*, *location* и других связанных с Azure параметрах JSON см. в справочной документации по модулю [Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Чтобы убедиться, что подготовка базы данных завершена, выполните команду `svcat get instance`.

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Ваша база данных будет готова, когда вы увидите *Готово* в разделе *Состояние*.

После завершения подготовки базы данных необходимо привязать ее метаданные к секрету [Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Другие приложения могут получить доступ к этим данным после их привязки к секрету. Чтобы привязать метаданные базы данных к секрету, используйте команду `svcat bind`:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Использование базы данных Cosmos DB с приложением

Для использования базы данных Cosmos DB с приложением необходимо знать идентификатор URI для подключения к ней. Чтобы получить эти сведения, используйте команду `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Предыдущая команда получает секрет *musicdb* и отображает только URI. Секреты хранятся в формате base64, поэтому предыдущая команда также декодирует его.

Используя URI базы данных Cosmos DB, измените *src/main/resources/application.yml* для его использования:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Обновление URI, содержащего имя пользователя и пароль, непосредственно к этому файлу предназначено только для **разработки**, и его **никогда не следует добавлять в систему управления версиями**.

Перестройте и запустите приложение, чтобы начать использовать базу данных Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Обратите внимание, что приложение по-прежнему использует профиль *mongodb* и URI, который начинается с *mongodb://*, для подключения к базе данных Cosmos DB. [API Azure Cosmos DB для MongoDB](../cosmos-db/mongodb-introduction.md) предоставляет такую совместимость. Это позволяет приложению продолжать работать так, как если бы оно использовало базу данных MongoDB, хотя фактически используется Cosmos DB.

В браузере перейдите по адресу http://localhost:8080. Обратите внимание, что данные по умолчанию были восстановлены. Взаимодействуйте с ними, удалив несколько имеющихся альбомов и создав несколько новых. Вы можете убедиться, что изменения сохраняются, остановив приложение, перезапустив его и вернувшись к нему в браузере. Обратите внимание, что внесенные изменения не исчезнут. Изменения сохраняются в Cosmos DB, созданной с помощью открытого компонента Service Broker для Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Запуск приложения в кластере AKS

Чтобы развернуть приложение в кластере AKS, можно использовать [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md). Azure Dev Spaces помогают создавать артефакты, такие как файлы Dockerfile и диаграммы Helm, а также развертывать и запускать приложения в AKS.

Чтобы включить Azure Dev Spaces в кластере AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Используйте инструментарий Azure Dev Spaces, чтобы подготовить приложение к запуску в AKS:

```cmd
azds prep --public
```

Эта команда создает несколько артефактов, включая папку *charts/*, которая является диаграммой Helm, в корневом каталоге проекта. Эта команда не может создать файл *Dockerfile* для данного проекта, поэтому его необходимо создать самостоятельно.

Создайте файл в корневом каталоге проекта с именем *Dockerfile* со следующим содержимым:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Кроме того, для свойства *configurations.develop.build* в файле *azds.yaml* следует задать значение *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Кроме того, для атрибута *containerPort* следует задать значение *8080* в *charts/spring-music/templates/deployment.yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Чтобы развернуть приложение в AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Перейдите по URL-адресу, отображаемому в журналах. В предыдущем примере используйте *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Убедитесь, что приложение отображается вместе с изменениями.

## <a name="next-steps"></a>Дополнительная информация

В этой статье описывается обновление имеющегося приложения с переходом с MongoDB на API Cosmos DB для MongoDB. В этой статье также описаны способы подготовки службы Cosmos DB с помощью открытого компонента Service Broker для Azure и развертывания этого приложения в AKS с использованием Azure Dev Spaces.

Дополнительные сведения о Cosmos DB, открытом компоненте Service Broker для Azure и Azure Dev Spaces см. в статье:
* [База данных Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh) (Открытый компонент Service Broker для Azure)
* [Рабочие среды Azure для разработчиков](../dev-spaces/azure-dev-spaces.md)
