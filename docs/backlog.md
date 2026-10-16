# Backlog do Projeto — Gerador de Senhas Seguras (PG)

Backlog operacional organizado pelas 4 entregas do roadmap de releases, contendo os Requisitos Funcionais (RF) e Requisitos Técnicos (RT) acompanhados de seus respectivos critérios de aceite em formato de checklist.

---

## 📦 Release v0.1.0 — MVP (Single-File)

Foco na disponibilização da aplicação autônoma em arquivo único `index.html`, sem dependência de ferramentas externas, com motor de aleatoriedade CSPRNG nativo e usabilidade responsiva.

### [RF-01] Configuração de Comprimento da Senha
- **Descrição:** Slider numérico e campo de entrada sincronizados permitindo definir o comprimento da senha entre 4 e 64 caracteres (padrão inicial: 16 caracteres).
- [x] O controle deslizante (*slider*) e o campo numérico mantêm seus valores sincronizados em tempo real.
- [x] O limite inferior permitido é 4 e o limite superior é 64.
- [x] A alteração do valor gera instantaneamente uma nova senha com o novo comprimento.

### [RF-02] Seleção de Conjunto de Caracteres
- **Descrição:** Opções independentes para incluir ou excluir os quatro grupos fundamentais de caracteres.
- [x] Checkboxes funcionais para: Maiúsculas (`A-Z`), Minúsculas (`a-z`), Números (`0-9`) e Símbolos especiais (`!@#$%^&*()_+-=[]{}|;:,.<>?`).
- [x] O sistema impede a desmarcação simultânea de todos os conjuntos (mantendo no mínimo um conjunto ativo).
- [x] A senha gerada é composta exclusivamente pelos conjuntos selecionados.

### [RF-03] Exclusão de Caracteres Ambíguos
- **Descrição:** Filtro opcional para eliminar caracteres visualmente confusos para digitação humana.
- [x] Disponibilização de controle (toggle/checkbox) "Excluir caracteres semelhantes/ambíguos".
- [x] Quando ativado, a senha gerada não deve conter nenhum dos caracteres: `0`, `O`, `o`, `1`, `l`, `I`, `|`.
- [x] Quando desativado, tais caracteres voltam a integrar o espaço amostral normalmente.

### [RF-04] Geração Criptográfica Segura
- **Descrição:** Geração de valores aleatórios por meio de primitivas criptográficas nativas do navegador.
- [x] Utilização exclusiva da Web Cryptography API (`window.crypto.getRandomValues`).
- [x] Eliminação de viés de amostragem (*modulo bias*) na seleção de caracteres dentro do espaço amostral.
- [x] Ausência completa de chamadas a `Math.random()`.

### [RF-05] Garantia de Inclusão de Regras
- **Descrição:** Garantia estrutural de que a senha contenha ao menos um exemplar de cada conjunto de caracteres selecionado.
- [x] A senha gerada contém obrigatoriamente pelo menos 1 caractere de cada grupo marcado.
- [x] A distribuição dos caracteres obrigatórios é embaralhada de forma criptograficamente segura (Fisher-Yates via CSPRNG), evitando posições previsíveis.

### [RF-06] Cópia para Área de Transferência com Feedback
- **Descrição:** Ação de cópia rápida com feedback visual temporário via Clipboard API.
- [x] Botão de ação rápida que copia a senha em texto claro via `navigator.clipboard.writeText`.
- [x] Exibição de confirmação visual imediata (ex.: tooltip/toast "Copiado!" com auto-dismiss em 2 segundos).
- [x] Tratamento gracioso de exceção caso o navegador não conceda permissão de acesso à área de transferência.

### [RF-07] Regeneração Rápida
- **Descrição:** Mecanismos ágeis para criação de nova credencial sob demanda.
- [x] Botão dedicado "Gerar Novamente" / "Novo" para criação imediata de outra senha.
- [x] Regeneração reativa acionada automaticamente ao modificar qualquer parâmetro ou filtro.

