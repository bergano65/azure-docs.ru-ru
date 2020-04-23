---
title: Разработка на Сервисе Azure Kubernetes (AKS) с помощью Helm
description: Используйте шлем с AKS и реестром контейнеров Azure для упаковки и запуска контейнеров приложений в кластере.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873437"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Быстрый запуск: Разработка на Azure Kubernetes службы (AKS) с хелмом

[Helm][helm] — это средство упаковки с открытым кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом. Аналогично диспетчерам пакетов Linux, таких как *APT* и *Yum*, Helm используется для управления чартами Kubernetes, представляющими собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как использовать Helm для упаковки и запуска приложения на AKS. Для получения более подробной информации об установке существующего приложения с использованием Helm [см.][helm-existing]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки НаAz, можно создать [бесплатную учетную запись.](https://azure.microsoft.com/free)
* [Установленный Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Докер установлен и настроен. Docker предоставляет пакеты, которые позволяют настроить Docker в системе [Mac][docker-for-mac], [Windows][docker-for-windows] или [Linux][docker-for-linux].
* [Шлем v3 установлен][helm-install].

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure
Для использования Helm для запуска приложения в кластере AKS необходим реестр контейнеров Azure для хранения изображений контейнеров. Ниже приведен пример использует [az acr создать][az-acr-create] для создания ACR имени *MyHelmACR* в группе ресурсов *MyResourceGroup* с *Basic* SKU. Вы должны предоставить свое уникальное имя реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Результат будет похож на следующий пример. Обратите внимание на значение *входа в* сервер для вашего ACR, так как он будет использоваться на более позднем этапе. В приведенном ниже примере *myhelmacr.azurecr.io* является *логинСервер* для *MyHelmACR*.

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

Чтобы использовать экземпляр ACR, необходимо сначала войти в систему. Используйте команду [входа в систему az acr][az-acr-login] для входа. Ниже приведен пример будет войти в ACR имени *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Команда возвращает сообщение *Логин Успешно* после завершения.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Создание кластера Службы Azure Kubernetes

Создайте кластер AKS. Ниже приведенная команда создает кластер AKS под названием MyAKS и прикрепляет MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Кластеру AKS необходим доступ к ACR, чтобы вытащить изображения контейнеров и запустить их. Вышеупомянутая команда также предоставляет *кластеру MyAKS* доступ к вашему *MyHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Подключение к кластеру AKS

Для подключения к кластеру Kubernetes с локального компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Его также можно установить локально с помощью команды [az aks install-cli][]:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][]. Следующий пример получает учетные данные для кластера AKS под названием *MyAKS* в *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Загрузка примера приложения

Этот быстрый запуск использует [пример приложения Node.js из репозитория образца Azure Dev Spaces.][example-nodejs] Клонировать приложение от GitHub и `dev-spaces/samples/nodejs/getting-started/webfrontend` перейти к каталогу.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Создание Dockerfile

Создайте новый файл *Dockerfile,* используя следующее:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Сборка и нажатие образца приложения в ACR

Получите адрес сервера входа с помощью команды [списка az acr][az-acr-list] и запроса для *loginServer:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте Docker для сборки, тегов и нажатия контейнера для применения образца в ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Создайте диаграмму helm

Создайте диаграмму `helm create` helm с помощью команды.

```console
helm create webfrontend
```

Сделайте следующие обновления для *webfrontend/values.yaml:*

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

Обновление `appVersion` `v1` в *webfrontend/Chart.yaml*. Например.

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Запустите диаграмму руля

Используйте `helm create` команду для установки приложения с помощью диаграммы Helm.

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

Перейдите к балансу нагрузочно-разгрузчик приложения в браузере, `<EXTERNAL-IP>` используя для просмотра образца приложения.

## <a name="delete-the-cluster"></a>Удаление кластера

Когда кластер больше не нужен, используйте команду [удаления группы аз для][az-group-delete] удаления группы ресурсов, кластера AKS, реестра контейнеров, сохраненных там изображений контейнеров и всех связанных ресурсов.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе с [дополнительными замечаниями][sp-delete]. Управляемые удостоверения администрируются платформой, и их не нужно удалять.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации об использовании Helm, см документация Helm.

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
