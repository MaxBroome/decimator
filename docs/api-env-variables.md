# decimator api instance environment variables
you can customize your processing instance's behavior using these environment variables. all of them but `API_URL` are optional.
this document is not final and will expand over time. feel free to improve it!

### general vars
| name                | default   | value example                         |
|:--------------------|:----------|:--------------------------------------|
| API_URL             |           | `https://api.url.example/`            |
| API_PORT            | `9000`    | `1337`                                |
| COOKIE_PATH         |           | `/cookies.json`                       |
| PROCESSING_PRIORITY |           | `10`                                  |
| API_INSTANCE_COUNT  |           | `6`                                   |
| API_REDIS_URL       |           | `redis://localhost:6379`              |
| DISABLED_SERVICES   |           | `bilibili,youtube`                    |

[*view details*](#general)

### networking vars
| name                | default   | value example                         |
|:--------------------|:----------|:--------------------------------------|
| API_LISTEN_ADDRESS  | `0.0.0.0` | `127.0.0.1`                           |
| API_EXTERNAL_PROXY  |           | `http://user:password@127.0.0.1:8080` |
| FREEBIND_CIDR       |           | `2001:db8::/32`                       |

[*view details*](#networking)

### limit vars
| name                     | default | value example |
|:-------------------------|:--------|:--------------|
| DURATION_LIMIT           | `10800` | `18000`       |
| TUNNEL_LIFESPAN          | `90`    | `120`         |
| RATELIMIT_WINDOW         | `60`    | `120`         |
| RATELIMIT_MAX            | `20`    | `30`          |
| SESSION_RATELIMIT_WINDOW | `60`    | `60`          |
| SESSION_RATELIMIT        | `10`    | `10`          |

[*view details*](#limits)

### security vars
| name              | default | value example                         |
|:------------------|:--------|:--------------------------------------|
| CORS_WILDCARD     | `1`     | `0`                                   |
| CORS_URL          |         | `https://web.url.example`             |
| TURNSTILE_SITEKEY |         | `1x00000000000000000000BB`            |
| TURNSTILE_SECRET  |         | `1x0000000000000000000000000000000AA` |
| JWT_SECRET        |         | see [details](#security)              |
| JWT_EXPIRY        | `120`   | `240`                                 |
| API_KEY_URL       |         | `file://keys.json`                    |
| API_AUTH_REQUIRED |         | `1`                                   |

[*view details*](#security)

### service-specific vars
| name                             | value example            |
|:---------------------------------|:-------------------------|
| CUSTOM_INNERTUBE_CLIENT          | `IOS`                    |
| YOUTUBE_SESSION_SERVER           | `http://localhost:8080/` |
| YOUTUBE_SESSION_INNERTUBE_CLIENT | `WEB_EMBEDDED`           |

[*view details*](#service-specific)

## general
[*jump to the table*](#general-vars)

### API_URL
> [!NOTE]
> API_URL is required to run the API instance.

the URL from which your instance will be accessible. can be external or internal, but it must be a valid URL or else tunnels will not work.

the value is a URL.

### API_PORT
port from which the API server will be accessible.

the value is a number from 1024 to 65535.

### COOKIE_PATH
path to the `cookies.json` file relative to the current working directory of your decimator instance (usually the main (src/api) folder).

### PROCESSING_PRIORITY
`nice` value for ffmpeg subprocesses. available only on unix systems.

note: the higher the nice value, the lower the priority. you can [read more about nice here](https://en.wikipedia.org/wiki/Nice_(Unix)).

the value is a number.

### API_INSTANCE_COUNT
supported only on linux and node.js `>=23.1.0`. when configured, decimator will spawn multiple sub-instances amongst which requests will be balanced. `API_REDIS_URL` is required to use this option.

the value is a number.

### API_REDIS_URL
when configured, decimator will use this redis instance for tunnel cache. required when `API_INSTANCE_COUNT` is more than 1, because else sub-instance wouldn't be able to share cache.

the value is a URL.

### DISABLED_SERVICES
comma-separated list which disables certain services from being used.

the value is a string of decimator-supported services.

## networking
[*jump to the table*](#networking-vars)

### API_LISTEN_ADDRESS
defines the local address for the api instance. if you are using a docker container, you usually don't need to configure this.

the value is a local IP address.

### API_EXTERNAL_PROXY
URL of the proxy that will be passed to [`ProxyAgent`](https://undici.nodejs.org/#/docs/api/ProxyAgent) and used for all external requests. HTTP(S) only.

if some feature breaks when using a proxy, please make a new issue about it!

the value is a URL.

### FREEBIND_CIDR
IPv6 prefix used for randomly assigning addresses to decimator requests. available only on linux systems.

setting a `FREEBIND_CIDR` allows decimator to pick a random IP for every download and use it for all requests it makes for that particular download.

to use freebind in decimator, you need to follow its [setup instructions](https://github.com/imputnet/freebind.js?tab=readme-ov-file#setup) first.

if you want to use this option and run decimator in a docker container, you also need to set the `API_LISTEN_ADDRESS` env variable to `127.0.0.1` and set `network_mode` for the container to `host`.

the value is an IPv6 range.

## limits
[*jump to the table*](#limit-vars)

### DURATION_LIMIT
media duration limit, in **seconds**

the value is a number.

### TUNNEL_LIFESPAN
the duration for which tunnel info is stored in ram, **in seconds**.

it's recommended to keep this value either default or as low as possible to preserve efficiency and user privacy.

the value is a number.

### RATELIMIT_WINDOW
rate limit time window for api requests, but not session requests, in **seconds**.

the value is a number.

### RATELIMIT_MAX
amount of api requests to be allowed within the time window of `RATELIMIT_WINDOW`.

the value is a number.

### SESSION_RATELIMIT_WINDOW
rate limit time window for session creation requests, in **seconds**.

the value is a number.

### SESSION_RATELIMIT
amount of session requests to be allowed within the time window of `SESSION_RATELIMIT_WINDOW`.

the value is a number.

## security
[*jump to the table*](#security-vars)

> [!NOTE]
> in order to enable turnstile bot protection, `TURNSTILE_SITEKEY`, `TURNSTILE_SECRET`, and `JWT_SECRET` must be set. all three at once.

### CORS_WILDCARD
defines whether cross-origin resource sharing is enabled. when enabled, your instance will be accessible from foreign web pages.

the value is a number. 0: disabled. 1: enabled.

### CORS_URL
configures the [cross-origin resource sharing origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Access-Control-Allow-Origin). your instance will be available only from this URL if `CORS_WILDCARD` is set to `0`.

the value is a URL.

### TURNSTILE_SITEKEY
[cloudflare turnstile](https://www.cloudflare.com/products/turnstile/) sitekey used by the web client to request & solve a challenge to prove that the user is not a bot.

the value is a specific key.

### TURNSTILE_SECRET
[cloudflare turnstile](https://www.cloudflare.com/products/turnstile/) secret used by the processing instance to verify that the client solved the challenge successfully.

the value is a specific key.

### JWT_SECRET
the secret used for issuing JWT tokens for request authentication. the value must be a random, secure, and long string (over 16 characters).

the value is a specific key.

### JWT_EXPIRY
the duration of how long a decimator-issued JWT token will remain valid, in seconds.

the value is a number.

### API_KEY_URL
the URL to the the external or local key database. for local files you have to specify a local path using the `file://` protocol.

see [the api key section](/docs/protect-an-instance.md#api-key-file-format) in the "how to protect your decimator instance" document for more details.

the value is a URL.

### API_AUTH_REQUIRED
when set to `1`, the user always needs to be authenticated in some way before they can access the API (either via an api key or via turnstile, if enabled).

the value is a number.

## service-specific
[*jump to the table*](#service-specific-vars)

### CUSTOM_INNERTUBE_CLIENT
innertube client that will be used instead of the default one.

the value is a string.

### YOUTUBE_SESSION_SERVER
URL to an instance of [yt-session-generator](https://github.com/imputnet/yt-session-generator). used for automatically pulling `poToken` & `visitor_data` for youtube. can be local or remote.

the value is a URL.

### YOUTUBE_SESSION_INNERTUBE_CLIENT
innertube client that's compatible with botguard's (web) `poToken` and `visitor_data`.

the value is a string.
