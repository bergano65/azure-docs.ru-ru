---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 426158945e609b2bb46dd9fbbbbe378f25cd93f1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206249"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Анализ форм для пар "ключ — значение" и таблиц

Теперь с помощью новой обученной модели нужно проанализировать документ и извлечь из него пары "ключ — значение" и таблицы. Вызовите API **[анализа формы](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm)** , выполнив следующий код в новом сценарии Python. Перед выполнением сценария внесите следующие изменения:

1. Замените `<file path>` путем к файлу формы (например, C:\temp\file.pdf). Это также может быть URL-адрес удаленного файла. При работе с этим кратким руководством можно использовать файлы в папке **Test** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Замените `<model_id>` идентификатором модели, полученным в предыдущем разделе.
1. Замените `<endpoint>` конечной точкой, полученной из ключа подписки Распознавателя документов (см. ресурс Распознавателя документов на вкладке **Обзор**).
1. Замените `<file type>` типом файла Поддерживаемые типы: `application/pdf`, `image/jpeg`, `image/png`, `image/tiff`.
1. Замените `<subscription key>` ключом своей подписки.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Сохраните код как файл с расширением .py. Например, *form-recognizer-analyze.py*.
1. Откройте окно командной строки.
1. В командной строке выполните пример кода с помощью команды `python`. Например, `python form-recognizer-analyze.py`.

Вызвав API **анализа формы**, вы получите ответ `201 (Success)`, содержащий заголовок **Operation-Location**. Значение этого заголовка — это идентификатор, используемый для отслеживания результатов операции анализа. Приведенный выше сценарий выводит значение этого заголовка в консоль.

## <a name="get-the-analyze-results"></a>Получение результатов анализа

Добавьте следующий код в нижнюю часть сценария Python. Для получения результатов анализа в новом вызове API используется значение идентификатора из предыдущего вызова. Операция **анализа формы** является асинхронной, поэтому этот сценарий вызывает API с регулярными интервалами, пока не станут доступны результаты. Мы рекомендуем установить интервал одну секунду или более.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
