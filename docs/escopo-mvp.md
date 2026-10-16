# Documento de Escopo — MVP: Gerador de Senhas Seguras

## 1. Visão Geral e Contexto
O presente documento especifica o escopo do Produto Mínimo Viável (MVP) para um **Gerador de Senhas Seguras**, acessível via navegadores web em múltiplos dispositivos (PC Desktop, smartphones e tablets rodando Android e iOS). 

O sistema adota uma arquitetura **100% Client-Side / Standalone (Zero-Dependency)**:
- **Solução Standalone (Arquivo Único):** Todo o código necessário (marcação HTML5, estilização CSS3 e scripts JavaScript ES6+) é concentrado em um único arquivo `.html` autossuficiente (ou conjunto estático direto), permitindo execução instantânea.
- **Zero Pré-Requisitos e Sem Backend:** Não há dependência de servidores de aplicação, bancos de dados ou instalação prévia de ferramentas (como Node.js, npm, Python, etc.). A aplicação é executada diretamente pelo navegador do usuário.
- **Processamento Criptográfico Local:** Todo o processo de geração, cálculo de entropia e validações é realizado no próprio navegador utilizando a API criptográfica nativa do sistema operacional (Web Cryptography API).

---

## 2. Objetivo
Desenvolver e disponibilizar uma aplicação web leve, responsiva, independente e segura que permita aos usuários gerar senhas fortes e customizadas em tempo real. A solução não exige nenhuma instalação de pré-requisitos, opera diretamente no navegador com privacidade absoluta (*Zero Knowledge* e *Zero Network*), eliminando a vulnerabilidade associada ao uso de senhas fracas ou reutilizadas.

---

## 3. Requisitos Funcionais (RF)

| ID | Nome | Descrição Técnica | Prioridade |
| :--- | :--- | :--- | :--- |
| **RF-01** | Configuração de Comprimento | Permitir que o usuário defina o tamanho da senha por meio de um controle deslizante (*slider*) e campo numérico sincronizado. Intervalo permitido: 4 a 64 caracteres (padrão sugerido: 16 caracteres). | Alta |
| **RF-02** | Seleção de Conjunto de Caracteres | Permitir alternar a inclusão/exclusão dos seguintes conjuntos:<br>• Letras maiúsculas (`A-Z`)<br>• Letras minúsculas (`a-z`)<br>• Dígitos numéricos (`0-9`)<br>• Símbolos/Caracteres especiais (`!@#$%^&*()_+-=[]{}|;:,.<>?`) | Alta |
| **RF-03** | Exclusão de Caracteres Ambíguos | Disponibilizar opção para excluir caracteres visualmente semelhantes ou propensos a confusão em digitação manual (ex.: `0`, `O`, `o`, `1`, `l`, `I`, `\|`). | Média |
| **RF-04** | Geração Criptográfica Segura | Gerar senhas utilizando a API criptográfica nativa do navegador (CSPRNG — `window.crypto.getRandomValues`), garantindo distribuição uniforme e imprevisibilidade estatística sem tráfego de rede. | Alta |
| **RF-05** | Garantia de Inclusão de Regras | Garantir que a senha gerada contenha obrigatoriamente ao menos um caractere de cada conjunto selecionado pelo usuário. | Alta |
| **RF-06** | Ação de Cópia para Área de Transferência | Fornecer botão de ação rápida para copiar a senha gerada via Clipboard API (`navigator.clipboard.writeText`), exibindo feedback visual temporário (ex.: *tooltip* ou *toast* "Copiado!"). | Alta |
| **RF-07** | Regeneração Rápida | Permitir gerar uma nova senha instantaneamente com um clique no botão "Gerar Novamente" ou ao alterar qualquer parâmetro de configuração. | Alta |
| **RF-08** | Indicador de Força e Entropia | Exibir cálculo dinâmico da entropia da senha gerada (em bits) e classificação visual de força (ex.: Fraca, Média, Forte, Muito Forte) com base no espaço amostral do conjunto de caracteres e comprimento. | Média |
| **RF-09** | Alternância de Visibilidade | Permitir alternar a visibilidade da senha na tela (ocultar com máscara ou exibir em texto claro) para evitar visualização indevida (*shoulder surfing*). | Baixa |

