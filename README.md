# Contador de Folhas — Logística

Aplicação de telemóvel/tablet para a logística da Hinojosa Graphicsleader, com:

- **Contar** — número de folhas de cartolina numa palete a partir da altura da pilha.
- **Kg ⇄ Folhas** — converte folhas em quilos e quilos em folhas.
- **Inventário** — leitura por câmara dos códigos QR das etiquetas SSCC para inventário físico ao armazém, comparação com um export do SAP (MIGO) e geração do ficheiro de correções.
- **Incidências** — registo de incidências logísticas (paletes/bobines danificadas, avarias, etc.) com formulário configurável e numeração automática (ano.semana.nº).
- **Definições** (só Supervisor) — códigos de acesso, responsável pela base de dados, pedidos dos operadores, notificações por email, formulário de incidências.

A app pede um código de acesso ao abrir, com dois perfis: **Operador** e **Supervisor**.

Aplicação original desenvolvida por David Malheiro (artefacto Claude); esta versão foi adaptada para alojamento estático, sem precisar de conta Claude — qualquer pessoa com o link pode abrir e usar.

## Estrutura

```
index.html              Aplicação (HTML/CSS/JS num só ficheiro)
config.js               Email automático (EmailJS) e responsável por defeito
js/db-local.js          Substitui a base de dados do Claude por armazenamento no browser
img/hinojosa.png        Logótipo
img/logistica.jpg       Imagem da página inicial
data/cartolinas.json    Base de dados inicial de cartolinas
.nojekyll                Diz ao GitHub Pages para servir os ficheiros tal como estão
```

## Publicar no GitHub Pages

1. Criar um repositório novo (ex.: `contador-de-folhas-logistica`).
2. Carregar todos os ficheiros e pastas desta pasta para a raiz do repositório (*Add file → Upload files*, mantendo a estrutura de pastas `js/`, `img/` e `data/`).
3. Em *Settings → Pages*, escolher *Deploy from a branch*, branch `main`, pasta `/ (root)`, e guardar.
4. Ao fim de 1–2 minutos a app fica disponível em `https://<utilizador>.github.io/<repositório>/`.

## Códigos de acesso

Por defeito: **Operador = 1234**, **Supervisor = 4321**. Mude-os assim que testar, em
**Definições → Códigos de acesso** (só visível depois de entrar como Supervisor).

## Diferenças importantes face à versão no Claude

- **Os dados ficam no browser de cada aparelho** (localStorage), sem partilha entre
  dispositivos nem com a versão Claude. Um telemóvel que faça uma contagem de inventário
  não aparece no computador do responsável, e vice-versa — isto serve para **testar a
  aplicação**, não para uso real em equipa. Para utilização real da logística (vários
  telemóveis a atualizar o mesmo inventário ao mesmo tempo, notificações cruzadas, etc.)
  é preciso a versão Claude, com base de dados partilhada.
- **As fotos das incidências ficam guardadas só neste aparelho** — como não há aqui um
  servidor próprio para receber ficheiros, cada foto é reduzida (até 1000px, qualidade
  comprimida) e guardada dentro do próprio registo da incidência, no browser deste
  dispositivo. Isto chega para testar, mas não substitui um armazenamento real de ficheiros:
  evite anexar muitas fotos grandes, porque o browser tem um limite de espaço (alguns MB) para
  guardar dados, e as fotos não ficam visíveis a partir de outro aparelho.
- **O envio automático de email funciona** (ao contrário da versão Claude, onde está
  bloqueado) — usa o serviço configurado em `config.js`.
- Cada aparelho arranca com a lista de cartolinas de `data/cartolinas.json`; se precisar de
  atualizar essa lista, pode fazê-lo pela própria app (Base de dados → Importar nova versão
  do Excel), que fica guardada só nesse aparelho.
