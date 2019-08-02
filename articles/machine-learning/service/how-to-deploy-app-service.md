---
title: Развертывание моделей ML в службе приложений Azure (Предварительная версия)
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать службу Машинное обучение Azure для развертывания модели в веб-приложении в службе приложений Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612240"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Развертывание модели машинного обучения в службе приложений Azure (Предварительная версия)

Узнайте, как развернуть модель из службы Машинное обучение Azure в качестве веб-приложения в службе приложений Azure.

> [!IMPORTANT]
> Хотя служба Машинное обучение Azure и служба приложений Azure являются общедоступными, возможность развертывания модели из службы Машинное обучение в службе приложений доступна в предварительной версии.

С помощью службы Машинное обучение Azure можно создавать образы DOCKER из обученных моделей машинного обучения. Этот образ содержит веб-службу, которая получает данные, отправляет ее в модель, а затем возвращает ответ. Службу приложений Azure можно использовать для развертывания образа и предоставляет следующие возможности.

* [Поддержка SSL](/azure/app-service/app-service-web-ssl-cert-load) для безопасной связи между клиентами и службой.
* [Масштабирование](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) до нескольких экземпляров без необходимости повторного развертывания.
* [Расширенная проверка](/azure/app-service/configure-authentication-provider-aad) подлинности для усиления безопасности.

Дополнительные сведения о функциях, предоставляемых службой приложений Azure, см. в статье [Обзор службы приложений](/azure/app-service/overview).

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область службы машинного обучения Azure. Дополнительные сведения см. в статье [Создание рабочей области](setup-create-workspace.md) .
* Обученная модель машинного обучения, зарегистрированная в вашей рабочей области. Если модель отсутствует, используйте [учебник по классификации образов: обучение модели](tutorial-train-models-with-aml.md) для обучения и регистрации.
* Образ DOCKER, созданный из модели. Если у вас нет образа, создайте его с помощью [классификации образа: развертывание модели](tutorial-deploy-models-with-aml.md) .

## <a name="deploy-image-as-a-web-app"></a>Развертывание образа как веб-приложения

1. В [портал Azure](https://portal.azure.com)выберите рабочую область машинное обучение Azure. В разделе __Обзор__ используйте ссылку __реестра__ , чтобы получить доступ к реестру контейнеров Azure для рабочей области.

    ![Снимок экрана с обзором рабочей области](media/how-to-deploy-app-service/workspace-overview.png)

2. В реестре контейнеров Azure выберите __репозитории__, а затем выберите __имя образа__ , который требуется развернуть. Для версии, которую требуется развернуть, выберите запись __...__ , а затем выполните __развертывание в веб-приложение__.

    ![Снимок экрана: развертывание из записи контроля доступа в веб-приложение](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Чтобы создать веб-приложение, укажите имя сайта, подписку, группу ресурсов и выберите план службы приложений или расположение. Наконец, выберите __Создать__.

    ![Снимок экрана: диалоговое окно нового веб-приложения](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Использование веб-приложения

В [портал Azure](https://portal.azure.com)выберите веб-приложение, созданное на предыдущем шаге. В разделе __Обзор__ скопируйте __URL-адрес__. Это значение является __базовым URL-адресом__ службы.

![Снимок экрана с обзором веб-приложения](media/how-to-deploy-app-service/web-app-overview.png)

Веб-служба, которая передает запросы модели, находится в `{baseurl}/score`папке. Например, `https://mywebapp.azurewebsites.net/score`. В следующем примере кода Python показано, как отправить данные в URL-адрес и отобразить ответ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о настройке веб-приложения см. в документации по [службе приложений в Linux](/azure/app-service/containers/) .
* Дополнительные сведения о масштабировании см. [в статье Начало работы с автомасштабированием в Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Дополнительные сведения о поддержке SSL см. [в статье Использование SSL-сертификата в службе приложений Azure](/azure/app-service/app-service-web-ssl-cert-load).
* Дополнительные сведения о проверке подлинности см. в статье [Настройка приложения службы приложений для использования Azure Active Directory входе в](/azure/app-service/configure-authentication-provider-aad)систему.