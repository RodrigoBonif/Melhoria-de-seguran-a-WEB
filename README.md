# Sistema de Ocorrências Acadêmicas — Análise e Fortalecimento de Segurança

Este repositório contém a entrega da **Atividade Prática 01 (AP01)** da disciplina de **Segurança da Informação**. O objetivo do projeto foi realizar um *security assessment* em um protótipo funcional e implementar controles de mitigação para riscos identificados.

## 1. Visão Geral do Projeto
O sistema original consistia em uma aplicação *client-side* pura para gestão de ocorrências acadêmicas. Durante a auditoria inicial, foram identificadas falhas críticas que comprometiam a tríade da segurança (Confidencialidade, Integridade e Disponibilidade), além de não conformidades com a LGPD.

## 2. Vulnerabilidades Identificadas
A análise técnica revelou os seguintes pontos de falha:
*   **Cross-Site Scripting (XSS):** Ausência de sanitização nos campos de entrada, permitindo a injeção de scripts maliciosos via formulário de ocorrências.
*   **Broken Access Control (BAC):** Falta de validação de permissões no código, permitindo que o perfil `ALUNO` executasse ações administrativas (exclusão e alteração de status).
*   **Exposição de Dados Sensíveis:** Exibição de CPFs e notas internas confidenciais para todos os perfis de usuário.
*   **Insecure Storage:** Uso do `localStorage` para persistência de dados sensíveis e credenciais em texto claro no código-fonte.
*   **Exposição de Segredos em Exportação:** O sistema exportava o token da API e a base de usuários completa (incluindo senhas) em arquivos JSON.

## 3. Melhorias Implementadas
Foram aplicados os seguintes controles de segurança no arquivo `app.js`:

### A. Sanitização de Inputs
Implementação da função `sanitizeHTML` para neutralizar caracteres especiais em todos os campos de texto, mitigando vetores de ataque XSS.

### B. Controle de Acesso Baseado em Funções (RBAC Simulado)
*   **Restrição de Escopo:** Alunos agora só podem visualizar ocorrências vinculadas ao seu próprio nome.
*   **Privilégios Administrativos:** As funções de `delete` e `changeStatus` foram protegidas por verificações de perfil, sendo restritas a `ADMIN` e `PROFESSOR`.

### C. Mascaramento e Minimização de Dados
*   **Data Masking:** O CPF dos estudantes agora é mascarado (`***.***.***-**`) para perfis não-administrativos.
*   **Sigilo de Notas:** O campo "Observação Interna" é ocultado para o perfil `ALUNO`, garantindo a confidencialidade da comunicação administrativa.

### D. Fortalecimento da Rastreabilidade
Os logs de auditoria foram reestruturados para incluir detalhes mais precisos sobre o autor da ação e o contexto do evento, dificultando a negação de ações (non-repudiation).

### E. Higienização de Exportações
A função de exportação foi modificada para remover o `FAKE_API_TOKEN` e a lista `USERS`. Agora, o arquivo JSON contém apenas os dados operacionais (ocorrências e logs), protegendo as credenciais do sistema.

## 4. Limitações e Recomendações
Como o projeto é estritamente *front-end*, as seguintes limitações persistem:
1.  **Segurança de Sessão:** A autenticação é apenas visual; um usuário avançado pode manipular o `localStorage` para elevar privilégios.
2.  **Persistência:** Os dados não possuem integridade garantida por um banco de dados relacional com ACID.

**Recomendação Técnica:** Para uso em produção, é imperativo migrar a lógica de autorização e o armazenamento para um ambiente *back-end* seguro, utilizando tokens JWT para gestão de sessão e criptografia de dados em repouso.

## 5. Como Executar
1.  Clone o repositório: `https://github.com/RodrigoBonif/Melhoria-de-seguran-a-WEB`
2.  Acesse a versão publicada: [https://melhoria-de-seguran-a-web.vercel.app/](https://melhoria-de-seguran-a-web.vercel.app/)
3.  Abra o arquivo `index.html` em qualquer navegador moderno.
4.  Utilize os usuários de demonstração listados na tela de login para testar os diferentes níveis de acesso.

---

**Desenvolvido por:** 
*   Rodrigo Bonifácio Conceição
*   Ruan Pablo de Lima Pereira
*   Vinicius Clemente Negherbon

**Disciplina:** Segurança da Informação
**Turma:** Engenharia de Software - 5º Semestre
**Data:** 05 de Maio de 2026
