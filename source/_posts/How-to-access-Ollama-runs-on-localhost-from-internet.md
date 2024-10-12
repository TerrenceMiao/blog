---
title: How to access Ollama runs on localhost from internet
date: 2024-10-12 22:53:37
tags:
---

By default, `Ollama` is only accessible from **localhost**. CORS error and HTTP 403 returned if try to invoke from other hosts.

To enable `Ollama` can be visited from hosts on intranet, run:

```
$ launchctl setenv OLLAMA_HOST "0.0.0.0"
```

in MacOS. Then restart `Ollama`. Then invoke it from hosts on intranet.

To enable `Ollama` can be visited from internet, with the help from `ngrok`, run:

```
$ ngrok http 11434

ngrok                                                     (Ctrl+C to quit)

Session Status  online
Account         Terrence Miao (Plan: Free)
Version         3.17.0
Region          Australia (au)
Web Interface   http://127.0.0.1:4040
Forwarding      https://294b-2403-5802-1c44-0-341c-aae8-a501-8b73.ngrok-free.app -> http://localhost:11434

Connections     ttl     opn     rt1     rt5     p50     p90
                0       0       0.00    0.00    0.00    0.00
```

Then can visit `Ollama` on **localhost** from internet. `11434` is default port of `Ollama`.

```
$ curl --location 'https://294b-2403-5802-1c44-0-341c-aae8-a501-8b73.ngrok-free.app/v1/models'

{
    "object": "list",
    "data": [
        {
            "id": "codellama:latest",
            "object": "model",
            "created": 1728736149,
            "owned_by": "library"
        },
        {
            "id": "deepseek-coder-v2:latest",
            "object": "model",
            "created": 1728735883,
            "owned_by": "library"
        },
        {
            "id": "mxbai-embed-large:latest",
            "object": "model",
            "created": 1713401203,
            "owned_by": "library"
        },
        {
            "id": "nomic-embed-text:latest",
            "object": "model",
            "created": 1708780687,
            "owned_by": "library"
        }
    ]
}
```

To write some code:

```
$ curl --location 'https://294b-2403-5802-1c44-0-341c-aae8-a501-8b73.ngrok-free.app/v1/chat/completions' \
--header 'Content-Type: application/json' \
--data '{
    "model": "deepseek-coder-v2",
    "messages": [
        {
            "role": "system",
            "content": "You are a helpful assistant."
        },
        {
            "role": "user",
            "content": "Write a function that outputs the fibonacci sequence in TypeScript."
        }
    ]
}'

{
  "id": "chatcmpl-174",
  "object": "chat.completion",
  "created": 1728738926,
  "model": "deepseek-coder-v2",
  "system_fingerprint": "fp_ollama",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "
        Certainly! Below is a TypeScript function that outputs the Fibonacci sequence up to a specified number of terms:

        This function starts with the initial terms of the Fibonacci sequence `[0, 1]` and then iteratively calculates each subsequent term
        by summing the two preceding terms. The function returns an array containing the first `n` terms of the Fibonacci sequence.
        "
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 29,
    "completion_tokens": 243,
    "total_tokens": 272
  }
}
```

```typescript\n
function fibonacciSequence(n: number): number[] {
  if (n <= 0) return [];
  if (n === 1) return [0];

  const result: number[] = [0, 1];

  for (let i = 2; i < n; i++) {
    result.push(result[i - 1] + result[i - 2]);
  }

  return result;
}

// Example usage: console.log(fibonacciSequence(10));
// Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

Check `Ollama` log:

```
$ tail -f ~/.ollama/logs/server.log
```

References
----------

- Ollama FAQ, _https://github.com/ollama/ollama/blob/main/docs/faq.md_
