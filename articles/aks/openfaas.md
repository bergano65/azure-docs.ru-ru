---
title: Использование OpenFaaS со Службой Azure Kubernetes (AKS)
description: Развертывание и использование OpenFaaS со Службой Azure Kubernetes (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 5ed6e0b21b00ede3f78a102fd004e5706ae3cea5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60464908"
---
# <a name="using-openfaas-on-aks"></a>Использование OpenFaaS в AKS

[OpenFaaS] [ open-faas] — это платформа для создания бессерверных функций при помощи контейнеров. Как проект с открытым кодом она стала очень популярной в сообществе. В этом документе описано, как установить и использовать OpenFaas в кластере Службы Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Базовое представление о Kubernetes.
* Кластер Службы Azure Kubernetes (AKS) и настроенные учетные данные AKS в системе разработки.
* Установленный компонент Azure CLI в системе разработки.
* Средства командной строки Git, установленные в системе.

## <a name="add-the-openfaas-helm-chart-repo"></a>Добавьте репозиторий helm диаграммы OpenFaaS

OpenFaaS поддерживает собственный чарты helm, чтобы поддерживать актуальность со всеми последними изменениями.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Развертывание OpenFaaS

Рекомендуется, чтобы OpenFaaS и функции OpenFaaS хранились в собственном пространстве имен Kubernetes.

Создание пространства имен для системы OpenFaaS и функции:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Создать пароль для OpenFaaS пользовательского интерфейса портала и REST API:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Можно получить значение секрета с `echo $PASSWORD`.

Пароль, который мы создаем здесь будет использоваться в диаграмме helm, чтобы включить обычную проверку подлинности на шлюзе OpenFaaS, который предоставляется в Интернете через подсистему балансировки нагрузки облака.

Диаграмма Helm для OpenFaaS будет включена в клонированный репозиторий. С помощью этой диаграммы разверните OpenFaaS в кластере AKS.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Выходные данные:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Для получения доступа к шлюзу OpenFaaS создается общедоступный IP-адрес. Чтобы получить этот IP-адрес, используйте команду [kubectl get service][kubectl-get]. Процесс назначения службе IP-адреса может занять около минуты.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Выходные данные.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Чтобы протестировать систему OpenFaaS, перейдите к внешнему IP-адресу на порту 8080 (в этом примере — `http://52.186.64.52:8080`). Вам будет предложено войти. Чтобы получить пароль, введите `echo $PASSWORD`.

![Пользовательский интерфейс OpenFaaS](media/container-service-serverless/openfaas.png)

Установите интерфейс командной строки (CLI) OpenFaaS. В этом примере используется brew. Дополнительные варианты см. в [документации по OpenFaaS CLI][open-faas-cli].

```console
brew install faas-cli
```

Задайте `$OPENFAAS_URL` общедоступный IP-адрес, определенный выше.

Войдите с помощью Azure CLI:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Создание первой функции

Теперь, когда OpenFaaS можно использовать, создайте функцию с помощью портала OpenFaas.

Щелкните **Deploy New Function** (Развернуть новую функцию) и выполните поиск по слову **Figlet**. Выберите функцию Figlet и нажмите кнопку **Deploy** (Развернуть).

![Figlet](media/container-service-serverless/figlet.png)

Для вызова функции используйте curl. Замените IP-адрес в следующем примере IP-адресом шлюза OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Выходные данные:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Создание второй функции

Теперь создайте вторую функцию. Этот пример будет развернут с помощью OpenFaaS CLI. Он включает настраиваемый образ контейнера и предусматривает получение данных из Cosmos DB. Перед созданием функции необходимо настроить несколько элементов.

Сначала создайте группу ресурсов для Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Разверните экземпляр CosmosDB вида `MongoDB`. Этому экземпляру требуется уникальное имя. Обновите `openfaas-cosmos` уникальным значением для вашей среды.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Получите строку подключения к базе данных Cosmos и сохраните ее в переменной.

Обновите значение аргумента `--resource-group` именем вашей группы ресурсов, а значение аргумента `--name` — именем базы данных Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Теперь заполните базу данных Cosmos DB тестовыми данными. Создайте файл с именем `plans.json` и скопируйте в него следующий код JSON.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Используйте средство *mongoimport* для загрузки экземпляра CosmosDB с данными.

При необходимости установите средства MongoDB. В следующем примере эти средства устанавливаются с помощью brew. Другие варианты см. в [документации MongoDB][install-mongo].

```azurecli-interactive
brew install mongodb
```

Загрузите данные в базу данных.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Выходные данные:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Выполните следующую команду для создания функции. Обновите значение аргумента `-g` адресом своего шлюза OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

После развертывания можно будет увидеть только что созданную конечную точку OpenFaaS для функции.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Протестируйте функцию с помощью curl. Обновите IP-адрес адресом шлюза OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Выходные данные:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Также функцию можно протестировать в пределах пользовательского интерфейса OpenFaaS.

![замещающий текст](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Следующие шаги

Вы можно продолжить обучение с обсуждения OpenFaaS через набор практических лабораторных работ, посвященных таким темам, как создание собственных программ-роботов GitHub, использования секретов, просмотре метрик и автоматическое масштабирование.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