### [RF-08] Indicador de Força e Cálculo de Entropia
- **Descrição:** Exibição da métrica de entropia (Shannon / bit-strength) e barra visual de robustez.
- [x] Cálculo da entropia da senha baseado na fórmula $E = L \times \log_2(R)$, exibindo o valor em bits.
- [x] Barra de progresso com coloração dinâmica e classificação textual: Fraca (< 50 bits), Média (50–74 bits), Forte (75–99 bits) e Muito Forte (≥ 100 bits).

### [RF-09] Alternância de Visibilidade da Senha
- **Descrição:** Opção para mascarar ou revelar o conteúdo da senha no visor principal.
- [x] Botão de alternância com ícone indicativo (olho aberto / olho fechado).
- [x] Ocultação do texto por meio de caracteres de máscara para proteção contra visualização indevida (*shoulder surfing*).

### [RT-01] Arquitetura Standalone (Single-File)
- **Descrição:** Empacotamento integral do sistema em um único arquivo autônomo `index.html`.
- [x] O arquivo contém todo o HTML5 semântico, estilos CSS3 e código JavaScript ES6+ embutidos.
- [x] Execução imediata via duplo clique (protocolo `file:///`), sem necessidade de servidor HTTP.
- [x] Nenhuma dependência externa carregada via CDN ou pré-processador.

### [RT-02] Segurança e Isolamento de Rede (*Zero Network*)
- **Descrição:** Garantia de operação 100% isolada e confidencial no cliente.
- [x] Zero emissão de requisições de rede (`fetch`, `XMLHttpRequest`, tracking ou telemetria).
- [x] Nenhuma gravação de credenciais em `localStorage`, `sessionStorage`, cookies ou IndexedDB.

### [RT-03] Design Responsivo e Acessibilidade (Mobile-First)
- **Descrição:** Interface fluida para dispositivos móveis e desktops com suporte a acessibilidade.
- [x] Layout adaptável para viewports a partir de 320px sem quebras ou rolagem horizontal.
- [x] Navegação completa por teclado via tecla `Tab`, `Enter` e `Space`.
- [x] Contraste de cores entre texto e fundo com proporção mínima de 4.5:1 (WCAG 2.1 AA).

---

## 📦 Release v0.2.0 — PWA e Produtividade

Foco em capacidades de instalação offline como Web App nativo e recursos para agilidade de uso no dia a dia.

### [RF-10] Atalhos de Teclado para Ações Frequentes
- **Descrição:** Mapeamento de teclas de atalho globais para aumentar a velocidade operacional do usuário.
- [ ] Pressionamento da tecla `Espaço` gera uma nova senha instantaneamente (quando o foco não estiver em inputs numéricos).
- [ ] Tecla `C` ou combinação `Ctrl+C` aciona a cópia imediata da senha atual.
- [ ] Tecla `V` alterna a visibilidade da senha (ocultar/mostrar).
- [ ] Legenda visual acessível indicando os atalhos disponíveis.

### [RF-11] Alternância de Temas (Claro / Escuro / Sistema)
- **Descrição:** Personalização da aparência visual com persistência voluntária da preferência estética.
- [ ] Seletor com 3 modos: Claro, Escuro e Automático (seguindo a preferência do sistema operacional via `prefers-color-scheme`).
- [ ] Transição de tema instantânea sem efeito de piscar (*flash of unstyled content*).

### [RF-12] Histórico Efêmero em Memória de Sessão
- **Descrição:** Registro volátil das últimas senhas geradas durante a utilização ativa.
- [ ] Painel recolhível exibindo até as últimas 5 senhas geradas durante a aba aberta.
- [ ] Dados armazenados estritamente na memória de execução JavaScript (RAM), sem gravação em storage persistente.
- [ ] Botão de "Limpar Histórico" para descarte imediato dos itens.
- [ ] O encerramento ou recarregamento da aba destrói permanentemente o histórico.

