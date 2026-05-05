# Relatório Técnico: Análise e Melhoria de Segurança em Sistema Web

## 1. Identificação do Grupo
*   **Nome da Disciplina:** Segurança da Informação
*   **Nome dos Integrantes:** Rodrigo Bonifácio Conceição, Ruan Pablo de Lima Pereira, Vinicius Clemente Negherbon
*   **Turma:** Engenharia de Software - 5º Semestre
*   **Data:** 05 de Maio de 2026
*   **Link do Repositório:** https://github.com/RodrigoBonif/Melhoria-de-seguran-a-WEB
*   **Link do Sistema Publicado:** https://melhoria-de-seguran-a-web.vercel.app/

## 2. Descrição Resumida do Sistema
O sistema analisado é um protótipo de **Sistema de Ocorrências Acadêmicas**, desenvolvido para fins didáticos. Ele permite o registro, consulta e acompanhamento de demandas de alunos (como revisão de notas e contestações de frequência). O sistema possui três perfis de usuário: **Aluno**, **Professor** e **Administrador**. A aplicação foi construída utilizando apenas tecnologias de front-end (HTML, CSS e JavaScript), sem um back-end real ou banco de dados centralizado, utilizando o `localStorage` do navegador para persistência de dados.

## 3. Regras de Negócio Percebidas
Durante a análise, identificamos as seguintes regras (muitas delas apenas simuladas no front-end):
*   **Autenticação:** O login é feito comparando e-mails e senhas em uma lista estática no código.
*   **Perfis de Acesso:** Existem diferentes níveis de permissão, mas no código original, qualquer usuário podia realizar qualquer ação (como excluir registros ou ver notas internas).
*   **Gestão de Ocorrências:** Professores e Admins deveriam criar e gerenciar ocorrências, enquanto alunos deveriam apenas visualizar as suas.
*   **Auditoria:** O sistema possui uma área de logs que registra ações, mas esses logs podem ser apagados facilmente pelo usuário.

## 4. Identificação dos Ativos
| Item | Ativo Identificado | Por que este ativo tem valor? |
| :--- | :--- | :--- |
| 1 | Dados Pessoais dos Alunos | Contém nomes, CPFs, e-mails e telefones (protegidos pela LGPD). |
| 2 | Registros de Ocorrências | Informações sensíveis sobre o desempenho e comportamento acadêmico. |
| 3 | Notas Internas | Comentários da coordenação que não devem ser vistos pelos alunos. |
| 4 | Logs de Auditoria | Essenciais para rastrear quem fez o quê no sistema. |
| 5 | Código-Fonte (app.js) | Contém a lógica de negócio e "segredos" como tokens simulados. |

## 5. Classificação dos Dados e Ativos
| Dado/Ativo | Classificação | Justificativa |
| :--- | :--- | :--- |
| Nome do Aluno | Interno | Necessário para identificação dentro da instituição. |
| CPF do Aluno | Confidencial | Dado pessoal sensível que exige proteção contra exposição. |
| Observação Interna | Restrito | Apenas para professores e administradores. |
| Lista de Usuários | Confidencial | Contém credenciais de acesso (mesmo que simuladas). |

## 6. Análise de Riscos
Identificamos riscos críticos devido à arquitetura "apenas front-end":
1.  **Quebra de Confidencialidade:** Como os dados estão no `localStorage`, qualquer pessoa com acesso ao computador pode ler todos os registros.
2.  **Falta de Integridade:** O usuário pode alterar os dados diretamente no console do navegador, burlando as regras de negócio.
3.  **Ausência de Autenticação Real:** As senhas estão em texto claro no código-fonte.
4.  **Vulnerabilidade a XSS:** O sistema original não sanitizava as entradas, permitindo a execução de scripts maliciosos através dos campos de descrição.

## 7. Tabela de Melhorias e Justificativas
| Item Analisado | Risco Associado | Controle ou Melhoria Proposta | Foi Implementado? | Justificativa Técnica |
| :--- | :--- | :--- | :--- | :--- |
| Entrada de Dados | Ataque de XSS | Implementação de função `sanitizeHTML` para limpar inputs. | Sim | Evita a execução de scripts maliciosos injetados nos formulários. |
| Exposição de CPF | Vazamento de Dados | Mascaramento de CPF para perfis não-administradores. | Sim | Aplica o princípio da necessidade de saber (Need-to-know). |
| Notas Internas | Acesso Indevido | Restrição de visualização de notas internas para o perfil ALUNO. | Sim | Garante a confidencialidade de informações administrativas. |
| Exclusão de Dados | Perda de Integridade | Restrição da função de exclusão apenas para o perfil ADMIN. | Sim | Implementa o Princípio do Menor Privilégio. |
| Logs de Sistema | Falta de Rastreabilidade | Melhoria no detalhamento dos logs (quem e quando). | Sim | Melhora a capacidade de auditoria do sistema. |
| Armazenamento | Manipulação de Dados | Migração para um Banco de Dados real com API. | Dependeria de Back-end | O `localStorage` não é um meio seguro para dados sensíveis. |

## 8. Conclusão Técnica
**O sistema, da forma como foi recebido, poderia ser usado em produção com dados reais?**
**Não.** Embora o protótipo seja funcional para fins de demonstração, ele apresenta falhas graves de segurança que violam princípios básicos da Segurança da Informação e da LGPD. A principal limitação é a ausência de um back-end seguro. Em um ambiente real, as credenciais nunca devem ficar no front-end, e a autorização deve ser validada no servidor, não apenas na interface.

As melhorias implementadas no front-end (sanitização, mascaramento e restrições de UI) mitigam alguns riscos superficiais, mas a solução definitiva exige uma arquitetura cliente-servidor com banco de dados protegido e criptografia de dados em repouso.