---

## 4. Requisitos Não Funcionais (RNF)

| ID | Categoria | Descrição Técnica |
| :--- | :--- | :--- |
| **RNF-01** | **Segurança & Privacidade** | • **Zero Persistence:** Nenhuma senha gerada deve ser armazenada em disco, `localStorage`, `sessionStorage` ou cookies.<br>• **Zero Network / Zero Telemetry:** A aplicação não realiza nenhuma requisição de rede para funcionar. Nenhuma informação ou métrica é transmitida externamente. |
| **RNF-02** | **Criptografia** | O motor de aleatoriedade deve utilizar exclusivamente o gerador CSPRNG nativo do navegador (`window.crypto.getRandomValues`), proibindo terminantemente o uso de funções inseguras como `Math.random()`. |
| **RNF-03** | **Responsividade & Multiplataforma** | A interface gráfica deve se adaptar de forma fluida a diferentes *viewports* (Mobile: 320px–480px, Tablet: 481px–768px, Desktop: 769px+), funcionando em navegadores modernos (Chrome, Firefox, Safari, Edge) em Android, iOS, Windows, macOS e Linux. |
| **RNF-04** | **Desempenho e Latência** | • Carregamento e renderização instantâneos ao abrir o arquivo `.html`.<br>• Tempo de geração e cálculo de entropia inferior a 10 milissegundos após interação do usuário. |
| **RNF-05** | **Acessibilidade (a11y)** | Aderência às diretrizes WCAG 2.1 Nível AA, contemplando contraste de cores adequado (mínimo 4.5:1 para texto normal), navegabilidade completa via teclado e atributos ARIA adequados em controles dinâmicos. |
| **RNF-06** | **Zero Dependências & Standalone** | Todo o código deve estar contido em um único arquivo `.html` autônomo (HTML5 semântico, CSS3 e JavaScript embutidos), sem dependência de frameworks externos, bibliotecas via CDN ou pré-processadores. |

---

## 5. Fora de Escopo (FDE)

Os itens listados abaixo estão explicitamente excluídos deste MVP:

- **FDE-01: Backend ou APIs de Servidor:** Não haverá camada de backend, serviços REST ou microsserviços. A execução é 100% no cliente.
- **FDE-02: Gestão e Cofre de Senhas:** Não haverá armazenamento, categorização ou persistência de senhas para consulta posterior.
- **FDE-03: Autenticação de Usuários:** Não haverá sistema de cadastro, login, perfis de usuários ou controle de sessão.
- **FDE-04: Dependência de Instalação de Runtimes:** Não haverá exigência de Node.js, gerenciadores de pacotes ou compiladores para executar o projeto.
- **FDE-05: Aplicativos Nativos Dedicados (Lojas de Apps):** O acesso é feito diretamente pelo navegador web.
- **FDE-06: Extensões de Navegador:** Não inclui o desenvolvimento de extensões de navegador (*autofill*).
- **FDE-07: Verificação em Bases Externas de Senhas Vazadas:** Não haverá integração com APIs externas (ex.: *Have I Been Pwned*) no MVP para manter o princípio de *Zero Network*.
- **FDE-08: Suporte a Navegadores Obsoletos:** Não haverá suporte a navegadores legados sem suporte a ES6+ ou Web Cryptography API (ex.: Internet Explorer 11).

---

## 6. Arquitetura Preliminar do MVP

