# Gerador de Senhas Seguras (PG)

Aplicação web *standalone*, leve e segura para geração de senhas fortes e customizáveis em tempo real. Desenvolvida em **arquivo único (`index.html`)**, sem necessidade de backend, compiladores ou instalação de ferramentas de terceiros.

---

## 🎯 Objetivo

Fornecer uma ferramenta de segurança de uso imediato, acessível em qualquer dispositivo através do navegador web, que permita gerar senhas de alta entropia com parâmetros personalizáveis. A aplicação prioriza a privacidade e a segurança:
- **Zero Pré-Requisitos:** Não requer instalação de Node.js, bancos de dados, pacotes ou servidores de aplicação.
- **Zero Network & Zero Persistence:** Operação 100% local no navegador; nenhuma senha é gravada em armazenamento persistente ou trafegada por requisições de rede.
- **Criptografia Forte:** Aleatoriedade gerada exclusivamente pela API criptográfica nativa do navegador (CSPRNG).

---

## 🛠️ Stack Tecnológica

A solução foi projetada para ser autossuficiente em um único arquivo:

- **Estrutura:** HTML5 Semântico e Acessível (suporte a ARIA e navegação por teclado).
- **Estilização:** CSS3 Moderno embutido (design responsivo *Mobile-First*, temas via variáveis CSS, sem frameworks externos).
- **Lógica e Criptografia:** Vanilla JavaScript (ES6+) embutido:
  - `window.crypto.getRandomValues()` para aleatoriedade criptográfica segura (CSPRNG).
  - Clipboard API (`navigator.clipboard`) para cópia rápida com feedback visual.
  - Algoritmo de cálculo de entropia (Shannon / bit-strength) em tempo real.
- **Zero Dependências:** Sem bibliotecas externas, CDNs ou passos de build.

---

## 🚀 Como Rodar Localmente

### Pré-requisitos
- **Nenhum!** Não é necessário instalar Node.js, Python, gerenciadores de pacotes ou servidores.
- Basta possuir qualquer navegador web moderno instalado (Google Chrome, Mozilla Firefox, Microsoft Edge, Apple Safari, Opera ou navegadores móveis).

### Execução

#### Opção 1: Abertura Direta (Mais simples)
Dê um **duplo clique** no arquivo `index.html` ou abra-o diretamente no navegador:
```text
file:///c:/Users/leopoldo.fernandes/Projetos/PG/index.html
```

#### Opção 2: Servidor Estático Local (Opcional)
Caso prefira servir via HTTP local, utilize qualquer utilitário de sua preferência:
```bash
# Com Python (se já estiver instalado):
python -m http.server 3000

# Ou com extensão do editor (ex.: Live Server no VS Code)
```

---

## 🗺️ Roadmap de Releases

| Versão | Fase | Descrição e Entregas Principais | Status |
| :--- | :--- | :--- | :--- |
| **v0.1.0** | **MVP (Single-File)** | • Aplicação autônoma em arquivo único `index.html`<br>• Geração via CSPRNG nativo (`window.crypto.getRandomValues`) de 4 a 64 caracteres<br>• Filtros de caracteres (maiúsculas, minúsculas, números e símbolos)<br>• Opção para exclusão de caracteres ambíguos (`0`, `O`, `l`, `1`, etc.)<br>• Medidor visual de força e cálculo dinâmico de entropia em bits<br>• Cópia rápida para área de transferência com feedback visual temporário<br>• Interface responsiva para Desktop, Tablet e Mobile (WCAG 2.1 AA) | Concluído |
| **v0.2.0** | **PWA e Produtividade** | • Suporte a PWA (Web App Manifest e Service Worker) para instalação e funcionamento 100% offline<br>• Atalhos de teclado (ex.: barra de espaço para regenerar, `Ctrl+C` dedicado)<br>• Alternância de temas visuais (Modo Claro / Modo Escuro / Seguir Sistema)<br>• Histórico efêmero em memória de sessão (opcional e descartável ao fechar a aba) | Concluído |
| **v0.3.0** | **Testes Unitários e Qualidade** | • Suíte de testes unitários automatizados em ambiente navegador/standalone (testes de aleatoriedade CSPRNG, validação de regras de inclusão e cálculo de entropia)<br>• Auditoria completa de acessibilidade e performance (Lighthouse a11y score 100)<br>• Otimização e validação estrita de contraste, teclado e atributos ARIA | Concluído |
| **v1.0.0** | **Release Final** | • Versão final estável e homologada para produção em múltiplos navegadores e sistemas operacionais<br>• Modo opcional de geração de frases-passe (*Passphrases* baseadas no método Diceware)<br>• Exportação e importação de perfis de preferências em formato JSON<br>• Documentação técnica e de usuário final consolidadas | Planejado |

---

## 🔒 Princípios de Segurança

1. **CSPRNG Nativo:** A aplicação utiliza estritamente `window.crypto.getRandomValues`. Funções pseudoaleatórias estatisticamente vulneráveis como `Math.random()` são terminantemente proibidas.
2. **Isolamento de Rede:** O gerador funciona sem emitir nenhuma requisição de rede (`fetch`, `XMLHttpRequest`, telemetria ou analytics).
3. **Privacidade Absoluta:** Nenhuma senha ou dado digitado é armazenado em disco, `localStorage`, `sessionStorage` ou cookies.

---

## 📄 Licença

Distribuído sob a licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.