### [RT-04] Suporte a Progressive Web App (PWA)
- **Descrição:** Transformação da solução em Web App instalável e com resiliência offline.
- [ ] Arquivo `manifest.json` com metadados da aplicação, cores temáticas e ícones em múltiplas resoluções.
- [ ] Service Worker configurado para cache offline imediato de todos os assets da aplicação.
- [ ] Capacidade de instalação direta na tela de início do Android/iOS e como app no Desktop (Chrome/Edge).

---

## 📦 Release v0.3.0 — Testes Unitários e Qualidade

Foco em confiabilidade criptográfica, precisão estatística e conformidade com diretrizes técnicas de excelência.

### [RT-05] Suíte de Testes Unitários Standalone
- **Descrição:** Módulo de testes automatizados executado diretamente no navegador sem necessidade de terminal ou Node.js.
- [ ] Teste unitário de limites de comprimento (validação estrita de 4 a 64 caracteres).
- [ ] Teste de inclusão obrigatória de todos os conjuntos marcados em amostras repetidas.
- [ ] Teste de exclusão estrita de caracteres ambíguos.
- [ ] Teste da precisão do cálculo matemático de entropia em bits.
- [ ] Painel visual minimalista no navegador exibindo status das asserções (Pass/Fail).

### [RT-06] Teste de Distribuição Estatística do CSPRNG
- **Descrição:** Validação estatística de aleatoriedade e uniformidade na amostragem de dados.
- [ ] Teste automatizado gerando amostra de 10.000 caracteres, verificando desvio uniforme via teste de Qui-Quadrado ($\chi^2$).
- [ ] Validação automatizada atestando ausência de qualquer referência ou execução de `Math.random()`.

### [RT-07] Auditoria de Acessibilidade e Performance
- **Descrição:** Validação formal com ferramentas de auditoria técnica.
- [ ] Pontuação de 100/100 na categoria Acessibilidade da ferramenta Google Lighthouse.
- [ ] Pontuação de 100/100 na categoria Melhores Práticas e Performance no Lighthouse.
- [ ] Inclusão e validação de atributos `aria-label`, `aria-live` (para leitura de tela ao gerar e copiar) e `role`.

---

## 📦 Release v1.0.0 — Release Final

Foco em consolidação para produção, novos modos de geração amigáveis e homologação em escala.

### [RF-13] Modo de Geração de Frases-Passe (*Passphrases* / Diceware)
- **Descrição:** Modalidade alternativa de geração baseada em palavras aleatórias legíveis e fáceis de memorizar.
- [ ] Seletor de modo entre "Senha Aleatória" e "Frase-passe (Diceware)".
- [ ] Controle para definição da quantidade de palavras (de 3 a 8 palavras).
- [ ] Dicionário embutido e compacto de palavras selecionadas em português.
- [ ] Opção de escolha do separador entre as palavras (hífen, espaço, ponto ou dígito aleatório).

### [RF-14] Exportação e Importação de Configurações de Preferências
- **Descrição:** Mecanismo seguro para salvar e carregar perfis de regras sem gravar senhas.
- [ ] Botão para exportar arquivo `.json` contendo estritamente os parâmetros de configuração (tamanho preferido, conjuntos ativos, exclusão de ambíguos).
- [ ] Botão para importar arquivo `.json` com validação de esquema contra dados corrompidos.
- [ ] Garantia formal de que nenhuma senha ou credencial gerada seja gravada no arquivo de configuração.

### [RT-08] Homologação Cross-Browser e Multiplataforma
- **Descrição:** Verificação formal de compatibilidade e experiência em múltiplos ecossistemas.
- [ ] Homologação em navegadores desktop: Google Chrome, Mozilla Firefox, Apple Safari e Microsoft Edge.
- [ ] Homologação em navegadores mobile: Chrome (Android) e Safari (iOS).
- [ ] Validação de responsividade táctil e ausência de bugs visuais em orientações retrato e paisagem.

### [RT-09] Documentação Consolidada e Fechamento de Release
- **Descrição:** Entrega da versão final homologada para o usuário final.
- [ ] Documentação de usuário final e manual de atalhos incorporados.
- [ ] Criação de tag Git formal `v1.0.0` com notas de lançamento consolidadas no repositório.
