---
title: Наверсможно настройку кластера Azure Dev Spaces, чтобы использовать шлем 3 (предварительный просмотр)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Узнайте, как настроить кластер Dev Spaces для использования Helm 3
keywords: Пространства Azure Dev, Dev Spaces, Докер, Кубернетес, Лазурный берег, AKS, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454301"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Наверсможно настройку кластера Azure Dev Spaces, чтобы использовать шлем 3 (предварительный просмотр)

Пространства Azure Dev по умолчанию используют Helm 2 для установки пользовательских служб в пробелах разработчиков в кластере AKS. Вы можете включить Azure Dev Spaces использовать Helm 3 вместо установки пользовательских служб в дев-пространствах. Независимо от версии Helm Azure Dev Spaces, используюейе езду для установки пользовательских служб, вы можете продолжать использовать клиента Helm 2 или 3 для управления собственными релизами в одном кластере.

При вводе Helm 3 azure Dev Spaces ведет себя по-разному при установке пользовательских служб в дев-пространствах следующим образом:

* Tiller больше не развертывается в кластере в пространстве имен *azds.*
* Шлем хранит информацию в пространстве имен, где установлена служба вместо пространства имен *azds.*
* Информация о выпуске Helm 3 остается в пространстве имен, где служба устанавливается после удаления контроллера.
* Вы можете напрямую взаимодействовать с любым выпуском, управляемым Azure Dev Spaces в вашем кластере, используя клиент Helm 3.

В этом руководстве вы узнаете, как включить Helm 3 для Azure Dev Spaces для установки пользовательских служб в пространствах разработчиков.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом

### <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Зарегистрируйте функцию предварительного просмотра Helm3Preview

Чтобы позволить Пространствам Azure Dev использовать Helm 3 для установки пользовательских служб в пространствах разработчиков, сначала включите флаг *функции Helm3Preview* в подписке с помощью команды *регистра функций az:*

> [!WARNING]
> Любой кластер AKS, на который вы включите Azure Dev Spaces с флагом *функции Helm3Preview,* будет использовать этот опыт предварительного просмотра. Чтобы продолжать включать полностью поддерживаемые пространства Azure Dev в кластерах AKS, не включайте функции предварительного просмотра производственных подписок. Используйте отдельный тест или подписку Azure на разработку для тестирования функций предварительного просмотра.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Регистрация занимает несколько минут. Проверьте статус регистрации с помощью команды *шоу-функции az:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Когда *государство* *зарегистрировано,* освежите регистрацию *Microsoft.DevSpaces* с помощью *реестра поставщиков az:*

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Ограничения

При предварительном просмотре применяются следующие ограничения:

* Эту функцию нельзя использовать в кластерах AKS с существующими рабочими нагрузками. Необходимо создать новый кластер AKS.

## <a name="create-your-cluster"></a>Создание кластера

Создайте новый кластер AKS в регионе с этой функцией предварительного просмотра. Ниже команды создают группу ресурсов под названием *MyResourceGroup* и новый кластер AKS под названием *MyAKS:*

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Включить пространства Azure Dev

Используйте команду *«Использование-dev-пространства»,* чтобы включить Dev Spaces в кластер akS и следовать запросам. Следующая команда включает Dev Spaces в кластере *MyAKS* в группе *MyResourceGroup* и создает пространство разработки по умолчанию.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Проверка Dev Пространства работает Руль 3

Проверка невыполнения руля путем перечисления развертываний в пространстве имен *azds:*

```cmd
kubectl get deployment -n azds
```

Подтверждение *развертывания не* работает в пространстве имен azds. Пример:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md