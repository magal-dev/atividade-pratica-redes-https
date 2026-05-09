# Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo B (sem privilégio administrativo)

> **Como usar este template:** substitua os campos `[...]` pelas suas respostas,
> anexe as capturas de tela na pasta `evidencias/` e referencie-as onde indicado.
> Preserve a formatação markdown (tabelas, blocos de código) para facilitar a correção.
>
> **Observação:** toda a análise prática deste relatório é feita sobre tráfego **HTTP em texto claro**. A análise de HTTPS é teórica, baseada na fundamentação do `readme.md` do repositório.

---

## Identificação

| Campo       | Valor                  |
|-------------|------------------------|
| Nome        | [seu nome completo]    |
| RA          | [seu RA]               |
| Disciplina  | Redes de Computadores  |
| Turma       | [sua turma]            |
| Data        | [data da realização]   |
| Fluxo       | **B — Aluno sem privilégio de administrador** |
| SO utilizado | [Windows 11 / Ubuntu 22.04 / macOS ...] |
| Ferramenta de proxy | [Fiddler Classic per-user / mitmproxy / HTTP Toolkit / ...] |
| Navegador(es)       | [Chrome 124 / Firefox 125 / ...] |
| HTTPS-First Mode / HTTPS-Only desabilitado? | [sim / não] |

---

## Atividade 1 — Primeira captura (`http://example.com`)

**Captura de tela:** <img width="533" height="893" alt="image" src="https://github.com/user-attachments/assets/635ec154-dff8-45d5-a278-024f1c3ea5ef" />


**Request-line enviada:**

```http
[GET http://example.com/ HTTP/1.1]
```

**Status-line recebida:**

```http
[HTTP/1.1 304 Not Modified]
```

### Pergunta 1.1
> Quantos cabeçalhos o navegador enviou no request? Liste-os.

**Resposta:**
[4]

Cabeçalhos:
- [Cache]
- [Client]
- [Security]
- [Transport]

### Pergunta 1.2
> Qual foi o `Content-Length` da resposta? Se ele não apareceu, registre `Transfer-Encoding`, versão do protocolo ou outro indício observado. O corpo retornado é HTML, texto puro, JSON ou binário? Como você descobriu?

**Resposta:** [HTTP/1.1 304 Not Modified. Não aparece Content Type em lugar nenhum.]

---

## Atividade 2 — Anatomia de um GET (`http://httpbin.org/get?...`)

**Captura de tela:** <img width="427" height="344" alt="image" src="https://github.com/user-attachments/assets/d67c4a72-34b8-4d81-b047-fbe2cff636bc" />


**Request-line completa:**

```http
[GET http://httpbin.org/get?aluno=miguel_macedo&curso=redes HTTP/1.1]
```

**Cabeçalhos-chave capturados:**

| Cabeçalho    | Valor                    |
|--------------|--------------------------|
| `Host`       | [httpbin.org]                    |
| `User-Agent` | [Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36
]                    |
| `Accept`     | [text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
]                    |

**Campos do JSON de resposta:**

```json
{
  "args":    [aluno=miguel_macedo curso=redes],
  "headers": [Accept=text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7 Accept-Encoding=gzip, deflate Accept-Language=pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7 Host=httpbin.org Upgrade-Insecure-Requests=1 User-Agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36 X-Amzn-Trace-Id=Root=1-69ff353d-55c124cc4d3d594d2ffa9364],
  "origin":  [187.58.19.47]
}
```

### Pergunta 2.1
> O valor do campo `origin` corresponde a qual elemento da rede? Por que normalmente não é o IP local?

**Resposta:** [O servidor web.]

### Pergunta 2.2
> Compare o `User-Agent` enviado com o que aparece no JSON da resposta. Coincidem?

**Resposta:** [Sim]

### Pergunta 2.3
> Em `http://httpbin.org/headers`, liste até três cabeçalhos que o servidor vê mas **não aparecem** no Raw do request. De onde vêm? Se não encontrar três, explique por que o resultado pode variar.

**Resposta:**

| Cabeçalho visto pelo servidor | Origem provável | Observação |
|-------------------------------|-----------------|------------|
| [Miscellanous]                         | [Infraestrutura / Proxy]           | [Agrupa headers extras]      |
| [Entity]                         | [Cliente]           | [Descreve o conteúdo enviado]      |
| [Cache]                         | [Navegador / Servidor]           | [Controla a validade dos dados]      |

