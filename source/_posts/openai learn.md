---

---

# UI

# request

### conversion

- request

  ```
  curl POST https://chat.openai.com/backend-api/conversation
  ```

- payload

```json
{
  "action": "next",
  "messages": [
    {
      "id": "aaa25811-d62c-4f23-963a-77c6eb90b899",
      "author": {
        "role": "user"
      },
      "content": {
        "content_type": "text",
        "parts": [
          "你是如何带上上下文进行问答的，用python代码说下示例"
        ]
      },
      "metadata": {}
    }
  ],
  "conversation_id": "0e02cc29-8cc2-4305-91a1-a3db1427f400",
  "parent_message_id": "36d4ff94-fb3f-4f9b-beeb-d9c93f5752fe",
  "model": "text-davinci-002-render-sha",
  "timezone_offset_min": -480,
  "suggestions": [],
  "history_and_training_disabled": false,
  "arkose_token": null,
  "conversation_mode": {
    "kind": "primary_assistant"
  },
  "force_paragen": false,
  "force_rate_limit": false
}
```

### ces/v1/t

- request

  ```
  https://chat.openai.com/ces/v1/t
  ```

- payload

  ```json
  {
    "timestamp": "2024-01-19T08:35:53.805Z",
    "integrations": {
      "Segment.io": true
    },
    "userId": "user-EyJTf0sEV6sYCFhgOwqCHGD2",
    "anonymousId": "dafc8561-54d1-4cb8-9cdf-91e5e4f7162e",
    "event": "View Template Prompt Ignore Suggestions",
    "type": "track",
    "properties": {
      "origin": "chat",
      "openai_app": "API"
    },
    "context": {
      "page": {
        "path": "/c/0e02cc29-8cc2-4305-91a1-a3db1427f400",
        "referrer": "",
        "search": "",
        "title": "feel",
        "url": "https://chat.openai.com/c/0e02cc29-8cc2-4305-91a1-a3db1427f400"
      },
      "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
      "locale": "zh-CN",
      "library": {
        "name": "analytics.js",
        "version": "npm:next-1.56.0"
      },
      "userAgentData": {
        "brands": [
          {
            "brand": "Not_A Brand",
            "version": "8"
          },
          {
            "brand": "Chromium",
            "version": "120"
          },
          {
            "brand": "Google Chrome",
            "version": "120"
          }
        ],
        "mobile": false,
        "platform": "macOS"
      }
    },
    "messageId": "ajs-next-c0f7b6265ed3eab440b0d44f21f53ffa",
    "sentAt": "2024-01-19T08:35:54.068Z",
    "_metadata": {
      "bundled": [
        "Segment.io"
      ],
      "unbundled": [],
      "bundledIds": []
    }
  }
  ```

  

### rgstr

- request

  ```
  https://events.statsigapi.net/v1/rgstr
  ```

- payload

  ```json
  {
    "events": [
      {
        "eventName": "chatgpt_prompt_ignore_suggestions",
        "user": {
          "userID": "user-EyJTf0sEV6sYCFhgOwqCHGD2",
          "custom": {
            "is_paid": false,
            "workspace_id": "b27c1f77-bb94-4e8f-a6d3-4b30a0eec927"
          },
          "locale": "zh-CN",
          "statsigEnvironment": {
            "tier": "production"
          }
        },
        "value": null,
        "metadata": null,
        "time": 1705653353804,
        "statsigMetadata": {
          "currentPage": "https://chat.openai.com/c/0e02cc29-8cc2-4305-91a1-a3db1427f400"
        }
      }
    ],
    "statsigMetadata": {
      "sdkType": "js-client",
      "sdkVersion": "4.39.3",
      "stableID": "f0eaa0cd-f15f-4d71-ab51-8a91e92aea53"
    }
  }
  ```

  

### backend/lat/r

- request

  ```
  https://chat.openai.com/backend-api/lat/r
  ```

- payload

  ```json
  {
    "server_request_id": "847dbf366a92408c-SIN",
    "model": "text-davinci-002-render-sha",
    "preflight_time_ms": 0.09999990463256836,
    "count_tokens": 472,
    "ts_first_token_ms": 1548.7000000476837,
    "ts_max_token_time_ms": 764,
    "ts_mean_token_without_first_ms": 30.007855625922005,
    "ts_median_token_without_first_ms": 21.799999952316284,
    "ts_min_token_time_ms": 0.09999990463256836,
    "ts_p95_token_without_first_ms": 88.99999988079071,
    "ts_p99_token_without_first_ms": 139.65999991893773,
    "ts_std_dev_token_ms": 44.98320283392762,
    "ts_total_request_ms": 15695.399999856949
  }
  ```

  

# API

## client.chat.completions.create

- request

  ```json
   response = client.chat.completions.create(
              model="gpt-3.5-turbo-1106",
              messages=[
                  {
                      "role": "system",
                      "content": prompt_str,
                  },
                  {
                      "role": "user",
                      "content": user_input.question,
                  },
              ],
          )
  ```

- response

  ```json
  {
      "id": "chatcmpl-8ify2KU1ANzQPQyD8WVMp6rZWgYtj",
      "choices": [
          {
              "finish_reason": "stop",
              "index": 0,
              "logprobs": null,
              "message": {
                  "content": "",
                  "role": "assistant",
                  "function_call": null,
                  "tool_calls": null
              }
          }
      ],
      "created": 1705658446,
      "model": "gpt-3.5-turbo-1106",
      "object": "chat.completion",
      "system_fingerprint": "fp_c596c86df9",
      "usage": {
          "completion_tokens": 51,
          "prompt_tokens": 1068,
          "total_tokens": 1119
      }
  }
  ```

  