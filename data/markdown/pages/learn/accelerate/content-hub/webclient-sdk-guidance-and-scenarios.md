---
title: 'Webclient SDK Guidance and Scenarios'
description: 'A deeper dive on best practice on using the Webclient SDK.'
hasSubPageNav: true
hasInPageNav: true
area: ['accelerate']
lastUpdated: '2024-11-21'
breadcrumb: 'Sitecore Accelerate Cookbooks > Content Hub (CH) - Sitecore Recipes > CH Implementation > CH Custom Logic'
author: 'Chris Howarth'
audience: ''
---
## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/2049.png) **Context**

Further and more specific information and examples to supplement the CH External Scripting Guidance recipe. Specifically related to the Webclient SDK.

## ![(lightbulb)](/images/learn/accelerate/content-hub/img/icons/emoticons/lightbulb_on.png) **Execution**

### Impersonating Another User

By default, any scripted CRUD operations will be logged as the authenticated user (created by, modified by, etc.). In many scenarios this may not be ideal, or for entities that should be related to another User, may not work at all.

The Webclient SDK offers a way to impersonate another user, an example in the below:

```csharp

IMClient impersonationClient = await MClient.ImpersonateAsync("Demo.User");
```

You will then use this new client to perform activities. Note that this user will likely have far fewer permissions than the Superuser account you are using by default. We recommend the use of try catch blocks and Exception handling as methods such as SaveAsync may fail due to lack of permissions.

e.g.

```csharp

catch (ValidationException vex)
{
    Console.WriteLine($"FAILURE ({asset.Id}): {vex.Message} : {string.Join(", ", vex.Failures)}");
}
```

### Parallel Execution Helper

Executing bulk CRUD operations can take a long time if your dataset is large. Below is an example using the 'Polly' library to perform actions in parallel. The maximum number of threads and maximum requests per second can be configured. Note that Content Hub will throttle your requests per second (this is handled automatically by the Web Client SDK), so experimentation may be necessary to find a good balance.

#### Event Limiter example code

```csharp

using System;
using System.Collections.Generic;
using System.Threading;

namespace Commands.Helpers
{
    public class EventLimiter
    {
        private readonly Queue<DateTime> _requestTimes;
        private static readonly object Lock = new object();
        private readonly int _maxRequests;
        private readonly TimeSpan _timeSpan;
        public EventLimiter(int maxRequests, TimeSpan timeSpan)
        {
            _maxRequests = maxRequests;
            _timeSpan = timeSpan;
            _requestTimes = new Queue<DateTime>(maxRequests);
        }
        private void SynchronizeQueue()
        {
            lock (Lock)
            {
                while ((_requestTimes.Count > 0) && (_requestTimes.Peek().Add(_timeSpan) < DateTime.UtcNow))
                    _requestTimes.Dequeue();
            }
        }
        public bool CanRequestNow()
        {
            SynchronizeQueue();
            return _requestTimes.Count < _maxRequests;
        }
        public void EnqueueRequest()
        {
            try
            {
                while (!CanRequestNow())
                {
                    Console.WriteLine("---------");
                    Console.WriteLine($"zzzZ zzzzZ     Slowing down thread {Thread.CurrentThread.ManagedThreadId}...     zzzzzzZ zzzzzzzZ");
                    var sleepTime = _requestTimes.Peek().Add(_timeSpan).Subtract(DateTime.UtcNow);
                    Thread.Sleep(sleepTime.TotalSeconds > 0 ? sleepTime : _timeSpan);
                }
                _requestTimes.Enqueue(DateTime.UtcNow);
            }
            catch (Exception)
            {
                Thread.Sleep(_timeSpan);
            }
        }
    }
}
```

#### Parallel Execution Implementation

```csharp

Bulkhead = Policy.BulkheadAsync(Settings.NumberOfThreads, int.MaxValue);
RequestsLimiter = new EventLimiter(Settings.MaxNumberOfRequestsPerSecond, new TimeSpan(0, 0, 0, 1, 0));

await foreach (IEntity entity in QueryManager.GetResults(query, loadConfiguration))
{
    var t = Bulkhead.ExecuteAsync(async () => { await UpdateEntityContentType(entity); });
    _updateTasks.Add(t);
}

async Task UpdateEntityContentType(IEntity content)
{
    ...
    ...
    RequestsLimiter.EnqueueRequest();
    await MClient.Entities.SaveAsync(...
}
```

### Raw Queries

Sometimes you may need to perform ‘raw’ API calls. Reasons may include: the API feature you require is not present in the SDK, or where you have a requirement to get and parse json responses. This allows you to leverage the REST API from the web client SDK.

Be careful with making calls in this way, as some APIs are intended for internal Content Hub use and may be decommissioned or repurposed at any time. Refer to the API reference for details.

Example:

```csharp

HttpResponseMessage response = await MClient.Raw.GetAsync("http://localhost:8080/api/entities/1000");
response.EnsureSuccessStatusCode();
HttpResponseMessage resource = await response.Content.ReadAsJsonAsync<EntityResource> ();
```

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f5e8.png) **Insights**

Key takeaways, deeper analysis and considerations - this is the space where you can deep dive further in the conversation or topic.

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Recipes

|     |     |
| --- | --- |
|     | **Recipe** |
| 1   |     |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Documentation

|     |     |
| --- | --- |
|     | **Documentation Link** |
| 1   |     |
| 2   |     |

## ![(blue star)](/images/learn/accelerate/content-hub/img/icons/emoticons/72/1f517.png) Related Learning Materials

List any related Learning activity to this recipe.

|     |     |
| --- | --- |
|     | **Learning link** |
| 1   |     |
| 2   |     |