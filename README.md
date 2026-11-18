# Contador de Folhas — Logística

Aplicação de telemóvel/tablet para a logística da Hinojosa Graphicsleader, com:

- **Contar** — número de folhas de cartolina numa palete a partir da altura da pilha.
- **Kg ⇄ Folhas** — converte folhas em quilos e quilos em folhas.
- **Inventário** — leitura por câmara do código da etiqueta SSCC (código de barras linear
  GS1-128 ou QR) para inventário físico ao armazém, com contador de leituras OK/falhadas e
  semáforo da última leitura, comparação com um export do SAP (MIGO) e geração do ficheiro
  de correções.
- **Incidências** — registo de incidências logísticas (paletes/bobines danificadas, avarias,
  etc.) com formulário configurável, leitura de SSCC, foto opcional, e numeração automática
  (ano.semana.nº).
- **Definições** (só Supervisor) — códigos de acesso, responsável pela base de dados, pedidos
  dos operadores, notificações por email, formulário de incidências.

A app pede um código de acesso ao abrir, com dois perfis: **Operador** e **Supervisor**.
Em ecrã pequeno (telemóvel), use o botão ☰ no canto superior esquerdo para mudar de separador.

Aplicação original desenvolvida por David Malheiro (artefacto Claude); esta versão foi
adaptada para alojamento estático, sem precisar de conta Claude.

## Esta é a versão de ficheiro único (recomendada)

**Esta pasta tem um único ficheiro, `index.html`** — tudo o resto (base de dados local, base
de dados inicial de cartolinas, leitura de código de barras/QR) está embutido dentro dele.
Não há pastas `js/`, `img/` ou `data/` para esta versão: isto elimina por completo os
problemas que acontecem quando o GitHub não preserva a estrutura de pastas ao carregar
vários ficheiros de uma vez (foi essa a causa do "sem acesso à base de dados" na versão
anterior, em várias pastas). **Use sempre esta versão**, a não ser que precise mesmo de
editar o email automático sem tocar no `index.html` (nesse caso use a versão em
`github-bundle/`, com `config.js` separado).

## Publicar no GitHub Pages

1. Criar um repositório novo (ex.: `contador-de-folhas-logistica`).
2. Carregar o ficheiro `index.html` (e o `.nojekyll`, opcional) para a **raiz** do repositório
   — é só um ficheiro, não há pastas a preservar.
3. Em *Settings → Pages*, escolher *Deploy from a branch*, branch `main`, pasta `/ (root)`, e
   guardar.
4. Ao fim de 1–2 minutos a app fica disponível em `https://<utilizador>.github.io/<repositório>/`.

## Códigos de acesso

Por defeito: **Operador = 1234**, **Supervisor = 4321**. Mude-os assim que testar, em
**Definições → Códigos de acesso** (só visível depois de entrar como Supervisor).

## Já vem pronta a testar

- **Contar / Kg ⇄ Folhas** — já tem a base de dados de cartolinas embutida (1103 linhas),
  por isso já dá para escolher fornecedor + gramagem e calcular a palete sem preparar nada.
- **Inventário** — tire uma foto a qualquer etiqueta com código de barras (Code128/GS1-128)
  ou QR para testar a leitura; o semáforo fica verde numa leitura bem-sucedida e vermelho
  numa falhada, e os contadores "Lidas com sucesso" / "Falhadas" acompanham a sessão.
- **Incidências** — crie uma incidência de teste (tipo, local, SSCC lido por foto, foto
  opcional, descrição) e confirme que fica registada com um número automático.
- Entre primeiro como **Supervisor** (código 4321) para ver Base de dados/Definições; entre
  como **Operador** (1234) para ver a app tal como a vê a equipa de armazém.

## Diferenças importantes face à versão no Claude

- **Os dados ficam no browser de cada aparelho** (localStorage), sem partilha entre
  dispositivos nem com a versão Claude. Um telemóvel que faça uma contagem de inventário
  não aparece no computador do responsável, e vice-versa — isto serve para **testar a
  aplicação**, não para uso real em equipa. Para utilização real da logística (vários
  telemóveis a atualizar o mesmo inventário ao mesmo tempo, notificações cruzadas, etc.)
  é preciso a versão Claude, com base de dados partilhada.
- **As fotos das incidências ficam guardadas só neste aparelho** — cada foto é reduzida
  (até 1000px) e guardada dentro do próprio registo da incidência, no browser deste
  dispositivo. Evite anexar muitas fotos grandes, porque o browser tem um limite de espaço
  (alguns MB) para guardar dados, e as fotos não ficam visíveis a partir de outro aparelho.
- **O envio automático de email funciona** (ao contrário da versão Claude, onde está
  bloqueado) — o serviço já vem configurado dentro do `index.html` (bloco
  `window.CF_CONFIG` no topo do ficheiro); se precisar de o mudar, edite esse bloco
  diretamente no ficheiro.
- A leitura do SSCC funciona totalmente offline — tanto a biblioteca de leitura como a base
  de dados inicial de cartolinas vêm embutidas no próprio `index.html`, sem depender de
  nenhum ficheiro ou serviço externo.
- Se precisar de atualizar a lista de cartolinas, pode fazê-lo pela própria app
  (Base de dados → Importar nova versão do Excel), que fica guardada só nesse aparelho.
