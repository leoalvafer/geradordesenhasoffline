# Relatório de Auditoria Técnica, Usabilidade e Roadmap — PG (Gerador de Senhas Offline)

**Data da Auditoria:** 18 de Outubro de 2026  
**Escopo Analisado:** Todo o repositório (`index.html`, `sw.js`, `manifest.json`, documentações)  
**Versão Atual:** `v1.0.0` (MVP Consolidado)

---

## 1. Auditoria de Código: Brechas de Segurança e Bugs

A aplicação apresenta uma base sólida nos requisitos de ausência de requisições externas e uso exclusivo de CSPRNG nativo ([`window.crypto.getRandomValues`](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L1952-L1964)). No entanto, foram detectadas vulnerabilidades conceituais, riscos de vazamento de credenciais e bugs de lógica que afetam a robustez e a integridade do sistema.

### 🔴 Riscos e Brechas de Segurança

#### 1. Dicionário Diceware Reduzido (512 Palavras = Falha Crítica de Entropia em Frases-Passe)
* **Local:** [index.html — Linha 1858](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L1858)
* **Diagnóstico:** O dicionário embutido contém exatamente 512 palavras (`DICEWARE_DICTIONARY.length === 512`). Cada palavra fornece apenas $\log_2(512) = 9\text{ bits}$ de entropia.
* **Impacto Criptográfico:**
  * Frase com 3 palavras: **27 bits** ($512^3 \approx 134\text{ milhões}$ de combinações). Quebrada em **frações de segundo** por qualquer ataque de dicionário direcionado ao repositório público.
  * Frase com 4 palavras (padrão): **36 bits** ($\approx 68\text{ bilhões}$ de combinações). Quebrável em poucos segundos em hardware doméstico moderno (GPUs).
  * O limite máximo permitido (8 palavras) rende apenas **72 bits**, nunca atingindo a faixa classificada como "Forte" ($\ge 75\text{ bits}$) ou "Muito Forte" ($\ge 100\text{ bits}$).
  * Em contrapartida, o padrão Diceware da EFF usa $6^5 = 7.776\text{ palavras}$ ($\approx 12.92\text{ bits/palavra}$), onde 5 palavras já garantem $\approx 65\text{ bits}$ e 6 palavras alcançam $\approx 77.5\text{ bits}$.
* **Recomendação:** Expandir a lista de vocábulos para um subconjunto de 1.296 palavras ($\approx 10.3\text{ bits}$) ou 7.776 palavras ($\approx 12.9\text{ bits}$), ou exibir aviso de segurança alertando sobre a vulnerabilidade do dicionário reduzido.

#### 2. Ausência de Content Security Policy (CSP) no Nível do Navegador
* **Local:** [index.html — `<head>`](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L3-L30)
* **Diagnóstico:** Não há tag `<meta http-equiv="Content-Security-Policy">`.
* **Impacto de Segurança:** Embora o código nativo não faça `fetch`, extensões instaladas no navegador do usuário, scripts injetados por ataques locais ou proxy reverso podem abrir conexões de rede (`connect-src *`).
* **Recomendação:** Declarar CSP estrito no `<head>`:
  ```html
  <meta http-equiv="Content-Security-Policy" content="default-src 'none'; style-src 'unsafe-inline'; script-src 'unsafe-inline'; img-src 'self' data:; manifest-src 'self'; connect-src 'none';">
  <meta name="referrer" content="no-referrer">
  ```
  O `connect-src 'none'` bloqueia por construção do navegador qualquer requisição de rede de saída.

#### 3. Persistência Indefinida no Clipboard do Sistema Operacional (Clipboard Leakage)
* **Local:** [index.html — Linhas 2235-2286](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2235-L2286)
* **Diagnóstico:** A senha copiada permanece na área de transferência por tempo indeterminado.
* **Impacto de Segurança:** No Windows 10/11, o *Histórico da Área de Transferência* (`Win + V`) grava as senhas em log e pode sincronizá-las para a nuvem da conta Microsoft. Em dispositivos Android e iOS, apps em segundo plano podem monitorar o clipboard.
* **Recomendação:** Implementar rotina de limpeza temporizada (auto-clear em 30 ou 60 segundos) e botão explícito "Limpar Área de Transferência".

