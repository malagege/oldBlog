---
title: XSS 處理
date: 2022-07-31 00:47:32
tags: [xss]
categories: 源碼掃描
---

前端XSS 問題很難處理，雖然可能有做`htmlencode`相關處理，但是程式還是會被判有風險。這邊整理一下那些做法。

<!--more-->


## DOMPurify

想說 `OWASP` 搞出這個東西應該有什麼可以解決方案。最後找到[GitHub - ESAPI/owasp-esapi-js: An UNMAINTAINTED project originally exported from code.google.com/p/owasp-esapi-js. This project is deprecated. See the README.md for further details and possible alternatives.](https://github.com/ESAPI/owasp-esapi-js)以廢棄不使用。

DEPRECATED - OWASP Enterprise Security API for JavaScript (ESAPI-JS)

```
Potential Alternatives to ESAPI-JS (aka, ESAPI4JS)

    node-esapi - a minimal port of ESAPI-JS' output encoder that does not depend on bower and as of this writing (2021-03-30), has no known vulnerabilities. It does not include the validator or other portions of ESAPI-JS.
    DOMPurify - a DOM-only, XSS sanitizer for HTML, MathML, and SVG.
    Lots of additional alternatives if your project is not pure JavaScript.

```

所以使用DOMPurify。


[DOMPurify 2.3.6 "Beastmaster"](https://cure53.de/purify)線上可以簡單測試過濾結果。

```htmlembedded=
<tr><td>test</td></tr>
```
result:
```
test
```

```htmlembedded=
<table>
<tbody><tr><td>test</td></tr>
</tbody></table>
```

result:
```htmlembedded=
<table>
<tbody><tr><td>test</td></tr>
</tbody></table>
```

從上面得知

[GitHub - cure53/DOMPurify: DOMPurify - a DOM-only, super-fast, uber-tolerant XSS sanitizer for HTML, MathML and SVG. DOMPurify works with a secure default, but offers a lot of configurability and hooks. Demo:](https://github.com/cure53/DOMPurify)
[通过混淆命名空间绕过DOMPurify实现XSS - 安全客，安全资讯平台](https://www.anquanke.com/post/id/219089)(https://www.anquanke.com/post/id/219089)
[OWASP JavaScript Security | OWASP Foundation](https://owasp.org/www-project-javascript-security/)