---

## Atividade 3 — POST e envio de formulário (`http://httpbin.org/forms/post` → `/post`)

**Captura de tela:** <img width="314" height="357" alt="image" src="https://github.com/user-attachments/assets/d4c9efc2-d978-4407-9ab8-789ebb7d542e" />


**Request-line do POST:**

```http
[POST http://httpbin.org/post HTTP/1.1]
```

**Cabeçalhos do request:**

| Cabeçalho        | Valor |
|------------------|-------|
| `Content-Type`   | [application/x-www-form-urlencoded] |
| `Content-Length` | [191] |

**Corpo completo do request:**

```
[custname=Isabella&custtel=13+92485289549&custemail=isabellacrazy%40gmail.com&size=medium&topping=bacon&topping=cheese&topping=onion&topping=mushroom&delivery=20%3A15&comments=do+lado+do+posto]
```

**Trecho do JSON de resposta (campo `form`):**

```json
"form": {
  "comments": "do lado do posto", 
    "custemail": "isabellacrazy@gmail.com", 
    "custname": "Isabella", 
    "custtel": "13 92485289549", 
    "delivery": "20:15", 
    "size": "medium", 
    "topping": [
      "bacon", 
      "cheese", 
      "onion", 
      "mushroom"

}
```

### Pergunta 3.1
> Qual o formato do corpo? Como esse formato codifica caracteres especiais (espaço, acentos)?

**Resposta:** [application/x-www-form-urlencoded]

### Pergunta 3.2
> Comparando **Request → WebForms** e **Request → Raw**: qual das duas corresponde literalmente aos bytes enviados no socket TCP?

**Resposta:** [Raw]

### Pergunta 3.3 — Composer
> Envie manualmente via Composer um `POST` para `http://httpbin.org/post` com JSON. Registre a resposta. Qual campo do JSON confirma que o servidor interpretou o JSON?

**Captura de tela:** <img width="578" height="383" alt="image" src="https://github.com/user-attachments/assets/b0c71c23-b831-44ce-b174-d26dd05977f6" />


**Response JSON (trecho relevante):**

```json
{
  ["form": {
    "comments": "do lado do posto", 
    "custemail": "isabellacrazy@gmail.com", 
    "custname": "Isabella", 
    "custtel": "13 92485289549", 
    "delivery": "20:15", 
    "size": "medium", 
    "topping": [
      "bacon", 
      "cheese", 
      "onion", 
      "mushroom"
    ]
  }, 
  "headers": {]
}
```

**Resposta:** [O form com os itens enviados]

---

## Atividade 4 — Catálogo de status codes (`http://httpbin.org/...`)

**Captura de tela (lista do Fiddler com as 7 sessões):** <img width="445" height="216" alt="image" src="https://github.com/user-attachments/assets/d224c90f-0e51-4fc8-a908-70fc5abb6fdd" />


| # | Método | URL | Status-line | `Content-Length` / `Transfer-Encoding` | Body presente? |
|---|--------|-----|-------------|-----------------------------------------|----------------|
| 1 | GET    | `http://httpbin.org/status/200` | [628] | [gzip, deflate] | [não] |
| 2 | GET    | `http://httpbin.org/redirect-to?status_code=301&url=/get` | [0] | [gzip, deflate] | [não] |
| 3 | GET    | `http://httpbin.org/status/404` | [0] | [gzip, deflate] | [não] |
| 4 | GET    | `http://httpbin.org/status/418` | [135] | [gzip, deflate] | [sim] |
| 5 | GET    | `http://httpbin.org/status/500` | [0] | [gzip, deflate] | [não] |
| 6 | GET    | `http://httpbin.org/status/503` | [0] | [gzip, deflate] | [não] |
| 7 | GET    | `http://httpbin.org/cache` com `If-Modified-Since` | [630] | [gzip, deflate] | [não] |

### Pergunta 4.1
> Em qual dos status o corpo está ausente/tamanho zero? Isso é obrigatório pela especificação ou depende do servidor?

**Resposta:** [Todos, obrigatório pela especificação]

### Pergunta 4.2
> No `301`, qual cabeçalho da resposta informa para onde ir? O que aconteceria se estivesse ausente?