#### 4. Seleção e Cópia Inadvertida de Caracteres de Máscara (`••••••`)
* **Local:** [index.html — Linhas 388 e 2140](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L388)
* **Diagnóstico:** `.password-text` possui `user-select: all;`. Quando o visor está mascarado (`isMasked = true`), o texto no DOM é substituído por bullets (`•`). Se o usuário selecionar com o mouse ou via teclado (`Ctrl+C` nativo com foco no texto), ele copia bullets em vez da senha real.
* **Impacto:** O usuário pode colar uma sequência de bullets em um formulário cadastral, perdendo o acesso à conta recém-criada.
* **Recomendação:** Ao mascarar a senha, usar estilização com `-webkit-text-security: disc;` mantendo a string real no DOM, ou interceptar a seleção para alertar o usuário.

#### 5. Ausência de Limite de Tamanho no Importador de JSON (DoS de Memória)
* **Local:** [index.html — Linhas 2491-2503](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2491-L2503)
* **Diagnóstico:** `FileReader.readAsText(file)` é disparado sem verificar `file.size`. Se o usuário acidentalmente selecionar um arquivo grande (ex.: ISO, vídeo de 1GB), o navegador trava ao tentar carregar o arquivo na memória.
* **Recomendação:** Inserir `if (file.size > 100 * 1024) throw new Error('Arquivo excessivamente grande');`.

---

### 🐛 Bugs de Lógica e Acessibilidade

