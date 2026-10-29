# Discovery: Aplicação de Previsão do Tempo

## Contexto

A empresa solicitou uma aplicação para que usuários consultem as condições meteorológicas de cidades. O produto deve permitir buscar cidades, visualizar o clima atual e a previsão para cinco dias, alternar entre Celsius e Fahrenheit e ser utilizável em dispositivos móveis.

O briefing define as capacidades principais, mas não especifica a fonte dos dados, os detalhes da previsão, os critérios de busca nem os níveis esperados de desempenho e acessibilidade.

## Requisitos Funcionais

- **RF-01 — Buscar cidade:** o usuário deve poder pesquisar uma cidade pelo nome e selecionar o local desejado entre os resultados.
- **RF-02 — Consultar clima atual:** após selecionar uma cidade, a aplicação deve exibir suas condições meteorológicas atuais.
- **RF-03 — Consultar previsão:** a aplicação deve exibir a previsão para cinco dias da cidade selecionada.
- **RF-04 — Alternar unidades:** o usuário deve poder alternar a temperatura exibida entre Celsius e Fahrenheit.
- **RF-05 — Usar em dispositivos móveis:** as funcionalidades de busca, consulta e troca de unidade devem estar disponíveis em telas móveis.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade:** conteúdo e controles devem se adaptar a diferentes tamanhos de tela, incluindo dispositivos móveis.
- **RNF-02 — Usabilidade:** a cidade consultada e a unidade de temperatura ativa devem estar claras para o usuário.
- **RNF-03 — Acessibilidade:** controles devem ser acessíveis por teclado e ter nomes ou rótulos compreensíveis para tecnologias assistivas.
- **RNF-04 — Resiliência:** a aplicação deve comunicar falhas de busca ou de consulta meteorológica sem apresentar dados incorretos como atuais.
- **RNF-05 — Desempenho percebido:** buscas e consultas em andamento devem ter seu estado indicado ao usuário. Limites de tempo de resposta precisam ser definidos.

## Riscos

- **Dependência de dados externos:** indisponibilidade, lentidão ou limites de uso do provedor podem impedir consultas.
- **Ambiguidade na busca:** cidades com nomes iguais ou semelhantes podem levar à seleção de um local incorreto.
- **Diferenças de fuso horário:** a data da previsão pode variar conforme o local e o fuso horário considerado.
- **Conversão e arredondamento:** diferenças na regra de arredondamento podem causar inconsistências ao alternar entre escalas.
- **Conectividade móvel:** redes instáveis podem interromper buscas e consultas.

## Decisões

- **Fonte de dados — Open-Meteo, sem API key:** usar a Open-Meteo para obter dados meteorológicos, sem exigir chave de API. Isso evita a necessidade de gerenciar credenciais para essa integração e resolve a pergunta sobre qual provedor utilizar. A cobertura, os dados necessários e os limites de uso ainda devem ser confirmados.
- **Período da previsão — hoje + quatro dias:** considerar cinco dias do calendário, incluindo hoje e os quatro dias seguintes. Isso resolve a ambiguidade entre cinco dias corridos e as próximas 120 horas. A data deve ser interpretada no contexto do local consultado.
- **Unidade padrão — Celsius:** exibir temperaturas em Celsius inicialmente. Isso define o estado inicial e resolve a pergunta sobre a unidade padrão; não define se a escolha posterior do usuário será lembrada.
- **Sem autenticação e sem persistência de servidor:** não exigir conta e não armazenar dados do usuário em servidor. Isso fecha o escopo de autenticação e armazenamento server-side, reduzindo requisitos de backend. A persistência local da unidade ou de buscas continua em aberto.
- **Idioma da interface — pt-BR:** apresentar a interface em português do Brasil. Isso resolve a escolha do idioma da UI; idiomas aceitos na busca e compatibilidade de navegadores permanecem em aberto.

## Perguntas em Aberto

- A busca deve aceitar apenas nomes de cidades ou também países, regiões e códigos postais?
- Como diferenciar resultados de cidades homônimas?
- Quais informações devem aparecer na previsão, além da temperatura (por exemplo, mínima, máxima e condição do tempo)?
- A aplicação deve abrir com uma cidade padrão, solicitar localização do dispositivo ou aguardar uma busca?
- A unidade escolhida pelo usuário deve ser lembrada localmente durante a sessão ou entre visitas?
- Quais navegadores, versões e dispositivos devem ser suportados?
- Quais são os limites aceitáveis para tempo de resposta, disponibilidade e atualização dos dados?
- Quais são os limites aceitáveis para tempo de resposta, disponibilidade e atualização dos dados?

## Suposições

- A consulta será feita por cidade, sem exigir criação de conta.
- O usuário selecionará a cidade para a qual deseja consultar os dados.
- Celsius e Fahrenheit são unidades de apresentação; alterná-las não deve mudar a cidade consultada.
- A aplicação dependerá de um serviço externo para obter localização e dados meteorológicos.
- O requisito de uso em dispositivos móveis será atendido por uma interface responsiva, sem exigir aplicativo nativo.
- Localização automática, favoritos, alertas meteorológicos e funcionamento offline não estão confirmados no escopo do briefing.