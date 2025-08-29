## 为什么

The Gemini API is [free](https://ai.google.dev/pricing "limits applied!"),
但是有很多工具专门与OpenAI API一起使用。

这个项目提供了一个个人的、兼容OpenAI的免费端点。


## 无服务器？

虽然它在云端运行，但不需要服务器维护。
它可以轻松部署到各种提供商上并且免费
（带有适合个人使用的慷慨限制）。

> [!提示]
> 也可以在本地运行代理端点，
> 但它更适合于开发使用。


## 如何开始

您需要一个个人 Google [API 密钥](https://makersuite.google.com/app/apikey).

> [!重要]
> 即使您位于 [支持的地区](https://ai.google.dev/gemini-api/docs/available-regions#available_regions)之外，
> 仍然可以使用VPN获取。

按照以下说明将项目部署到其中一个提供商。
您需要在那里设置一个账户。

如果您选择“按钮部署”，系统将引导您首先克隆仓库。
这是持续集成（CI）所必需的。


### Deploy with Vercel

 [![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/PublicAffairs/openai-gemini&repository-name=my-openai-gemini)
- Alternatively can be deployed with [cli](https://vercel.com/docs/cli):
  `vercel deploy`
- Serve locally: `vercel dev`
- Vercel _Functions_ [limitations](https://vercel.com/docs/functions/limitations) (with _Edge_ runtime)


### Deploy to Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/PublicAffairs/openai-gemini&integrationName=integrationName&integrationSlug=integrationSlug&integrationDescription=integrationDescription)
- Alternatively can be deployed with [cli](https://docs.netlify.com/cli/get-started/):
  `netlify deploy`
- Serve locally: `netlify dev`
- Two different api bases provided:
  - `/v1` (e.g. `/v1/chat/completions` endpoint)  
    _Functions_ [limits](https://docs.netlify.com/functions/get-started/?fn-language=js#synchronous-function-2)
  - `/edge/v1`  
    _Edge functions_ [limits](https://docs.netlify.com/edge-functions/limits/)


### Deploy to Cloudflare

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/PublicAffairs/openai-gemini)
- Alternatively can be deployed manually pasting content of [`src/worker.mjs`](src/worker.mjs)
  to https://workers.cloudflare.com/playground (see there `Deploy` button).
- 或者可以部署为 [cli](https://developers.cloudflare.com/workers/wrangler/):
  `wrangler 部署`
- 本地服务: `wrangler dev`
- _工人_ [限制](https://developers.cloudflare.com/workers/platform/limits/#worker-limits)


### Deploy to Deno

See details [here](https://github.com/PublicAffairs/openai-gemini/discussions/19).


### Serve locally - with Node, Deno, Bun

Only for Node: `npm install`.

Then `npm run start` / `npm run start:deno` / `npm run start:bun`.


#### Dev mode (watch source changes)

Only for Node: `npm install --include=dev`

Then: `npm run dev` / `npm run dev:deno` / `npm run dev:bun`.


## How to use
If you open your newly-deployed site in a browser, you will only see a `404 Not Found` message. This is expected, as the API is not designed for direct browser access.
To utilize it, you should enter your API address and your Gemini API key into the corresponding fields in your software settings.

> [!NOTE]
> Not all software tools allow overriding the OpenAI endpoint, but many do
> (however these settings can sometimes be deeply hidden).

Typically, you should specify the API base in this format:  
`https://my-super-proxy.vercel.app/v1`

The relevant field may be labeled as "_OpenAI proxy_".
You might need to look under "_Advanced settings_" or similar sections.
Alternatively, it could be in some config file (check the relevant documentation for details).

For some command-line tools, you may need to set an environment variable, _e.g._:
```sh
OPENAI_BASE_URL="https://my-super-proxy.vercel.app/v1"
```
_..or_:
```sh
OPENAI_API_BASE="https://my-super-proxy.vercel.app/v1"
```


## Models

Requests use the specified [model] if its name starts with "gemini-", "gemma-", "learnlm-", 
or "models/". Otherwise, these defaults apply:

- `chat/completions`: `gemini-2.5-flash`
- `embeddings`: `gemini-embedding-001`

[model]: https://ai.google.dev/gemini-api/docs/models#model-variations


## Built-in tools

To use the **web search** tool, append ":search" to the model name
(e.g., "gemini-2.5-flash:search").

Note: The `annotations` message property is not implemented.


## Media

[Vision] and [audio] input supported as per OpenAI [specs].
Implemented via [`inlineData`](https://ai.google.dev/api/caching#Part).

[vision]: https://platform.openai.com/docs/guides/images-vision?api-mode=chat&format=url#giving-a-model-images-as-input
[audio]: https://platform.openai.com/docs/guides/audio?example=audio-in&lang=curl#add-audio-to-your-existing-application
[specs]: https://platform.openai.com/docs/api-reference/chat/create


## Gemini-specific functions

There are several features supported by Gemini that are not available in OpenAI models
but can be enabled using the `extra_body` field.
The most notable of these is [`thinking_config`](https://ai.google.dev/gemini-api/docs/openai#thinking).

For more details, refer to the [Gemini API docs](https://ai.google.dev/gemini-api/docs/openai#extra-body).

---

## Supported API endpoints and applicable parameters

- [x] `chat/completions`

  Currently, most of the parameters that are applicable to both APIs have been implemented.
  <details>

  - [x] `messages`
      - [x] `content`
      - [x] `role`
          - [x] "system" (=>`system_instruction`)
          - [x] "user"
          - [x] "assistant"
          - [x] "tool"
      - [ ] `name`
      - [x] `tool_calls`
  - [x] `model`
  - [x] `frequency_penalty`
  - [ ] `logit_bias`
  - [ ] `logprobs`
  - [ ] `top_logprobs`
  - [x] `max_tokens`, `max_completion_tokens`
  - [x] `n` (`candidateCount` <8, not for streaming)
  - [x] `presence_penalty`
  - [x] `reasoning_effort`
  - [x] `response_format`
      - [x] "json_object"
      - [x] "json_schema" (a select subset of an OpenAPI 3.0 schema object)
      - [x] "text"
  - [x] `seed`
  - [x] `stop`: string|array (`stopSequences` [1,5])
  - [x] `stream`
  - [x] `stream_options`
      - [x] `include_usage`
  - [x] `temperature` (0.0..2.0 for OpenAI, but Gemini supports up to infinity)
  - [x] `top_p`
  - [x] `tools`
  - [x] `tool_choice`
  - [ ] `parallel_tool_calls` (is always active in Gemini)
  - [x] [`extra_body`](#gemini-specific-functions)

  </details>
- [ ] `completions`
- [x] `embeddings`
  - [x] `dimensions`
- [x] `models`
Commit changes
