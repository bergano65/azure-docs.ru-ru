---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Python
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как для запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Python для вызова конечной точки службы пользовательского поиска Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: e3e4256d3654f532f16d33c77f4c7e8cb7e93dd4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162642"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" (Python)

В этом кратком руководстве показано, как запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Python для вызова конечной точки службы пользовательского поиска Bing. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- Готовый экземпляр службы пользовательского поиска. Ознакомьтесь с разделом [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы "Пользовательский поиск Bing").
- Установленный компонент [Python](https://www.python.org/).
- ключ подписки; Ключ подписки можно получить при активации [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). Кроме того, вы можете использовать ключ платной подписки из панели мониторинга Azure (см. раздел об [учетной записи API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Выполнение кода

Чтобы запустить этот пример, выполните следующее.

1. Создайте папку для своего кода.  
  
2. Из командной строки или терминала с правами администратора перейдите в эту папку.  
  
3. Установите модуль Python **requests**.  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Создайте файл с именем BingCustomSearch.py в созданной папке и скопируйте в него следующий код. Замените **YOUR-SUBSCRIPTION-KEY** и **YOUR-CUSTOM-CONFIG-ID** своими ключом подписки и идентификатором конфигурации.  
  
    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```  
  
7. Выполните код с помощью приведенной ниже команды.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Дополнительная информация
- [Настройка размещенного пользовательского интерфейса](./hosted-ui.md)
- [Use decoration markers to highlight text](./hit-highlighting.md) (Использование маркеров оформления для выделения текста)
- [Разбивка веб-страниц на страницы](./page-webpages.md)
