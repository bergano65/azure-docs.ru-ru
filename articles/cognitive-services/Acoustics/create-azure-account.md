---
title: Настройка пакетной учетной записи Project Acoustics Azure
titlesuffix: Azure Cognitive Services
description: Это способ описывают настройку учетной записи Azure Batch для использования с интеграцией проекта Acoustics Unity и Unreal engine.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855078"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Настройка пакетной учетной записи Project Acoustics Azure
Это способ описывают настройку учетной записи Azure Batch для использования с интеграцией проекта Acoustics Unity и Unreal engine.

## <a name="get-an-azure-subscription"></a>Оформление подписки Azure
До настройки учетных записей хранения и учетной записи пакетной службы необходимо получить [подписку Azure](https://azure.microsoft.com/free/). Если регистрируетесь впервые, Azure предоставляет несколько бесплатных ресурсов, которые ограничены по времени, и кредит в сумме 200 долл. США.

## <a name="create-azure-batch-and-storage-accounts"></a>Создание учетных записей хранения и учетной записи пакетной службы Azure
Далее следуйте [этим инструкциям](https://docs.microsoft.com/azure/batch/batch-account-create-portal), чтобы настроить учетную запись пакетной службы Azure и связанные учетные записи хранения Azure.

Выберите параметры по умолчанию для учетных записей хранения и учетной записи пакетной службы:
  
  ![Скриншот новых параметров пакетных учетных записей Azure, показывающих параметры по умолчанию](media/new-batch-account-create.png)

  ![Скриншот новых параметров хранения Azure, показывающих параметры по умолчанию](media/batch-storage-account-create.png)

Развертывание учетных записей в Azure занимает несколько минут. В правом верхнем углу портала появится уведомление о завершении.
  
  ![Скриншот развернутых учетных записей Azure](media/batch-accounts-deploy-notification.png)

Теперь учетные записи должны отображаться на панели мониторинга.
  
  ![Скриншот панели мониторинга портала Azure, показывающей учетную запись пакета и хранения](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Настройка пользовательского интерфейса акустической обработки с использованием учетных данных Azure
Щелкните ссылку на учетную запись пакетной службы на панели мониторинга, а затем на открывшейся странице щелкните ссылку **Ключи** для доступа к своим учетным данным.
  
  ![Скриншот учетной записи Azure Batch со ссылкой на страницу Keys выделен](media/batch-access-keys.png)

  ![Скриншот страницы ключей учетной записи Azure Batch с ключами доступа](media/batch-keys-info.png)

Щелкните ссылку **Учетная запись хранения** на странице, чтобы получить доступ к учетным данным учетной записи хранения Azure.
  
  ![Скриншот страницы ключей учетной записи хранилища Azure с ключами доступа](media/storage-keys-info.png)

Введите эти учетные данные в [Unity испечь плагин](unity-baking.md) или [Unreal испечь плагин.](unreal-baking.md)

## <a name="node-types-and-region-support"></a>Типы узлов и поддержка регионов
Project Acoustics требует, чтобы fsv2- и H-серии вычислили оптимизированные vM-узлы Azure, которые могут быть поддержаны не во всех регионах Azure. Просмотрите [эту таблицу](https://azure.microsoft.com/global-infrastructure/services), чтобы выбрать правильное расположение для учетной записи пакетной службы.
![Скриншот, показывающий виртуальные машины Azure по регионам](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Обновление квоты
Учетные записи пакетной службы Azure подготавливаются с ограничением в 20 вычислительных ядер. Мы можем увеличить этот предел для более быстрого времени выпечки, потому что вы можете параллелизировать рабочую нагрузку акустики во многих узлах, вплоть до количества точек зонда в вашей сцене. Вы можете запросить увеличение квоты, щелкнув ссылку **Квота** на странице портала для пакетной службы Azure, а затем щелкните **Запросить увеличение квоты**:

![Скриншот страницы Квоты Лазурного](media/azure-quotas.png)

## <a name="next-steps"></a>Дальнейшие действия
* Интегрируйте плагин Project Acoustics в свой проект [Unity](unity-integration.md) или [Unreal](unreal-integration.md)

