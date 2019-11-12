---
title: Запуск задания Apache Spark с помощью Службы Azure Kubernetes (AKS)
description: Использование Службы Azure Kubernetes (AKS) для выполнения задания Apache Spark
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 5ecfa1853479c1cdc705a1a465a1de6318917a72
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929000"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Запуск заданий Apache Spark в AKS

[Apache Spark][apache-spark] — это быстрый механизм для обработки больших данных. Начиная с [выпуска Spark 2.3.0][spark-latest-release], Apache Spark поддерживает собственную интеграцию с кластерами Kubernetes. Служба Azure Kubernetes (AKS) — это управляемая среда Kubernetes, выполняющаяся в Azure. В этом документе описывается подготовка и запуск заданий Apache Spark в кластере Службы Azure Kubernetes (AKS).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Основные сведения о Kubernetes и [Apache Spark][spark-quickstart].
* Учетная запись [центра DOCKER][docker-hub] или [Реестр контейнеров Azure][acr-create].
* Azure CLI, [установленные][azure-cli] в системе разработки.
* [JDK 8][java-install] , установленный в системе.
* SBT ([средство сборки Scala][sbt-install]), установленное в системе.
* Средства командной строки Git, установленные в системе.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Spark используется для крупномасштабной обработки данных и требует, чтобы узлы Kubernetes соответствовали требованиям к ресурсам Spark. Минимальный рекомендуемый размер для узлов службы Azure Kubernetes (AKS) — `Standard_D3_v2`.

Если вам нужен кластер AKS, соответствующий этой минимальной рекомендации, выполните следующие команды.

Создайте группу ресурсов для кластера.

```azurecli
az group create --name mySparkCluster --location eastus
```

Создайте субъект-службу для кластера. После создания для следующей команды потребуется идентификатор и пароль субъекта-службы.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Создайте кластер AKS с узлами, имеющими размер `Standard_D3_v2`, и значения appId и Password, передаваемые в качестве параметров субъекта-службы и секрета клиента.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Подключитесь к кластеру AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Если вы используете Реестр контейнеров Azure (ACR) для хранения образов контейнеров, настройте аутентификацию между AKS и ACR. Инструкции см. в [документации по проверке подлинности записей контроля][acr-aks] доступа.

## <a name="build-the-spark-source"></a>Создание источника Spark

Перед запуском заданий Spark в кластере AKS вам необходимо создать исходный код Spark и упаковать его в образ контейнера. Источник Spark содержит скрипты, которые можно использовать для завершения этого процесса.

Клонируйте репозиторий проектов Spark в свою систему разработки.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Перейдите в каталог клонированного репозитория и сохраните путь к источнику Spark в переменной.

```bash
cd spark
sparkdir=$(pwd)
```

Если у вас установлено несколько версий JDK, задайте для `JAVA_HOME` использование версии 8 в текущем сеансе.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Выполните следующую команду для создания исходного кода Spark с поддержкой Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Следующая команда создает образ контейнеров Spark и помещает их в реестр образов контейнеров. Замените `registry.example.com` именем реестра контейнеров и замените `v1` тегом, который вы хотите использовать. При использовании центра Docker нужно использовать имя реестра, а при использовании ACR — имя сервера входа ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Передайте образ контейнера в реестр образов контейнеров.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Подготовка задания Spark

Далее подготовьте задание Spark. JAR-файл используется для хранения задания Spark и необходим при выполнении команды `spark-submit`. Его можно сделать доступным через общедоступный URL-адрес или предварительно упаковать в образ контейнера. В этом примере создается пример JAR-файла для вычисления значения Pi. Затем этот JAR-файл загружается в хранилище Azure. Если у вас есть существующий файл, вы можете использовать его.

Создайте каталог, в который вы хотели бы поместить проект для задания Spark.

```bash
mkdir myprojects
cd myprojects
```

Создайте новый проект Scala из шаблона.

```bash
sbt new sbt/scala-seed.g8
```

При появлении запроса введите `SparkPi` для имени проекта.

```bash
name [Scala Seed Project]: SparkPi
```

Перейдите в каталог созданного проекта.

```bash
cd sparkpi
```

Выполните следующие команды, чтобы добавить подключаемый модуль SBT, который позволяет упаковать проект в виде JAR-файла.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Выполните эти команды, чтобы скопировать пример кода в только что созданный проект и добавить все необходимые зависимости.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Чтобы упаковать проект в JAR-файл, выполните следующую команду.

```bash
sbt assembly
```

После выполнения упаковки должен появиться результат, аналогичный приведенному ниже:

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Копирование задания в хранилище

Создайте учетную запись Azure и контейнер для хранения JAR-файла.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Передайте JAR-файл в учетную запись Azure с помощью следующей команды.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Переменная `jarUrl` теперь содержит общедоступный путь к JAR-файлу.

## <a name="submit-a-spark-job"></a>Отправка задания Spark

Запустите прокси-сервер kube в отдельной командной строке с помощью следующего кода.

```bash
kubectl proxy
```

Вернитесь в корень репозитория Spark.

```bash
cd $sparkdir
```

Создайте учетную запись службы, имеющую достаточные разрешения для запуска задания.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Отправьте задание с помощью `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Эта операция запускает задание Spark, которое передает состояние задания в сеанс оболочки. Пока выполняется задание, вы можете получить сведения о модуле драйвера Spark и модулях исполнителя, используя команду kubectl get pods. Для выполнения этих команд откройте второй сеанс терминала.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Пока задание выполняется, вы также можете получить доступ к интерфейсу Spark. Во втором сеансе терминала используйте команду `kubectl port-forward`, чтобы обеспечить доступ к интерфейсу Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Чтобы получить доступ к интерфейсу Spark, откройте адрес `127.0.0.1:4040` в браузере.

![Пользовательский интерфейс Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Получение результатов задания и журналов

Когда задание завершится, модуль драйвера будет находиться в состоянии "Completed" (Завершено). Получите имя модуля с помощью следующей команды.

```bash
kubectl get pods --show-all
```

Выходные данные:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Используйте команду `kubectl logs`, чтобы получить журналы из модуля драйвера Spark. Замените имя модуля именем модуля драйвера.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Внутри этих журналов можно увидеть результат работы Spark, который является значением Pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Упаковка JAR-файла в образ контейнера

В приведенном выше примере JAR-файл Spark был загружен в хранилище Azure. Другой вариант — упаковать JAR-файл в пользовательские образы Docker.

Для этого найдите `dockerfile` для образа Spark в каталоге `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Добавьте оператор `ADD` для `jar` задания Spark где-то между объявлениями `WORKDIR` и `ENTRYPOINT`.

Обновите путь к JAR-файлу, указав расположение файла `SparkPi-assembly-0.1.0-SNAPSHOT.jar` в вашей системе разработки. Также можно использовать собственный настраиваемый JAR-файл.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Создайте и передайте образ вместе со скриптами Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

При запуске задания вместо указания удаленного URL-адреса JAR-файла можно использовать схему `local://` ​​с указанием пути к файлу в образе Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Из [документации по][spark-docs]Spark: "Планировщик Kubernetes в настоящее время экспериментальен. В будущих версиях возможны изменения в поведении конфигурации, образов контейнеров и точек входа".

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в документации по Spark.

> [!div class="nextstepaction"]
> [Документация по Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
