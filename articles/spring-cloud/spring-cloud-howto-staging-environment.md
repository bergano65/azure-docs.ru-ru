---
title: Настройка промежуточной среды в Azure Spring Cloud | Документация Майкрософт
description: Узнайте, как использовать развертывание по методу Blue-Green с помощью Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053115"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Настройка промежуточной среды в облаке Azure весны

**Эта статья применима к:** ✔️ Java

В этой статье описывается настройка промежуточного развертывания с помощью шаблона развертывания "синий-зеленый" в Azure Веснного облака. Сине-зеленое развертывание — это шаблон непрерывной поставки Azure DevOps, который предполагает сохранение существующей (синей) версии при развертывании новой (зеленой). В этой статье показано, как разместить промежуточное развертывание в рабочей среде, не изменяя рабочее развертывание напрямую.

## <a name="prerequisites"></a>Предварительные требования

* Выполняющееся приложение.  См. раздел [Краткое руководство. развертывание первого облачного приложения Azure](spring-cloud-quickstart.md).
* [Расширение Azure CLI ASC](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Если вы хотите использовать другое приложение в этом примере, необходимо внести простое изменение в общедоступную часть приложения.  Это изменение отличает промежуточное развертывание от развертывания в рабочей среде.

>[!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, которую можно использовать для выполнения инструкций, описанных в этой статье.  Он содержит общие, предварительно установленные инструменты Azure, включая последние версии Git, JDK, Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com).  Дополнительные сведения см. в разделе [обзор Azure Cloud Shell](../cloud-shell/overview.md).

Чтобы настроить промежуточную среду в Azure Веснного облака, следуйте инструкциям в следующих разделах.

## <a name="install-the-azure-cli-extension"></a>Установка расширения Azure CLI

Установите расширение Azure Spring Cloud для Azure CLI с помощью следующей команды:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Просмотр приложений и развертываний

Просмотрите развернутые приложения, выполнив следующие процедуры.

1. Перейдите к облачному экземпляру Azure весны в портал Azure.

1. В левой области навигации откройте **развертывания**.

    [![Развертывание — нерекомендуемый](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Откройте колонку "приложения", чтобы просмотреть приложения для своего экземпляра службы.

    [![Приложения — панель мониторинга](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Можно щелкнуть приложение и просмотреть сведения.

    [![Общие сведения о приложениях](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Откройте колонку **развертывания** , чтобы просмотреть все развертывания приложения. В сетке развертывания показано, является ли развертывание производственным или промежуточным.

    [![Панель мониторинга развертываний](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Можно щелкнуть имя развертывания, чтобы просмотреть общие сведения о развертывании. В этом случае единственное развертывание называется *Default*.

    [![Общие сведения о развертываниях](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Создание промежуточного развертывания

1. В локальной среде разработки внесите небольшие изменения в приложение. Это позволяет легко различать два развертывания. Чтобы создать пакет JAR, выполните следующую команду: 

    ```console
    mvn clean package -DskipTests
    ```

1. В Azure CLI создайте новое развертывание и присвойте ему имя промежуточного развертывания "Green".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. После успешного завершения развертывания CLI откройте страницу приложения на **панели мониторинга приложения** и просмотрите все экземпляры на вкладке **развертывания** слева.

   [![Панель мониторинга развертываний после зеленого развертывания](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> Состояние обнаружения — *OUT_OF_SERVICE* , поэтому трафик не будет направляться в это развертывание до завершения проверки.

## <a name="verify-the-staging-deployment"></a>Проверка промежуточного развертывания

Чтобы проверить работу зеленого промежуточного развертывания, выполните следующие действия.
1. Перейдите в раздел **развертывания** и щелкните `green` **промежуточное развертывание**.
1. На странице **Обзор** щелкните **конечную точку теста**.
1. Откроется промежуточная сборка, в которой отобразятся изменения.

>[!TIP]
> * Убедитесь, что конечная точка теста заканчивается косой чертой (/), чтобы убедиться, что CSS-файл загружен правильно.  
> * Если в браузере для просмотра страницы требуется ввести учетные данные, используйте параметр [Декодировать в URL-адресе](https://www.urldecoder.org/) для декодирования тестовой конечной точки. Декодирование URL-адреса возвращает URL-адрес в формате "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.IO/Gateway/Green".  Используйте эту форму для доступа к конечной точке.

>[!NOTE]    
> Параметры сервера конфигурации применяются как к промежуточной, так и рабочей среде. Например, если задать путь контекста ( `server.servlet.context-path` ) для шлюза приложений на сервере конфигурации в качестве *сомепас*, то путь к зеленому развертыванию изменится на "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.IO/Gateway/Green/somepath/...".
 
 Если на этом этапе посетить общедоступный шлюз приложений, вы увидите старую страницу без новых изменений.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Установка зеленого развертывания в качестве рабочей среды

1. После проверки изменений в промежуточной среде ее можно отправить в рабочую среду. Вернитесь в **Управление развертыванием** и выберите приложение в данный момент `Production` .

1. Нажмите кнопку с многоточием после **состояния регистрации** и задайте для рабочей сборки значение `staging` .

   [Развертывание ![ Настройка промежуточного развертывания](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Вернитесь на страницу **Управление развертыванием** .  `green`Отобразится состояние развертывания развертывания.  Теперь это выполняемая рабочая сборка.

   [![Результаты задания промежуточного развертывания для развертывания](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Скопируйте и вставьте URL-адрес в новое окно браузера. После внесения изменений отобразится страница новое приложение.

>[!NOTE]
> После настройки зеленого развертывания в качестве рабочей среды, предыдущее развертывание станет промежуточным развертыванием.

## <a name="modify-the-staging-deployment"></a>Изменение промежуточного развертывания

Если вы не удовлетворены изменениями, вы можете изменить код приложения, создать новый пакет JAR и передать его в зеленое развертывание с помощью Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Удаление промежуточного развертывания

Чтобы удалить промежуточное развертывание из порта Azure, перейдите на страницу промежуточного развертывания, а затем нажмите кнопку **Удалить** .

Кроме того, можно удалить промежуточное развертывание из Azure CLI, выполнив следующую команду:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Дальнейшие действия

* [CI/CD для Azure Веснного облака](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)