```
+-----------------------------------------------------------------+
|               Arquivo Único Standalone (index.html)             |
|                                                                 |
|  +-----------------------------------------------------------+  |
|  | Estrutura: HTML5 Semântico                                |  |
|  | - Controles acessíveis (slider, checkboxes, inputs)        |  |
|  +-----------------------------------------------------------+  |
|  | Estilo: CSS3 Embutido (<style>)                           |  |
|  | - Design responsivo (Mobile-First)                        |  |
|  | - Variáveis CSS para temas e layout flex/grid             |  |
|  +-----------------------------------------------------------+  |
|  | Lógica: Vanilla JavaScript ES6+ Embutido (<script>)       |  |
|  | - CSPRNG nativo: window.crypto.getRandomValues            |  |
|  | - Cálculo local de entropia (Shannon / bit-strength)      |  |
|  | - Clipboard API para cópia rápida                         |  |
|  | - Validação de inclusão de regras                         |  |
|  +-----------------------------------------------------------+  |
|                                                                 |
|  * ZERO DEPENDÊNCIAS * ZERO BACKEND * ZERO NETWORK *           |
+-----------------------------------------------------------------+
                                |
               (Aberto diretamente no Navegador)
                                v
+-----------------------------------------------------------------+
|                       Navegador do Usuário                      |
|      - Execução local via protocolo file:/// ou servidor web    |
|      - 100% Funcional Offline                                   |
|      - Total isolamento e privacidade dos dados                 |
+-----------------------------------------------------------------+
```

---

## 7. Critérios de Aceite do MVP

1. **Execução Imediata e Zero Pré-requisitos:** O projeto deve funcionar perfeitamente com um simples duplo clique no arquivo `.html` (via protocolo `file:///`), sem requerer Node.js, servidores locais ou qualquer ferramenta de build.
2. **Geração Funcional:** O sistema deve gerar senhas de 4 a 64 caracteres cumprindo rigorosamente os filtros selecionados pelo usuário.
3. **Aleatoriedade Criptográfica:** Uso exclusivo de `window.crypto.getRandomValues`, garantindo a ausência de chamadas a `Math.random()`.
4. **Zero Conexões de Rede:** O gerador deve operar completamente offline sem realizar requisições HTTP/HTTPS durante o seu uso.
5. **Usabilidade Multiplataforma:** A interface deve ser testada e validada em navegadores desktop e mobile sem sobreposições de layout.
6. **Cópia com Feedback:** A função de cópia deve transferir a senha para o clipboard do dispositivo com resposta visual imediata.

---

## 8. Roadmap de Releases

| Versão | Fase | Descrição e Entregas Principais | Status |
| :--- | :--- | :--- | :--- |
| **v0.1.0** | **MVP (Single-File)** | • Aplicação autônoma em arquivo único `index.html`<br>• Geração via CSPRNG nativo (`window.crypto.getRandomValues`) de 4 a 64 caracteres<br>• Filtros de caracteres (maiúsculas, minúsculas, números e símbolos)<br>• Opção para exclusão de caracteres ambíguos (`0`, `O`, `l`, `1`, etc.)<br>• Cálculo dinâmico de entropia e indicador visual de força<br>• Cópia para área de transferência via Clipboard API com feedback visual temporário<br>• Interface responsiva (Mobile, Tablet e Desktop) e acessível (WCAG 2.1 AA) | Concluído |
| **v0.2.0** | **PWA e Produtividade** | • Suporte a PWA (Web App Manifest e Service Worker) para instalação e funcionamento offline resiliente<br>• Atalhos de teclado (ex.: barra de espaço para regenerar, `Ctrl+C` dedicado)<br>• Alternância de tema visual (Modo Claro / Modo Escuro / Seguir Sistema)<br>• Histórico efêmero em memória de sessão (opcional e descartável ao fechar a aba) | Concluído |
| **v0.3.0** | **Testes Unitários e Qualidade** | • Suíte de testes unitários automatizados em ambiente navegador/standalone (testes estatísticos de aleatoriedade CSPRNG, validação de regras de inclusão e cálculo de entropia)<br>• Auditoria completa de acessibilidade e performance (Lighthouse a11y score 100)<br>• Otimização e validação estrita de contraste, teclado e atributos ARIA | Concluído |
| **v1.0.0** | **Release Final** | • Versão estável final homologada para produção em múltiplos navegadores e sistemas operacionais<br>• Modo opcional de geração de frases-passe (*Passphrases* baseadas no método Diceware)<br>• Exportação e importação de perfis de preferências em JSON<br>• Documentação técnica e de usuário final consolidadas | Planejado |
