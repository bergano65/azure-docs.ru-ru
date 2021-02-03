---
title: Развертывание приложения Java с помощью Open Liberty или WebSphere Liberty в кластере службы Kubernetes Azure (AKS)
description: Разверните приложение Java с помощью Open Liberty или WebSphere Liberty в кластере Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, жакартаи, Java, Микропрофиль, Open-Liberty, WebSphere-Liberty, AKS, kubernetes
ms.openlocfilehash: 2e025c706512b6ab3945118da996b11a5a8a9585
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526896"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Развертывание приложения Java с помощью Open Liberty или WebSphere Liberty в кластере службы Kubernetes Azure (AKS)

В этом руководстве показано, как запустить приложение Java, Java EE, [Джакарта ee](https://jakarta.ee/)или [Микропрофиль](https://microprofile.io/) в открытой среде выполнения Liberty или WebSphere Liberty, а затем развернуть контейнерное приложение в кластере AKS с помощью оператора Open Liberty. Оператор Open Liberty упрощает развертывание и Управление приложениями, работающими на открытых кластерах Liberty Kubernetes. Кроме того, можно выполнять более сложные операции, такие как сбор трассировок и дампов с помощью оператора. В этой статье описывается подготовка приложения Liberty, создание образа DOCKER приложения и запуск контейнерного приложения в кластере AKS.  Дополнительные сведения об Open Liberty см. [на странице Открытие проекта Liberty](https://openliberty.io/). Дополнительные сведения о IBM WebSphere Liberty см. [на странице продукта WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Для работы с этой статьей требуется последняя версия Azure CLI. Если вы используете Azure Cloud Shell, последняя версия уже установлена.
* При запуске команд в этом руководство локально (вместо Azure Cloud Shell):
  * Подготовка локального компьютера с установленной операционной системой, похожей на UNIX (например, Ubuntu, macOS).
  * Установите реализацию Java SE (например, [Адоптопенждк OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Установите [Maven](https://maven.apache.org/download.cgi) 3.5.0 или более поздней версии.
  * Установите [DOCKER](https://docs.docker.com/get-docker/) для вашей ОС.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Создайте группу ресурсов *Java-Liberty-Project* с помощью команды [AZ Group Create](/cli/azure/group#az_group_create) в расположении *eastus* . Он будет использоваться позже для создания экземпляра реестра контейнеров Azure (записи контроля доступа) и кластера AKS. 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>Создание экземпляра ACR

Используйте команду [AZ контроля](/cli/azure/acr#az_acr_create) доступа для создания экземпляра контроля доступа. В следующем примере создается экземпляр контроля учетных записей с именем *йоуруникуеакрнаме*. Убедитесь, что *йоуруникуеакрнаме* является уникальным в Azure.

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

Через некоторое время вы увидите выходные данные JSON, которые содержат:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Подключение к экземпляру записи контроля доступа

Чтобы отправить образ в экземпляр записи контроля доступа, сначала необходимо войти в него. Выполните следующие команды, чтобы проверить подключение:

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`Если вы успешно выполнили вход в экземпляр контроля доступа, вы должны увидеть конец выходных данных команды.

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Используйте команду [az aks create](/cli/azure/aks#az_aks_create), чтобы создать кластер AKS. В следующем примере создается кластер *myAKSCluster* с одним узлом. Это может занять несколько минут.

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Через несколько минут команда завершит работу и возвратит сведения о кластере в формате JSON, включая следующие:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Подключение к кластеру AKS

Управлять кластером Kubernetes можно c помощью [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> Приведенная выше команда использует расположение по умолчанию [файла конфигурации Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) — `~/.kube/config`. Вы можете указать другое расположение файла конфигурации Kubernetes с помощью параметра *--file*.

Чтобы проверить подключение к кластеру, используйте команду [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) для получения списка узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере показан единый узел, созданный на предыдущих шагах. Убедитесь, что узел находится в состоянии *Ready* (Готово):

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Установить Open Liberty operator

После создания кластера и подключения к нему установите [оператор Open Liberty](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) , выполнив следующие команды.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Сборка образа приложения

Чтобы развернуть и запустить приложение Liberty в кластере AKS, контейнеризовать приложение как образ DOCKER с помощью [открытых Liberty контейнеров](https://github.com/OpenLiberty/ci.docker) или образов [контейнеров WebSphere Liberty](https://github.com/WASdev/ci.docker).

1. Клонировать пример кода для этого руководством. Пример находится на [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Измените каталог на `javaee-app-simple-cluster` локальный клон.
1. Выполните команду `mvn clean package` , чтобы упаковать приложение.
1. Выполните одну из следующих команд, чтобы создать образ приложения и отправить его в экземпляр записи контроля доступа.
   * Выполните сборку с открытым базовым образом Liberty, если вы предпочитаете использовать Open Liberty в качестве облегченного™ среды выполнения Java с открытым кодом:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Выполните сборку с помощью базового образа WebSphere Liberty, если вы предпочитаете использовать коммерческую версию Open Liberty:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Развертывание приложения в кластере AKS

Выполните действия ниже, чтобы развернуть приложение Liberty в кластере AKS.

1. Создайте секрет для получения по запросу, чтобы кластер AKS прошел проверку подлинности в образе извлечения из экземпляра записи контроля доступа.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Убедитесь, что текущий рабочий каталог относится `javaee-app-simple-cluster` к локальному клону.
1. Выполните следующие команды, чтобы развернуть приложение Liberty с 3 репликами в кластере AKS. Выходные данные команды также отображаются как встроенные.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Подождите, пока не появится `3/3` `READY` столбец и `3` под `AVAILABLE` столбцом, используйте, `CTRL-C` чтобы прерывать `kubectl` процесс просмотра.

### <a name="test-the-application"></a>Тестирование приложения

При запуске приложения служба балансировщика нагрузки Kubernetes предоставляет внешний интерфейс приложения в Интернете. Для завершения этого процесса может потребоваться некоторое время.

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) с аргументом `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

Дождитесь, пока *внешний IP* -адрес изменится с *ожидания* на фактический общедоступный IP-адрес, используйте `CTRL-C` для отмены `kubectl` процесса просмотра.

Откройте веб-браузер с внешним IP-адресом и портом службы ( `52.152.189.57:9080` для приведенного выше примера), чтобы просмотреть домашнюю страницу приложения. Имя Pod реплик приложения должно отобразиться в левом верхнем углу страницы. Подождите несколько минут и обновите страницу. Вы, вероятно, увидите другое имя Pod, отображаемое в результате балансировки нагрузки, предоставляемой кластером AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Приложение Java Liberty успешно развернуто в AKS":::

>[!NOTE]
> - В настоящее время приложение не использует протокол HTTPS. Мы рекомендуем использовать [параметр ENABLE TLS и собственные сертификаты](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>очищать ресурсы.

Чтобы избежать расходов за использование Azure, необходимо удалить ненужные ресурсы.  Если кластер больше не нужен, используйте команду [AZ Group Delete](/cli/azure/group#az_group_delete) , чтобы удалить группу ресурсов, службу контейнеров, реестр контейнеров и все связанные ресурсы.

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения можно узнать из ссылок, используемых в этом руководство:

* [Служба Azure Kubernetes](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Открыть Liberty](https://openliberty.io/)
* [Open Liberty, оператор](https://github.com/OpenLiberty/open-liberty-operator)
* [Открыть конфигурацию сервера Liberty](https://openliberty.io/docs/ref/config/)
* [Подключаемый модуль Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Открытие образов контейнеров Liberty](https://github.com/OpenLiberty/ci.docker)
* [Образы контейнеров WebSphere Liberty](https://github.com/WASdev/ci.docker)