| ID | Severidade | Descrição | Localização |
| :--- | :--- | :--- | :--- |
| **BUG-01** | **Alta** | **Inundação do Histórico ao Arrastar Sliders:** O evento `'input'` nos controles de comprimento e palavras gera senhas intermediárias e preenche instantaneamente o limite de 5 itens do histórico, destruindo credenciais anteriores úteis. | [index.html#L2978-L2998](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2978-L2998) |
| **BUG-02** | **Alta** | **Tecla Espaço Bloqueia Acessibilidade de Botões:** A escuta global de tecla intercepta `Space` mesmo quando o foco está em `<button>`, `<summary>` ou `<dialog>`, acionando a regeneração e impedindo o acionamento do elemento em foco via teclado (violação WCAG). | [index.html#L2630-L2646](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2630-L2646) |
| **BUG-03** | **Média** | **Efeito Colateral na Suíte de Testes:** Abrir o modal de testes executa `generatePassword()` e `generatePassphrase()` de produção, alterando a senha da tela e injetando dados falsos no histórico da sessão do usuário. | [index.html#L2813-L2818](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2813-L2818) |
| **BUG-04** | **Baixa** | **Dica de Visor sem Evento de Clique:** O elemento `#password-display` possui `title="Clique para selecionar"`, porém não há nenhum listener de clique associado no JavaScript. | [index.html#L1451](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L1451) |
| **BUG-05** | **Baixa** | **`URL.revokeObjectURL` Síncrono no Download:** No método `exportConfig()`, revogar o Blob síncrono logo após `a.click()` pode interromper o download em navegadores Firefox e mobile WebViews. | [index.html#L2483](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2483) |
| **BUG-06** | **Baixa** | **Atalhos de Teclado Ativos com Diálogos Abertos:** Teclas como `C`, `V` e `Espaço` alteram o estado da tela ao fundo enquanto os modais de Guia ou Testes estão em primeiro plano. | [index.html#L2630-L2682](file:///g:/Meu%20Drive/Projetos/geradordesenhasoffline/index.html#L2630-L2682) |

---

## 2. Sugestões de Melhorias de Usabilidade (UX / UI / DX)

1. **Restaurar Senha do Histórico com 1 Clique:**
   * Atualmente o histórico efêmero permite apenas copiar. Permitir clicar sobre uma credencial do histórico para recarregá-la no visor principal (recalculando entropia e métricas).
2. **Realce Sintático de Caracteres no Visor (Color-Coding):**
   * Destacar visualmente no visor as classes de caracteres: números em azul/ciano, símbolos em laranja/roxo, maiúsculas em negrito, minúsculas em tom neutro. Isso facilita a leitura e a digitação manual em telas secundárias.
3. **Pílulas de Composição no Rodapé do Visor:**
   * Exibir contadores dinâmicos da credencial gerada (ex.: `8 Min`, `4 Mai`, `2 Núm`, `2 Sím`).
4. **Visor Clicável ("Clique para Copiar"):**
   * Permitir clicar diretamente no visor de senha para copiar a credencial, com animação instantânea (comportamento padrão esperado em ferramentas modernas).
5. **Persistência Voluntária de Parâmetros de Configuração:**
   * Atualmente apenas o tema é salvo no `localStorage`. Permitir a opção "Salvar minhas preferências de geração neste navegador" (tamanho padrão, conjuntos ativos, separador preferido).
6. **Redução Dinâmica da Fonte para Senhas Longas:**
   * Para senhas de 40 a 64 caracteres, ajustar a tipografia de `1.25rem` para `1rem` dinamicamente, evitando quebras excessivas de linha que prejudicam a visualização em dispositivos móveis.
7. **Feedback Tátil (Haptic Feedback) no Mobile:**
   * Incorporar `navigator.vibrate([15])` ao clicar em gerar ou copiar quando suportado por navegadores em smartphones Android.

---

## 3. Backlog de Novas Funcionalidades para Próximas Entregas

### 🚀 Release v1.1.0 — Segurança e Refinamento de Usabilidade (Prioridade Alta)
* **[RF-15] Expansão do Dicionário Diceware:** Atualizar a base de frases-passe para lista com 1.296 ou 7.776 palavras em português, garantindo entropia mínima recomendável ($\ge 65\text{ bits}$ em 5 palavras).
* **[RF-16] Content Security Policy e Proteção contra Iframe:** Imposição estrita de CSP via meta tag e cabeçalho anti-clickjacking.
* **[RF-17] Temporizador de Limpeza do Clipboard (Auto-Clear):** Opção configurável para limpar o clipboard do dispositivo após 30 ou 60 segundos com aviso visual.
* **[RF-18] Correção de Histórico e Acessibilidade:** Filtrar o registro de histórico para apenas eventos de soltura do slider (`change`) e preservar navegação por teclado (`Space` em botões).
* **[RF-19] Carregamento de Itens do Histórico:** Botão dedicado ou clique para carregar credencial arquivada de volta ao visor.

### 🌟 Release v1.2.0 — Produtividade e Novos Modos de Geração (Prioridade Média)
* **[RF-20] Modo PIN Numérico Seguro:** Geração de códigos bancários e PINs de 4 a 12 dígitos, com algoritmo de rejeição de sequências triviais (`1234`, `1111`, etc.).
* **[RF-21] Modo Fácil de Pronunciar (Fonético):** Alternância inteligente de consoantes e vogais para credenciais comunicáveis por voz ou fáceis de memorizar.
* **[RF-22] Realce Sintático (Syntax Highlighting) de Caracteres:** Renderização com cores distintas para letras, números e símbolos especiais.
* **[RF-23] Geração em Lote (Bulk Passwords):** Geração de 5 a 100 senhas em lista unificada com botão de exportação em `.txt` ou `.csv` local (para administradores de sistemas).

### 💎 Release v2.0.0 — Utilitários Avançados e Ecossistema (Prioridade Futura)
* **[RF-24] QR Code Offline para Transferência Móvel:** Exibição opcional de QR Code gerado 100% localmente em SVG/Canvas para importar a credencial no smartphone sem tráfego de rede.
* **[RF-25] Analisador Local de Senhas Existentes:** Campo isolado para avaliar a força e estimar tempo de quebra de senhas pré-existentes do usuário (com verificação heurística local de padrões fracos).
* **[RF-26] Gerador de Chaves Criptográficas e Hashes Utilitários:** Geração de chaves hexadecimais (128/256/512 bits), chaves para API/JWT e UUIDs v4 via CSPRNG.
* **[RF-27] Empacotamento como Extensão de Navegador (WebExtension MV3):** Disponibilização em formato de extensão popup para Google Chrome, Edge e Mozilla Firefox.
