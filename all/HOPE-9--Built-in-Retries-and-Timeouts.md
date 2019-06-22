# HOPE 9 -- Built-in Retries and Timeouts

|             |                                             |
| ------------| ------------------------------------------- |
| HOPE:       | 9                                           |
| Title:      | Built-in Retries and Timeouts               |
| Author(s):  | Timothy Crosley <timothy.crosley@gmail.com> |
| Status:     | Provisional                                 |
| Type:       | Standards Track                             |
| Created:    | 5-June-2019                                 |
| Updated:    | 5-June-2019                                 |

## Abstract

One of the most common tasks, when building endpoints is within any API, is handling unexpectedly long tasks, and building in retries.
This HOPE proposes building in support for this use common use-case across all standard HUG API interfaces.

## Proposed API

Each interface router will take the following additional (optional) arguments:

- `timeout: int` - How long in Milliseconds this endpoint is allowed to take per an attempt.
- `attempts: int = 1` - How many attempts the endpoint is allowed to make before raising a `TimeoutException`.
- `time_between_attempts: int = 100` - How long in Milliseconds to wait between each made attempt.
- `exponential_backoff: bool = False` - If set to `True`, the time between attempts will grow exponentially between each attempt.

In use:

```
    @hug.get(timeout=500, attempts=2, time_between_attempts=200):
    def get_user_data(user_id: int) -> UserProfile:
        pass
```

In the above example the `get_user_data` endpoint will return a user profile within 5 seconds, or if unable to do so in time retry after 2 seconds.
If the second attempt also exceeds 5 seconds a `TimeoutException` will be raised.
