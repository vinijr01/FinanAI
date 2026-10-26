# Arquitetura do produto em desenvolvimento

## Agente principal

`WhatsApp -> Evolution API -> identificação e acesso -> tratamento da mensagem
-> buffer Redis -> orquestrador -> agente especializado -> ferramentas Supabase
-> resposta -> sincronização da planilha`.

O fluxo possui sete agentes especializados conectados ao orquestrador:
registrar transação, lembretes, metas, relatórios, atualizar, deletar e genérico.
O agente de relatórios possui a ferramenta que chama o workflow de dashboard.

O caminho de áudio usa uma requisição à Groq para transcrição. Imagens e PDFs
têm caminhos próprios de tratamento. A memória principal está ligada ao PostgreSQL.

Existe ainda um ramo agendado de lembretes, com consulta por data no fuso
America/Sao_Paulo, envio de mensagens e atualização do registro ou recorrência.

## Sincronização

`Dados do usuário -> localizar ou criar planilha -> consultar categorias,
transações, metas e lembretes no Supabase -> transformar registros -> atualizar
três abas -> consolidar resultado -> atualizar data da sincronização`.

As categorias são utilizadas para enriquecer as linhas com nomes e grupos.
O código prepara campos de apresentação como situação da meta, progresso,
valor restante e próximo aviso do lembrete.

O export usa limpeza e preenchimento das abas; não foi descrito como uma
sincronização incremental ou bidirecional.

## Dashboard

`Chamada do agente ou requisição web -> normalizar usuário -> localizar planilha
-> ler transações, metas e lembretes -> gerar HTML e resumo -> retornar página ou URL`.

A visualização atual utiliza Google Sheets como fonte de leitura, alimentado
a partir do Supabase. O renderer contém navegação para início, lançamentos,
metas e lembretes, além de gráficos e filtros.

## Responsabilidades dos dados

| Estrutura referenciada nos fluxos | Uso |
| --- | --- |
| usuarios | Identificação, acesso e associação com a planilha |
| transacoes | Entradas, saídas, categoria e pagamento |
| categorias | Classificação dos registros |
| lembretes | Compromissos, antecedência, recorrência e situação |
| metas | Objetivos, limites, valores e prazos |
| memória PostgreSQL | Contexto da conversa por sessão |

Os nomes e campos vêm dos nós dos workflows. Eles não substituem o esquema
completo e atual do banco. DDLs auxiliares encontrados no export incluem chaves
primárias e referências a usuários, mas não comprovam todas as migrações atuais.

Recursos de cartões e faturas estão em evolução e não foram representados como
um agente funcional nos três exports recebidos.

## Escopo da verificação desta apresentação

Foram lidas as configurações e conexões dos três exports e renderizada a
interface com um conjunto local de dados fictícios. Essa verificação não
acionou WhatsApp, Supabase, planilhas reais ou dados dos participantes.
O uso com cinco pessoas e o feedback apresentado foram informados pelo desenvolvedor.