**Resposta:** [Location, Se ele estivesse ausente, o navegador receberia a ordem de "mudar de endereço", mas não saberia para onde ir, resultando em uma página de erro ou em uma tela em branco.]

### Pergunta 4.3
> Diferença semântica entre `200`, `304` e `404` do ponto de vista do cache do navegador.

**Resposta:** [200 (OK): O servidor entrega o arquivo novo e completo. O navegador baixa tudo e salva no cache para usar no futuro. 304 (Not Modified): O servidor diz: "O arquivo que você já tem é igual ao meu". O navegador não baixa nada e usa a versão que já está no cache, economizando tempo e internet. 404 (Not Found): O arquivo não existe. O navegador não tem o que salvar no cache e, se tivesse uma versão antiga lá, ela se torna inválida.]

---

## Atividade 5 — Identificação de cabeçalhos (`http://httpbin.org/response-headers?...` + `/gzip`)

**Captura de tela (Inspectors → Headers):** <img width="541" height="546" alt="image" src="https://github.com/user-attachments/assets/41b27adb-3122-41c0-8eec-fa4616bead79" />


| Cabeçalho                    | Req/Resp | Valor capturado | Função em uma frase |
|------------------------------|----------|------------------|----------------------|
| `Host`                       | [Req]    | [httpbin.org]            | [...]                |
| `User-Agent`                 | [Req]    | [Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36]            | [...]                |
| `Accept`                     | [Req]    | [text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7]            | [...]                |
| `Accept-Encoding`            | [Req]    | [gzip, deflate]            | [...]                |
| `Cookie`                     | [Req]    | [teste=1]            | [...]                |
| `Server`                     | [Resp]    | [gunicorn/19.9.0]            | [...]                |
| `Content-Type`               | [Resp]    | [application/json]            | [...]                |
| `Content-Encoding`           | [Nenhum]    | [Nenhum]            | [...]                |
| `Set-Cookie`                 | [Resp]    | [teste=1]            | [...]                |
| `Cache-Control`              | [Resp]    | [max-age=3600]            | [...]                |
| `Strict-Transport-Security`  | Não esperado em HTTP — ver Pergunta 5.3 | — | — |

### Pergunta 5.1
> `Content-Encoding: gzip`/`br` apareceu? Compare `Content-Length`, quando presente, com o conteúdo visível. O que explica a diferença?

**Resposta:** [Não apareceu para mim. Content-Length: 133. O Content-Length reflete o tamanho do corpo da mensagem compactado (como ele viajou pela rede). O conteúdo que você vê nos Inspectors do Fiddler, após clicar para decodificar, é o texto descompactado. Por isso, o conteúdo visível parece ser muito maior do que o número indicado no cabeçalho.]

### Pergunta 5.2
> Cliente envia `Accept: application/json` mas o recurso só existe em `text/html`. Qual status code esperar?

**Resposta:** [O status code esperado é o 406 Not Acceptable.]

### Pergunta 5.3
> `Strict-Transport-Security` apareceu nas respostas HTTP? Por que esse cabeçalho está ausente neste fluxo? (Consulte a RFC 6797.) Qual é seu papel contra downgrades para HTTP puro?

**Resposta:** [O cabeçalho Strict-Transport-Security (HSTS) só deve ser processado se a conexão for HTTPS.]

---

## Atividade 6 — HTTP vs HTTPS (análise sem decriptação)

**Captura de tela HTTP (`neverssl.com`):<img width="536" height="854" alt="image" src="https://github.com/user-attachments/assets/9e0a4e5d-bfd2-480c-89a5-5a4aafd03f60" />

**Captura de tela HTTPS (`https://httpbin.org/get`, apenas CONNECT):** <img width="535" height="811" alt="image" src="https://github.com/user-attachments/assets/d50a23ee-274d-4071-9907-ae7821fcf667" />


### Pergunta 6.1
> Que método HTTP aparece na sessão do `https://httpbin.org/get`? O que ele faz e por que existe?

**Resposta:** [Connect, Cria um túnel direto entre o seu computador e o servidor de destino, geralmente através de um servidor proxy.]

### Pergunta 6.2
> Tabela comparativa dos campos visíveis ao Fiddler em cada caso:

