---
title: Разработка в Azure Kubernetes Service (AKS) с помощью Helm
description: Используйте Helm с AKS и реестром контейнеров Azure для упаковки и запуска контейнеров приложений в кластере.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169574"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Краткое руководство. Разработка в службе Azure Kubernetes Service (AKS) с помощью Helm

[Helm][helm] — это средство упаковки с открытым кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом. Аналогично диспетчерам пакетов Linux, таких как *APT* и *Yum*, Helm используется для управления чартами Kubernetes, представляющими собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как использовать Helm для упаковки и запуска приложения в AKS. Дополнительные сведения об установке существующего приложения с помощью Helm см. [в статье Установка существующих приложений с использованием Helm в AKS][helm-existing].

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* DOCKER установлен и настроен. Docker предоставляет пакеты, которые позволяют настроить Docker в системе [Mac][docker-for-mac], [Windows][docker-for-windows] или [Linux][docker-for-linux].
* [Установлен Helm v3][helm-install].

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Чтобы использовать Helm для запуска приложения в кластере AKS, вам понадобится реестр контейнеров Azure для хранения образов контейнеров. В приведенном ниже примере с помощью команды [AZ запись контроля][az-acr-create] доступа создается для создания записи контроля доступа с именем *михелмакр* в группе ресурсов *MyResourceGroup* с номером SKU *Basic* . Необходимо указать собственное уникальное имя реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Результат будет похож на следующий пример. Запишите значение *loginServer* для записи контроля доступа, так как оно будет использоваться на более позднем этапе. В приведенном ниже примере *myhelmacr.azurecr.IO* является *loginServer* для *михелмакр*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Чтобы использовать экземпляр записи контроля доступа, необходимо сначала войти в систему. Для входа используйте команду [AZ запись контроля][az-acr-login] доступа. В приведенном ниже примере выполняется вход в запись контроля доступа с именем *михелмакр*.

```azurecli
az acr login --name MyHelmACR
```

Команда возвращает сообщение *Login "успешно выполнено* " после завершения.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Создайте кластер AKS. Приведенная ниже команда создает кластер AKS с именем Мякс и присоединяет Михелмакр.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Кластеру AKS требуется доступ к записи контроля доступа для извлечения образов контейнеров и их запуска. Приведенная выше команда также предоставляет кластеру *мякс* доступ к записи контроля доступа *михелмакр* .

## <a name="connect-to-your-aks-cluster"></a>Подключение к кластеру AKS

Для подключения к кластеру Kubernetes с локального компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Его также можно установить локально с помощью команды [az aks install-cli][]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. Следующий пример получает учетные данные для кластера AKS с именем *мякс* в *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Загрузка примера приложения

В этом кратком руководстве используется [пример приложения Node. js из Azure dev Spaces примера репозитория][example-nodejs]. Клонирование приложения из GitHub и переход к `dev-spaces/samples/nodejs/getting-started/webfrontend` каталогу.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Создание Dockerfile

Создайте новый файл *Dockerfile* , используя следующую команду:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Создание и отправка примера приложения в запись контроля доступа

Получите адрес сервера входа с помощью команды [AZ login List][az-acr-list] и запросите *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте DOCKER для создания, пометки и отправки примера контейнера приложения в запись контроля доступа:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Создание диаграммы Helm

Создайте диаграмму Helm с помощью `helm create` команды.

```console
helm create webfrontend
```

Выполните следующие обновления для *интерфейсов YAML*:

* Измените `image.repository` на `<acrLoginServer>/webfrontend`.
* Измените `service.type` на `LoadBalancer`.

Пример:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

`appVersion` Обновите `v1` в в *интерфейсе YAML*. Например:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Запуск диаграммы Helm

Используйте `helm install` команду, чтобы установить приложение с помощью диаграммы Helm.

```console
helm install webfrontend webfrontend/
```

Возвращение общедоступного IP-адреса службы займет несколько минут. Чтобы отследить ход выполнения, используйте команду `kubectl get service` с параметром *watch*:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Перейдите к подсистеме балансировки нагрузки приложения в браузере, `<EXTERNAL-IP>` используя для просмотра примера приложения.

## <a name="delete-the-cluster"></a>Удаление кластера

Если кластер больше не нужен, используйте команду [AZ Group Delete][az-group-delete] , чтобы удалить группу ресурсов, кластер AKS, реестр контейнеров, хранящиеся в нем образы контейнеров и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе с [дополнительными замечаниями][sp-delete]. Управляемые удостоверения администрируются платформой, и их не нужно удалять.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Helm см. в документации по Helm.

> [!div class="nextstepaction"]
> [Документация по Helm][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
