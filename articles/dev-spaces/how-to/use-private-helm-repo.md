---
title: Как использовать частный репозиторий Helm в пространствах Azure Dev
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Используйте частное репозиторий helm в пространстве Azure Dev.
keywords: Докер, Кубернетес, Лазурный газ, AKS, Контейнерная служба Azure, контейнеры, шлем
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240467"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Используйте частное репозиторий helm в пространствах Azure Dev

[Хельм][helm] является менеджером пакетов для Kubernetes. Helm использует формат [диаграммы][helm-chart] для упаковки зависимостей. Диаграммы руля хранятся в репозитории, которое может быть публичным или закрытым. Пространства Azure Dev только извлекают диаграммы руля из общедоступных репозиторий при запуске приложения. В тех случаях, когда репозиторий Helm является частным или пространства Azure Dev не могут получить к нему доступ, можно добавить диаграмму из этого репозитория непосредственно в приложение. Добавление диаграммы непосредственно позволяет Пространствам Azure Dev запускать приложение без доступа к частному репозиторию helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Добавьте частное репозиторий Helm в локальную машину

Используйте обновление [репо руля][helm-repo-add] и [репо руля][helm-repo-update] для доступа к частному репозиторию Helm от локальной машины.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Добавьте диаграмму в приложение

Перейдите к каталогу проекта `azds prep`и запустите.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Команда `prep` пытается создать [Dockerfile и диаграмму Helm](../how-dev-spaces-works-prep.md#prepare-your-code) для проекта. Azure Dev Spaces использует эти файлы для сборки и выполнения кода. Но вы можете изменить эти файлы, если нужно определить другой способ сборки и запуска проекта.

Создайте файл [requirements.yaml][helm-requirements] с диаграммой в каталоге диаграммы приложения. Например, если ваше приложение названо *app1,* вы создадите *диаграммы/app1/requirements.yaml.*

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Перейдите в каталог диаграммы приложения и используйте [обновление зависимости руля][helm-dependency-update] для обновления зависимостей helm для вашего приложения и загрузки диаграммы из частного репозитория.

```cmd
helm dependency update
```

Проверка *субдиректории диаграмм* с помощью файла *tgz* была добавлена в каталог диаграммы приложения. Например, *диаграммы/app1/charts/mychart-0.1.0.tgz*.

Диаграмма из вашего частного репозитория Helm была загружена и добавлена в ваш проект. Удалите файл *requirements.yaml,* чтобы Dev Spaces не пытался обновить эту зависимость.

## <a name="run-your-application"></a>Запуск приложения

Перейдите к корневому каталогу проекта и запустите `azds up` для проверки успешного выполнения приложения в вашем пространстве разработчиков.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [Хелме и как он работает.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