| Campo                          | Visível em HTTP? | Visível em HTTPS (sem decriptação)? |
|--------------------------------|------------------|-------------------------------------|
| Método                         | [Sim]            | [Sim]                               |
| URL completa (path + query)    | [Sim]            | [Nao]                               |
| Cabeçalhos de request          | [Sim]            | [Nâo]                               |
| Corpo de request               | [Nâo]            | [Nâo]                               |
| Status code                    | [Sim]            | [Sim]                               |
| Cabeçalhos de response         | [Sim]            | [Sim]                               |
| Corpo de response              | [Sim]            | [Sim]                               |
| Host (via SNI, no `CONNECT`)   | [Sim]            | [Sim]                               |
| IP e porta de destino          | [Não]            | [Sim]                               |

### Pergunta 6.3 (teórica)
> O que você **veria** no Fiddler se tivesse privilégio de administrador e pudesse habilitar *Decrypt HTTPS traffic*? Indique telas/abas e justifique por que essa inspeção exige a instalação de um certificado raiz.

**Resposta:** [Telas/Abas: Você conseguiria usar as abas JSON, XML, WebForms e TextView para ler o conteúdo das mensagens (corpo e cabeçalhos) que antes estavam escondidos.
Justificativa do Certificado Raiz: A inspeção exige um certificado raiz porque o HTTPS foi feito para impedir que qualquer intermediário leia os dados. O Fiddler atua como um "homem no meio" (Man-in-the-Middle); ele cria um certificado falso para o site que você quer visitar. Sem o certificado raiz do Fiddler instalado e confiado no seu sistema, o navegador bloquearia a conexão por segurança, avisando que o certificado é inválido.]

### Pergunta 6.4
> Por que a técnica de decriptação dos *debugging proxies* **não** funcionaria contra um usuário se um atacante a tentasse sem instalar o certificado?

**Resposta:** [A técnica não funcionaria porque o navegador do usuário verificaria a Cadeia de Confiança.]

---

## Atividade 7 — Cookies e sessão (`http://httpbin.org/cookies/...`)

**Captura de tela da sequência:** `evidencias/atv7_cookies.png`

| # | URL | `Set-Cookie` recebido | `Cookie` enviado |
|---|-----|-----------------------|-------------------|
| 1 | `/cookies/set?...`       | [...] | [nenhum / ...] |
| 2 | `/cookies` (1ª visita)   | [...] | [...]          |
| 3 | `/cookies` (reload 1)    | [...] | [...]          |
| 4 | `/cookies` (reload 2)    | [...] | [...]          |

### Pergunta 7.1
> `Set-Cookie` aparece uma vez ou em toda requisição? Justifique.

**Resposta:** [...]

### Pergunta 7.2
> Que atributos o `Set-Cookie` trouxe? Explique cada um presente. Para atributos não observados, registre `não observado`.

> **Nota:** o httpbin define cookies mínimos — apenas o atributo `Path=/` estará presente. Para cada atributo ausente, registre **não observado** e explique o comportamento padrão do navegador na sua ausência (ex.: sem `Expires`/`Max-Age` → cookie de sessão; sem `Secure` → pode ser enviado por HTTP; sem `SameSite` → o navegador aplica a política padrão da versão em uso).

**Resposta:**

| Atributo  | Valor | Função | Observado? |
|-----------|-------|--------|------------|
| `Path`    | `/`   | [...]  | Sim        |
| `Domain`  | —     | [...]  | não observado |
| `Expires` | —     | [...]  | não observado |
| `Max-Age` | —     | [...]  | não observado |
| `Secure`  | —     | [...]  | não observado |
| `HttpOnly`| —     | [...]  | não observado |
| `SameSite`| —     | [...]  | não observado |

### Pergunta 7.3
> O atributo `Secure` pode aparecer num cookie recebido por HTTP puro? Qual seria o comportamento esperado? Relacione com o fato de que todo o tráfego desta atividade é visível em texto claro.

**Resposta:** [...]

### Pergunta 7.4
> Na aba **Inspectors → Cookies**, o cookie armazenado coincide com o campo `cookies` do JSON?

**Resposta:** [...]

---

## Atividade 8 — Manipulação com breakpoints

> **Atividade exclusiva do Fiddler Classic.** Se você utilizou mitmproxy ou HTTP Toolkit, responda às questões 8.1 e 8.2 de forma teórica (sem capturas de tela), indicando que a ferramenta utilizada não suporta breakpoints interativos.

