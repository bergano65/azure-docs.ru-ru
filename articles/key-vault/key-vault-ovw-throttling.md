---
title: Руководство по регулированию хранилища ключей Azure
description: Регулирование Key Vault позволяет ограничить число одновременных вызовов для предотвращения чрезмерного использования ресурсов.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 0f8aafce4c4feeed742504db84664e4dfd472ca6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304937"
---
# <a name="azure-key-vault-throttling-guidance"></a>Руководство по регулированию хранилища ключей Azure

Регулирование — это процесс, позволяющий ограничить число одновременных вызовов к службе Azure для предотвращения чрезмерного использования ресурсов. Хранилище ключей Azure (AKV) может обрабатывать большое число запросов. Если это число превышает приемлемое, для обеспечения оптимальной производительности и надежности службы AKV используется регулирование клиентских запросов.

Ограничения регулирования зависят от сценария. Например, при выполнении большого объема операций записи возможность регулирования будет более высокой по сравнению с ситуацией, когда выполняются только операции чтения.

## <a name="how-does-key-vault-handle-its-limits"></a>Как хранилище ключей обрабатывает свои ограничения?

Для предотвращения некорректного использования ресурсов и обеспечения качества обслуживания для всех клиентов хранилища ключей в самом хранилище существуют ограничения служб. При превышении порогового значения службы хранилище ключей ограничивает все последующие запросы от этого клиента на определенный период времени. В этом случае хранилище ключей возвращает код состояния HTTP 429 (слишком много запросов), и запросы завершаются сбоем. Кроме того, хранилище ключей подсчитывает невыполненные запросы, возвращающих код состояния 429, и сравнивает их с ограничениями регулирования. 

Если у вас есть реальный пример использования с более высокими ограничениями регулирования, пожалуйста, расскажите нам о нем.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Регулирование приложения в ответ на ограничения служб

Ниже приведены **рекомендации** следует реализовать во время регулирования службы:
- Сократите число операций на один запрос.
- Уменьшите частоту запросов.
- Избегайте немедленных повторных попыток. 
    - Все запросы учитываются, и их количество сравнивается с ограничениями использования.

При реализации обработки ошибок для приложения используйте код ошибки HTTP 429, чтобы определить необходимость регулирования на стороне клиента. Если запрос снова завершается сбоем с кодом ошибки HTTP 429, это означает, что вы по-прежнему не соблюдаете ограничения службы Azure. Продолжайте использовать рекомендуемый метод регулирования на стороне клиента и повторяйте выполнять запрос, пока он не будет успешно выполнен.

Ниже показан код, который реализует экспоненциальную задержку. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Используя этот код в клиенте C\# приложения прост. В следующем примере показан способ, как это сделать с помощью класса HttpClient.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Помните, что этот код подходит только в качестве подтверждения концепции. 

### <a name="recommended-client-side-throttling-method"></a>Рекомендуемый метод регулирования на стороне клиента

При возврате кода ошибки HTTP 429 начните регулирование клиента с помощью метода экспоненциального откладывания:

1. Подождите 1 с и повторите запрос
2. Если запрос по-прежнему не выполнен, подождите 2 с и повторите запрос
3. Если запрос по-прежнему не выполнен, подождите 4 с и повторите запрос
4. Если запрос по-прежнему не выполнен, подождите 8 с и повторите запрос
5. Если запрос по-прежнему не выполнен, подождите 16 с и повторите запрос

На этом этапе вы не должны получать коды ответа HTTP 429.

## <a name="see-also"></a>См. также

Более подробные сведения о регулировании для Microsoft Cloud см. в разделе [Шаблон регулирования](https://docs.microsoft.com/azure/architecture/patterns/throttling).

