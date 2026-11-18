# Contador de Folhas — Logística

Aplicação de telemóvel/tablet para a logística da Hinojosa Graphicsleader, com:

- **Contar** — número de folhas de cartolina numa palete a partir da altura da pilha.
- **Kg ⇄ Folhas** — converte folhas em quilos e quilos em folhas.
- **Inventário** — leitura por câmara do código da etiqueta SSCC (código de barras linear
  GS1-128 ou QR) para inventário físico ao armazém, com um flash a ecrã inteiro (verde/vermelho,
  bem visível) em cada leitura, comparação com um export do SAP (MIGO), geração do ficheiro
  de correções, e exportação de todo o inventário para Excel (para comparar fora da aplicação).
- **Incidências** — registo de incidências logísticas (paletes/bobines danificadas, avarias,
  etc.) com formulário configurável, leitura de SSCC, foto opcional, numeração automática
  (ano.semana.nº), e geração automática de um PDF da incidência (layout próprio da Hinojosa),
  pronto a descarregar e enviar ao fornecedor/parceiro.
- **Definições** (só Supervisor) — códigos de acesso, responsável pela base de dados, pedidos
  dos operadores, notificações por email, formulário de incidências.

A app pede um código de acesso ao abrir, com dois perfis: **Operador** e **Supervisor**.
Em ecrã pequeno (telemóvel), use o botão ☰ no canto superior esquerdo para mudar de separador.

Aplicação original desenvolvida por David Malheiro (artefacto Claude); esta versão foi
adaptada para alojamento estático, sem precisar de conta Claude.

## Esta é a versão de ficheiro único (recomendada)

**Esta pasta tem um único ficheiro, `index.html`** — tudo o resto (base de dados inicial de
cartolinas, leitura de código de barras/QR, adaptador da base de dados) está embutido dentro
dele. A única exceção são os três scripts do Firebase (ver secção seguinte), que vêm sempre de
um CDN público porque precisam de ligação à internet para sincronizar dados — exatamente como
qualquer app que use uma base de dados na nuvem. Não há pastas `js/`, `img/` ou `data/` para
esta versão: isto elimina por completo os problemas que acontecem quando o GitHub não preserva
a estrutura de pastas ao carregar vários ficheiros de uma vez. **Use sempre esta versão**, a
não ser que precise mesmo de editar o email automático sem tocar no `index.html` (nesse caso
use a versão em `github-bundle/`, com `config.js` separado).

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

## Ligar a base de dados partilhada (Firebase) — importante

Esta versão já vem preparada para sincronizar os dados entre TODOS os telemóveis/computadores
que abrirem o link (tal como a versão Claude), usando o Firebase Firestore (gratuito, da
Google) — a única parte que não é possível embutir no ficheiro único, por precisar sempre de
ligação à internet para falar com a nuvem. Enquanto não configurar isto, a app funciona mas
cada aparelho fica com os seus próprios dados, sem partilha.

Passos (uma única vez, ±10 minutos):

1. Abrir https://console.firebase.google.com e criar um projeto novo (plano gratuito "Spark").
2. **Build → Firestore Database** → "Criar base de dados" → modo produção → região mais
   próxima (ex. `eur3 (europe-west)`).
3. No separador **Regras**, substituir o conteúdo por:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

   e "Publicar".
4. **Build → Authentication** → "Começar" → ativar o método **Anónimo**.
5. ⚙️ (Definições do projeto) → **Geral** → "As suas apps" → `</>` (Web) → registar uma app →
   copiar o objeto `firebaseConfig`.
6. Abrir o `index.html` num editor de texto, procurar `window.CF_FIREBASE_CONFIG` (perto do
   topo, logo a seguir ao `window.CF_CONFIG`) e colar lá os valores copiados (`apiKey`,
   `authDomain`, `projectId`, etc.).
7. Voltar a carregar o `index.html` atualizado para o repositório no GitHub.

A partir daí, qualquer pessoa que abra o link — no telemóvel da logística, no seu computador,
etc. — vê e atualiza os MESMOS dados em tempo real. Sem configurar isto, a app continua a
funcionar normalmente, só sem sincronização entre aparelhos (ver "Diferenças importantes"
abaixo).

## Já vem pronta a testar

- **Contar / Kg ⇄ Folhas** — já tem a base de dados de cartolinas embutida (1103 linhas),
  por isso já dá para escolher fornecedor + gramagem e calcular a palete sem preparar nada.
- **Inventário** — tire uma foto a qualquer etiqueta com código de barras (Code128/GS1-128)
  ou QR para testar a leitura; o ecrã fica todo verde numa leitura bem-sucedida e todo
  vermelho numa falhada, e os contadores "Lidas com sucesso" / "Falhadas" acompanham a
  sessão. Use "📊 Exportar Excel" para transferir a lista contada em .xlsx.
- **Incidências** — crie uma incidência de teste (tipo, local, SSCC lido por foto, foto
  opcional, descrição) e confirme que fica registada com um número automático; use "📄
  Descarregar PDF desta incidência" (ou o botão "📄 PDF" na lista) para gerar o relatório em
  PDF, pronto a enviar por email ao fornecedor/parceiro.

## Download de ficheiros (PDF/Excel) nesta versão

O download do PDF da incidência e do Excel do inventário usa o mecanismo normal do browser
(um link temporário que o browser transfere de imediato) — não há confirmação extra a
mostrar. Isto é diferente da versão no artefacto Claude, onde a plataforma pede sempre
confirmação antes de gravar um ficheiro.
- Entre primeiro como **Supervisor** (código 4321) para ver Base de dados/Definições; entre
  como **Operador** (1234) para ver a app tal como a vê a equipa de armazém.

## Diferenças importantes face à versão no Claude

- **Com o Firebase configurado (ver secção acima), os dados ficam partilhados entre todos os
  aparelhos**, tal como na versão Claude. Sem o Firebase configurado, cada aparelho guarda os
  seus próprios dados (localStorage), sem partilha nenhuma.
- **As fotos das incidências** são reduzidas (até 1000px) e guardadas dentro do próprio
  registo da incidência — com Firebase configurado, ficam sincronizadas com todos os
  aparelhos; sem Firebase, ficam só no aparelho que tirou a foto. O Firestore tem um limite de
  1 MB por documento: evite anexar mais do que 2–3 fotos grandes à mesma incidência.
- **O envio automático de email funciona** (ao contrário da versão Claude, onde está
  bloqueado) — o serviço já vem configurado dentro do `index.html` (bloco
  `window.CF_CONFIG` no topo do ficheiro); se precisar de o mudar, edite esse bloco
  diretamente no ficheiro.
- A leitura do SSCC funciona totalmente offline — a biblioteca de leitura e a base de dados
  inicial de cartolinas vêm embutidas no próprio `index.html`. Só a sincronização com o
  Firebase (quando configurada) precisa de ligação à internet, tal como a versão Claude.
- Se precisar de atualizar a lista de cartolinas, pode fazê-lo pela própria app
  (Base de dados → Importar nova versão do Excel) — com Firebase configurado, essa
  atualização fica logo visível em todos os aparelhos.