**Captura de tela da edição do User-Agent:** `evidencias/atv8_ua_edit.png`

**JSON de resposta após edição:**

```json
{
  "user-agent": "[valor forjado]"
}
```

### Pergunta 8.1
> O servidor pode detectar que o `User-Agent` foi forjado? Discuta.

**Resposta:** [...]

### Pergunta 8.2
> Após editar a status-line de `200 OK` para `404 Not Found`, o que o navegador exibe? Comente o papel do proxy como MITM.

**Captura de tela:** `evidencias/atv8_status_edit.png`

**Resposta:** [...]

### Pergunta 8.3
> Confirme que todos os breakpoints foram desabilitados.

- [ ] Breakpoints desabilitados ao final (Shift+F11)

---

## Atividade 9 — Redirecionamento HTTP → HTTPS

**Captura de tela:** `evidencias/atv9_redir.png`

**Status-line da resposta a `http://httpbin.org/redirect-to?status_code=301&url=https%3A%2F%2Fhttpbin.org%2Fget`:**

```http
[colar aqui, ex: HTTP/1.1 301 Moved Permanently]
```

**Cabeçalho `Location` da resposta:**

```
Location: [colar aqui]
```

### Pergunta 9.1
> Código de status e cabeçalho que direcionaram o navegador para `https://`.

**Resposta:** [...]

### Pergunta 9.2
> Além do redirecionamento 3xx, qual outro mecanismo/cabeçalho faz o navegador passar a forçar HTTPS em visitas futuras? Cite a RFC.

**Resposta:** [...]

### Pergunta 9.3
> Se esse cabeçalho fosse enviado por uma resposta servida via HTTP puro, o navegador deveria obedecer? Justifique com base na RFC.

**Resposta:** [...]



### 7. Impacto prático de `Cache-Control: no-store`.

[resposta]

### 8. Como um debugging proxy decifra HTTPS sem violar a criptografia, e por que isso exige cooperação do usuário (e por que, justamente, você não pôde executar essa etapa)?

[resposta]

### 9. Exemplo de cabeçalho de request que o navegador envia automaticamente, sem a página pedir.

[resposta]

### 10. Se fosse automatizar a inspeção via script, qual ferramenta alternativa escolheria? Por quê?

[resposta]

### 11. (Exclusiva do Fluxo B) Três cabeçalhos de segurança que não aparecem ou não fazem sentido em respostas HTTP puro. Para cada um, o que aconteceria se enviado por um servidor HTTP? (Cite RFC 6797 para HSTS.)

**Resposta:**

| Cabeçalho | Comportamento esperado sobre HTTP | Referência |
|-----------|-----------------------------------|-----------|
| [...]     | [...]                             | [...]     |
| [...]     | [...]                             | [...]     |
| [...]     | [...]                             | [...]     |

---

## Reflexão final (opcional, até 10 linhas)

> O que você aprendeu que não conhecia antes deste laboratório? Há algum
> cabeçalho, código de status ou comportamento que passou a olhar com
> mais atenção? Alguma dificuldade que recomendaria evitar para a próxima turma?

[reflexão]

---

## Encerramento — justificativa de segurança (Fluxo B)

**Parágrafo: por que a remoção de certificado é dispensável neste fluxo e por que seria obrigatória para o aluno administrador:**

[redigir, em até 5 linhas, com base na seção 4.6 do readme.md]

- [ ] HTTPS-First Mode / HTTPS-Only Mode reabilitado no navegador
- [ ] Fiddler / mitmproxy / HTTP Toolkit fechado (porta de proxy liberada)
- [ ] Configuração de proxy removida do navegador (se aplicável)

---

## Checklist de entrega

- [ ] Todos os campos `[...]` substituídos
- [ ] Pasta `evidencias/` com capturas nomeadas por atividade (incluindo Atv. 9)
- [ ] 11 questões de verificação respondidas
- [ ] Atividade 9 (redirecionamento HTTP→HTTPS) documentada
- [ ] Justificativa de encerramento redigida
- [ ] Arquivo compactado como `NOME_RA_LAB_HTTP_FLUXOB.zip`
- [ ] Submetido no Microsoft Teams dentro do prazo
