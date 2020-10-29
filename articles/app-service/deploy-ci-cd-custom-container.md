---
title: CI/CD для пользовательских контейнеров Linux
description: Узнайте, как настроить непрерывное развертывание в пользовательском контейнере Linux в службе приложений Azure. Непрерывное развертывание поддерживается для DOCKER HUB и записи контроля доступа.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8a51fbcb7b7504b9a16e8d0025856c2b007070a9
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928014"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из управляемых репозиториев [реестра контейнеров Azure](https://azure.microsoft.com/services/container-registry/) или [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Включение непрерывного развертывания с помощью ACR

![Снимок экрана с веб-перехватчиком ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите **Служба приложений** в левой части страницы.
3. Выберите имя приложения, для которого следует настроить непрерывное развертывание.
4. На странице **Параметры контейнера** выберите **Один контейнер** .
5. Выберите **Реестр контейнеров Azure** .
6. Выберите **Непрерывное развертывание > Вкл.**
7. Нажмите кнопку **Сохранить** , чтобы включить непрерывное развертывание.

## <a name="use-the-acr-webhook"></a>Использование веб-перехватчика ACR

После включения непрерывного развертывания можно просмотреть созданный веб-перехватчик на странице веб-перехватчиков Реестра контейнеров Azure.

![Снимок экрана, на котором показано, где можно просмотреть только что созданный веб-перехватчик на странице веб перехватчика реестра контейнеров Azure.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

В Реестре контейнеров щелкните "Веб-перехватчики", чтобы просмотреть текущие веб-перехватчики.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Включение непрерывного развертывания с помощью Docker Hub (необязательно)

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите **Служба приложений** в левой части страницы.
3. Выберите имя приложения, для которого следует настроить непрерывное развертывание.
4. На странице **Параметры контейнера** выберите **Один контейнер** .
5. Выберите **Docker Hub** .
6. Выберите **Непрерывное развертывание > Вкл.**
7. Нажмите кнопку **Сохранить** , чтобы включить непрерывное развертывание.

![Снимок экрана с параметрами приложения](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Скопируйте URL-адрес веб-перехватчика. Чтобы добавить веб-перехватчик для Docker Hub, ознакомьтесь со статьей о <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">веб-перехватчиках для Docker Hub</a>.

## <a name="automate-with-cli"></a>Автоматизация с помощью CLI

Чтобы настроить CI/CD с помощью Azure CLI, выполните команду [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) , чтобы создать URL-адрес перехватчика. URL-адрес можно использовать для настройки реестра контейнеров DockerHub или Azure.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Дальнейшие действия

* [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
* [Создание веб-приложения .NET Core в службе приложений на платформе Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Создание веб-приложения Ruby в службе приложений на платформе Linux](quickstart-ruby.md)
* [Краткое руководство. Запуск пользовательского контейнера в Службе приложений](quickstart-custom-container.md?pivots=container-linux)
* [Служба приложений в Linux: вопросы и ответы](faq-app-service-linux.md)
* [Настройка пользовательских контейнеров Linux](configure-custom-container.md)