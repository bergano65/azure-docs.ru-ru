---
title: Создание Cloud Foundry в Azure
description: Узнайте, как настроить параметры, необходимые для подготовки к работе кластера Cloud Foundry PCF в Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250621"
---
# <a name="create-cloud-foundry-on-azure"></a>Создание Cloud Foundry в Azure

Это руководство предоставляет быстрые шаги по созданию параметров, необходимых для подготовки кластера Pivotal Cloud Foundry PCF в Azure.  Решение Pivotal Cloud Foundry можно найти, выполнив поиск в Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Замещающий текст](media/deploy/pcf-marketplace.png "Поиск Pivotal Cloud Foundry в Azure")


## <a name="generate-an-ssh-public-key"></a>Создание открытого ключа SSH

Существует несколько способов создать открытый ключ SSH с помощью Windows, Mac или Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Щелкните здесь для просмотра [инструкций]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) для вашей среды.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

> [!NOTE]
>
> Для создания субъекта-службы требуется разрешение владельца учетной записи.  Кроме того, можно написать сценарий для автоматизации создания субъекта-службы. Например, с помощью команды [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) Azure CLI.

1. Войдите в свою учетную запись Azure.

    `az login`

    ![Замещающий текст](media/deploy/az-login-output.png "Вход Azure CLI")
 
    Скопируйте значение "id" как **идентификатор подписки** и значение **tenantId** для дальнейшего использования.

2. Определите подписку по умолчанию для этой конфигурации.

    `az account set -s {id}`

3. Создайте приложение AAD для вашего PCF и укажите уникальный буквенно-цифровой пароль.  Сохраните пароль как **clientSecret** для дальнейшего использования.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Скопируйте значение "appId" в выходных данных как **ClientID** для дальнейшего использования.

    > [!NOTE]
    >
    > Выберите домашнюю страницу приложения и URI идентификатора.  Например, http://www.contoso.com.

4. Создайте субъект-службу с новым "appId".

    `az ad sp create --id {appId}`

5. Задайте **Contributor** в качестве разрешения роли субъекта-службы.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Кроме того, можно использовать…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Замещающий текст](media/deploy/svc-princ.png "Назначение роли субъекту-службе")

6. Убедитесь, что вы можете успешно войти в субъект-службу с помощью appId, пароля и tenantId.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Создайте JSON-файл в следующем формате с помощью всех указанных выше значений: **subscription ID**, **tenantId**, **clientID** и **clientSecret** — которые вы скопировали ранее.  Сохраните файл.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Получение токена Pivotal Network

1. Зарегистрируйтесь или войдите в учетную запись [Pivotal Network](https://network.pivotal.io).
2. Щелкните имя своего профиля в верхней правой части страницы, а затем выберите "Изменение профиля".
3. Прокрутите до нижней части страницы и скопируйте значение **Legacy API token** (Устаревший токен API).  Это ваше значение **токена Pivotal Network**, которое будет использоваться позже.

## <a name="provision-your-cloud-foundry-on-azure"></a>Подготовка к работе Cloud Foundry в Azure

1. Теперь у вас есть все параметры, необходимые для подготовки к работе кластера [Pivotal Cloud Foundry в Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Введите параметры и создайте кластер PCF.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Проверка развертывания и вход в Pivotal Ops Manager

1. Кластер PCF должен отображать состояние развертывания.

    ![Замещающий текст](media/deploy/deployment.png "Состояние развертывания Azure")

2. Щелкните ссылку **Развертывания** на панели навигации слева, чтобы получить учетные данные своего PCF Ops Manager, а затем щелкните **Имя развертывания** на следующей странице.
3. На панели навигации слева щелкните ссылку **Выходные данные**, чтобы отобразить URL-адрес, имя пользователя и пароль для PCF Ops Manager.  Значение "OPSMAN-FQDN" является URL-адресом.
 
    ![Замещающий текст](media/deploy/deploy-outputs.png "Выходные данные развертывания Cloud Foundry")
 
4. Перейдите по URL-адресу в веб-браузере и введите учетные данные для входа из предыдущего шага.

    ![Замещающий текст](media/deploy/pivotal-login.png "Страница входа в Pivotal")
         
    > [!NOTE]
    >
    > Если произошел сбой браузера Internet Explorer из-за предупреждающего сообщения о небезопасности веб-сайта, щелкните "Подробнее" и "Перейти на веб-страницу".  Для Firefox щелкните "Дополнительно" и добавьте сертификацию для продолжения.

5. Ваш PCF Ops Manager должен отображать развернутые экземпляры Azure. Теперь здесь можно приступать к развертыванию ваших приложений и управлению ими!
               
    ![Замещающий текст](media/deploy/ops-mgr.png "Развернутый экземпляр Azure в Pivotal")
 
