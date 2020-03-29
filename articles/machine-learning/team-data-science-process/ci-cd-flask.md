---
title: Создание конвейера CI/CD с помощью конвейеров Azure - Процесс получения данных team
description: Создание непрерывной интеграции и непрерывной доставки для приложений искусственного интеллекта (ИИ) с использованием Docker и Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721835"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Создание конвейеров CI/CD для приложений ИИ с использованием Azure Pipelines, Docker и Kubernetes

Приложение искусственного интеллекта (ИИ) — это код приложения, встроенный в модель машинного обучения (ML). Для приложения ИИ всегда есть два потока работы: специалисты по обработке данных создают модель ML, а разработчики приложений создают приложение и предоставляют его конечным пользователям для потребления. В этой статье описывается, как реализовать конвейер непрерывной интеграции и непрерывной доставки (CI/CD) для приложения ИИ, которое встраивает модель ML в исходный код приложения. В примере кода и учебника используется веб-приложение Python Flask и получена предподготовленная модель из частной учетной записи хранения капли Azure. Вы также можете использовать учетную запись хранения AWS S3.

> [!NOTE]
> Следующий процесс является одним из нескольких способов сделать CI / CD. Есть альтернативы этому инструментарию и предпосылки.

## <a name="source-code-tutorial-and-prerequisites"></a>Исходный код, учебник и предпосылки

Вы можете скачать [исходный код](https://github.com/Azure/DevOps-For-AI-Apps) и [подробный учебник](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) от GitHub. Следуйте инструкциям по реализации конвейера CI/CD для собственного приложения.

Для использования загруженного исходного кода и учебника необходимы следующие предпосылки: 

- [Репозиторий исходного кода](https://github.com/Azure/DevOps-For-AI-Apps) разряженный на вашу учетную запись GitHub
- [Организация Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Лазурный CLI](/cli/azure/install-azure-cli)
- Контейнерная [служба Azure для кластера Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для выполнения команд и получения конфигурации из кластера AKS 
- Учетная [запись реестра контейнеров Azure (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Резюме конвейера CI/CD

Каждый новый git-коммит выключает конвейер Build. Сборка надежно вытягивает последнюю модель ML из учетной записи хранения капли и упаковывает ее с кодом приложения в один контейнер. Такое разъединение разработки приложений и рабочих потоков данных гарантирует, что производственное приложение всегда работает с последним кодом с последней моделью ML. Если приложение проходит тестирование, конвейер надежно хранит изображение сборки в контейнере Docker в ACR. Затем конвейер выпуска развертывает контейнер с помощью AKS. 

## <a name="cicd-pipeline-steps"></a>Шаги конвейера CI/CD

На следующей диаграмме и шагах описывается архитектура конвейера CI/CD:

![Архитектура конвейера CI/CD](./media/ci-cd-flask/architecture.png)

1. Разработчики работают над кодом приложения в IDE по своему выбору.
2. Разработчики зачисляют код в Azure Repos, GitHub или другого поставщика управления источниками Git. 
3. Отдельно специалисты по обработке своей модели ML работают над разработкой.
4. Данные ученые публикуют готовую модель в репозиторий модели, в данном случае счет хранения кабл. 
5. Azure Pipelines начинает сборку на основе коммита Git.
6. Конвейер Build вытягивает последнюю модель ML из хранилища капли и создает контейнер.
7. Конвейер толкает изображение сборки в частное репозиторий изображения в ACR.
8. Конвейер выпуска стартует на основе успешной сборки.
9. Конвейер вытягивает последнее изображение из ACR и развертывает его через кластер Kubernetes на AKS.
10. Запросы пользователей на приложение проходят через DNS-сервер.
11. Сервер DNS передает запросы балансиру нагрузочку и отправляет ответы обратно пользователям.

## <a name="see-also"></a>См. также

- [Командный процесс обработки и анализа данных (TDSP)](/azure/machine-learning/team-data-science-process/).
- [Документация по службам машинного обучения Azure (предварительная версия)](/azure/machine-learning/)
- [Лазурные ДевОпс](https://azure.microsoft.com/services/devops/)
- [Службы Azure Kubernetes (AKS)](/azure/aks/intro-kubernetes)
