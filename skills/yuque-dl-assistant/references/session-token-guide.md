# Session and Token Guide for yuque-dl

## 1) Standard Yuque (`yuque.com`) session token

1. Log in to Yuque in a desktop browser.
2. Open DevTools (`F12` on Windows, `Option+Command+J` on macOS).
3. Go to `Application`.
4. Go to `Cookies` and choose `https://www.yuque.com`.
5. Find cookie name `_yuque_session`.
6. Copy the `Value` field.

Use with CLI:

```bash
yuque-dl "<url>" -t "<value>"
```

## 2) Enterprise Yuque domains

For domains like `https://yuque.antfin.com/...`, cookie key may not be `_yuque_session`.
Ask user to inspect browser cookies and provide both:

- cookie key (for `-k`)
- cookie value (for `-t`)

Use with CLI:

```bash
yuque-dl "<url>" -k "<cookie_key>" -t "<cookie_value>"
```

## 3) Password-protected public libraries

Two common cases:

- User is logged in: usually use `_yuque_session`.
- User is not logged in: may require `verified_books` or `verified_docs`.

## 4) Common error handling

### Token parsed as option

If token begins with `-`, use:

```bash
yuque-dl "<url>" -t="<token>"
```

### Attachment download fails on public library

Even public content may require a logged-in token for attachment files.
Ask user for login token and retry.

## 5) Safety reminder

Cookie/token represents login session and must be treated as secret.
Never ask users to post tokens in public channels.

## 6) Suggested clarification prompts

- `这是私有库还是公开库？`
- `是否需要下载附件？`
- `你当前是否已登录语雀账号？`
- `如果是企业语雀，请提供 cookie 的 key 和 value。`
