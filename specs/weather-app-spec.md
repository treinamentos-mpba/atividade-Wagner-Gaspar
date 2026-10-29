# Especificação do Produto: Aplicação de Previsão do Tempo

## Overview

Aplicação web responsiva, em português do Brasil, para consultar clima atual e previsão para cinco dias de uma cidade escolhida manualmente. A pessoa usuária pesquisa e seleciona a localidade, vê temperatura e condições meteorológicas e alterna entre Celsius e Fahrenheit.

A Open-Meteo é a fonte de geocoding e meteorologia. A aplicação não exige conta, não solicita localização do dispositivo e não armazena buscas em servidor. A unidade escolhida é salva localmente no navegador. A tela inicial aguarda uma busca manual.

## Functional Requirements

- **RF-01 — Buscar e selecionar cidade:** aceitar nome de cidade ou cidade acompanhada de região/país, com letras Unicode, acentos, espaços, apóstrofos e hífens. Remover espaços externos e normalizar Unicode NFC. Input vazio ou somente espaços não gera chamada. Exibir até 10 resultados na ordem retornada pelo geocoding, com nome da cidade, região administrativa e país quando disponíveis; acrescentar coordenadas com quatro casas decimais quando esses rótulos não distinguirem resultados. Códigos postais e endereços não são formatos de busca suportados.
- **RF-02 — Consultar clima atual:** ao selecionar uma localidade, exibir `current.temperature_2m`, `current.apparent_temperature` e `current.weather_code` da Open-Meteo, além de `current.time` convertido para horário local no formato 24 horas (`HH:mm`). Mapear `weather_code` conforme os códigos WMO para rótulo em pt-BR; código desconhecido aparece como “Condição indisponível”. Os dados são válidos como atuais somente se `current.time`, convertido para UTC, tiver no máximo duas horas de idade em relação ao relógio da consulta. Incluir ação explícita para atualizar a consulta.
- **RF-03 — Consultar previsão de cinco dias:** exibir hoje e os quatro dias de calendário seguintes no fuso (`timezone`) da localidade selecionada. Para cada dia de `daily.time`, exibir condição (`daily.weather_code`), temperatura mínima (`daily.temperature_2m_min`), máxima (`daily.temperature_2m_max`) e probabilidade máxima de precipitação (`daily.precipitation_probability_max`) como porcentagem inteira seguida de `%`, arredondada para o inteiro mais próximo com empate para longe de zero. Mapear códigos WMO para rótulos em pt-BR; código desconhecido aparece como “Condição indisponível”. Uma data ou campo sem dado válido deve ser identificado como indisponível.
- **RF-04 — Alternar unidade de temperatura:** iniciar em Celsius quando não houver preferência salva; alternar todas as temperaturas atuais e diárias entre Celsius e Fahrenheit. Converter a partir do valor não arredondado, exibir uma casa decimal com arredondamento de empate para longe de zero e salvar a unidade no armazenamento local do navegador. A conversão não altera local ou datas.
- **RF-05 — Usar as funções em dispositivos móveis:** permitir o fluxo completo de busca, seleção, consulta atual e diária e troca de unidade em larguras de 320 a 1920 pixels CSS, sem perda de conteúdo ou controles.
- **RF-06 — Comunicar estados e recuperar falhas:** indicar carregamento, vazio, sucesso, falha e timeout como estados distintos. Limitar geocoding a 8 segundos e consulta/atualização meteorológica a 10 segundos. Em falha ou timeout, permitir nova tentativa manual e não apresentar dados de outra localidade como atuais. Não fazer polling ou retry automático.

## User Stories

- **US-01 (RF-01):** Como usuário que consulta o tempo de uma cidade, quero pesquisar pelo nome e selecionar o local desejado para consultar dados daquele lugar.
- **US-02 (RF-02):** Como usuário que consulta o tempo de uma cidade, quero visualizar as condições meteorológicas atuais do local selecionado para saber como está o tempo.
- **US-03 (RF-03):** Como usuário que consulta o tempo de uma cidade, quero ver a previsão de hoje e dos quatro dias seguintes para me preparar para os próximos dias.
- **US-04 (RF-04):** Como usuário que consulta temperaturas, quero alternar entre Celsius e Fahrenheit para visualizar os valores na unidade que prefiro.
- **US-05 (RF-05):** Como usuário em um dispositivo móvel, quero buscar uma cidade, consultar o clima e alternar a unidade para usar as funções da aplicação fora do computador.
- **US-06 (RF-06):** Como usuário que realiza uma busca ou consulta meteorológica, quero saber quando a operação está em andamento, não encontra resultados ou falha para compreender o estado da informação.

## Acceptance Criteria

As respostas externas devem ser controladas em testes automatizados. Datas e horários dos cenários usam relógio fixo e o fuso retornado pelo fixture.

- **CA-01.1 (RF-01):** **Given** input vazio ou somente espaços, **When** a pessoa usuária envia a busca, **Then** a interface informa que o nome da cidade é obrigatório e nenhuma requisição de geocoding é enviada.
- **CA-01.2 (RF-01):** **Given** o termo `São João d'El-Rei` com espaços externos, **When** a pessoa usuária envia a busca, **Then** os espaços externos são removidos, o termo é normalizado em NFC e acentos, apóstrofo e hífen são preservados no parâmetro enviado.
- **CA-01.3 (RF-01):** **Given** resposta ordenada com 12 localidades, **When** os resultados são exibidos, **Then** são apresentados os primeiros 10 na mesma ordem, cada um com cidade, região e país disponíveis.
- **CA-01.4 (RF-01):** **Given** dois resultados homônimos com a mesma região e país, **When** os resultados são exibidos, **Then** cada um mostra coordenadas com quatro casas decimais para distingui-los.
- **CA-01.5 (RF-01):** **Given** dois resultados com coordenadas distintas, **When** a pessoa seleciona o segundo, **Then** a consulta meteorológica usa as coordenadas do segundo resultado e a interface identifica esse local.
- **CA-02.1 (RF-02):** **Given** local selecionado e resposta válida com temperatura, sensação térmica, código WMO conhecido e `current.time`, **When** a consulta termina, **Then** os três dados meteorológicos são exibidos, a condição tem rótulo em pt-BR e o horário local aparece em formato 24 horas (`HH:mm`).
- **CA-02.2 (RF-02):** **Given** resposta cujo `current.time` convertido para UTC tem até duas horas de idade em relação ao relógio do teste, **When** ela é recebida, **Then** as condições atuais são exibidas como recentes.
- **CA-02.3 (RF-02):** **Given** resposta cujo `current.time` convertido para UTC tem mais de duas horas de idade, **When** ela é recebida, **Then** os valores não são apresentados como atuais e a interface informa que não há dados recentes.
- **CA-02.4 (RF-02):** **Given** código WMO não mapeado, **When** a condição atual é exibida, **Then** o rótulo é “Condição indisponível” em português do Brasil.
- **CA-02.5 (RF-02):** **Given** resposta atual sem sensação térmica, mas com localização, temperatura e `current.time` recentes, **When** a consulta termina, **Then** sensação térmica é identificada como indisponível e os valores presentes são marcados como parciais.
- **CA-02.6 (RF-02):** **Given** clima exibido para uma localidade, **When** a pessoa aciona Atualizar, **Then** uma nova consulta é feita para as mesmas coordenadas e os dados só são substituídos após resposta válida.
- **CA-02.7 (RF-02):** **Given** resposta atual sem `current.temperature_2m`, mas com localização e `current.time` recentes, **When** a consulta termina, **Then** a temperatura é identificada como indisponível e a consulta não é apresentada como completa.
- **CA-03.1 (RF-03):** **Given** relógio fixo, data local e fuso conhecidos, **When** a previsão é exibida, **Then** aparecem exatamente cinco datas consecutivas, de hoje local até o quarto dia seguinte.
- **CA-03.2 (RF-03):** **Given** fixture diário com códigos WMO, mínima, máxima e probabilidade de precipitação distintas por data, incluindo probabilidade de 43.5, **When** a previsão é exibida, **Then** cada data mostra a condição mapeada para pt-BR, temperaturas com unidade e a probabilidade como porcentagem inteira seguida de `%`, com 43.5 exibido como 44%.
- **CA-03.3 (RF-03):** **Given** que falta um campo ou dia na resposta, **When** a previsão é exibida, **Then** a lacuna é identificada como indisponível, sem valor inventado, e os demais dados válidos permanecem associados às datas corretas.
- **CA-03.4 (RF-03):** **Given** código WMO diário não mapeado, **When** a data é exibida, **Then** a condição aparece como “Condição indisponível” em português do Brasil.
- **CA-04.1 (RF-04):** **Given** ausência de preferência salva, **When** a aplicação é aberta, **Then** Celsius está ativo; após selecionar Fahrenheit e recarregar a aplicação, **Then** Fahrenheit continua ativo.
- **CA-04.2 (RF-04):** **Given** valores atuais e diários em Celsius, incluindo 0 °C e 100 °C, **When** Fahrenheit é selecionado, **Then** são exibidos 32.0 °F e 212.0 °F, todas as temperaturas diárias são convertidas e local e datas não mudam.
- **CA-04.3 (RF-04):** **Given** temperatura de 0.25 °C, **When** a unidade Fahrenheit é selecionada e o valor é arredondado para uma casa decimal, **Then** o resultado é 32.5 °F.
- **CA-05.1 (RF-05):** **Given** viewports de 320 x 568 e 390 x 844 pixels CSS, **When** a pessoa completa busca, seleção, consulta e troca de unidade, **Then** o fluxo é operável sem rolagem horizontal ou controle essencial cortado.
- **CA-05.2 (RF-05):** **Given** viewport de 1280 x 720 pixels CSS, **When** o fluxo principal é executado, **Then** busca, clima atual, cinco dias e unidade permanecem acessíveis sem sobreposição.
- **CA-06.1 (RF-06):** **Given** resposta pendente de busca, consulta inicial ou atualização manual, **When** a operação começa, **Then** o carregamento é visível antes da resposta e termina em sucesso, falha ou timeout.
- **CA-06.2 (RF-06):** **Given** geocoding pendente por 8 segundos ou consulta meteorológica pendente por 10 segundos, **When** o limite é atingido, **Then** a operação é encerrada, timeout é anunciado como erro recuperável e uma nova tentativa pode ser iniciada.
- **CA-06.3 (RF-06):** **Given** falha de rede ou resposta HTTP de erro, **When** a falha é processada, **Then** a mensagem em pt-BR é distinta do estado vazio e nova tentativa só ocorre após ação explícita da pessoa usuária.
- **CA-NF-01 (RNF-03):** **Given** foco no primeiro controle, **When** a pessoa opera busca, resultados, atualização e unidade somente pelo teclado, **Then** todos são alcançáveis, têm foco visível e podem ser acionados sem mouse.
- **CA-NF-02 (RNF-03):** **Given** transição para carregamento, vazio, sucesso ou erro, **When** o estado muda, **Then** ele tem nome acessível e é anunciado por tecnologia assistiva, sem depender somente de cor.

## Non-Functional Requirements

- **RNF-01 — Responsividade:** o fluxo principal deve funcionar em larguras de 320 a 1920 pixels CSS nos navegadores definidos em Assumptions, sem perda de conteúdo ou controles.
- **RNF-02 — Contexto:** cidade selecionada, unidade ativa e estado da consulta devem permanecer identificáveis durante a exibição dos dados.
- **RNF-03 — Acessibilidade:** cumprir WCAG 2.2 nível AA no fluxo principal; oferecer nomes acessíveis, foco visível, operação por teclado e anúncio de estados. CA-NF-01 e CA-NF-02 são verificações mínimas, complementadas por auditoria automatizada e manual.
- **RNF-04 — Integridade:** dados de outra localidade, inválidos, incompletos ou com mais de duas horas não podem ser apresentados como clima atual válido.
- **RNF-05 — Desempenho e resiliência:** exibir carregamento imediatamente após a ação; aplicar timeouts de 8 segundos para geocoding e 10 segundos para meteorologia; não fazer polling nem retry automático. A disponibilidade do provedor externo não é garantida pela aplicação.
- **RNF-06 — Idioma:** textos, validações, condições meteorológicas e estados da interface devem estar em português do Brasil.
- **RNF-07 — Privacidade:** não exigir conta nem persistir buscas ou dados meteorológicos em servidor. Persistir somente a unidade escolhida no armazenamento local do navegador.

## Edge Cases

- **Cidade sem correspondência:** resposta válida do geocoding sem resultados produz estado vazio, mantém o termo editável e não inicia consulta meteorológica.
- **Input inválido:** input vazio ou somente espaços não é enviado; nomes Unicode são normalizados em NFC e acentos, apóstrofos e hífens são preservados após remoção de espaços externos.
- **Resultados homônimos:** exibir cidade, região e país; se os rótulos não distinguirem os resultados, acrescentar coordenadas com duas casas decimais.
- **Falha de serviço:** distinguir erro de geocoding/API de resposta válida sem resultados, informar a falha e oferecer nova tentativa.
- **Timeout:** geocoding expira em 8 segundos e consulta meteorológica em 10; encerrar a operação e oferecer nova tentativa.
- **Resposta parcial ou inválida:** marcar cada campo ou data ausente e não inventar valores. Sem identidade de local ou estrutura válida, tratar a consulta inteira como falha.
- **Dados antigos:** se `current.time` exceder duas horas, não exibir condições atuais como válidas e permitir atualização manual.
- **Mudança de data/fuso:** produzir as cinco datas consecutivas usando o fuso local do resultado selecionado, inclusive quando a consulta ocorre perto da meia-noite.
- **Falha após consulta anterior:** manter a identificação da nova cidade, exibir o estado de erro e nunca rotular valores anteriores como atuais para ela.
- **Troca de unidade durante consulta:** atualizar as temperaturas atuais e diárias sem alterar cidade ou datas.
- **Conectividade móvel:** em falha de rede, encerrar a operação com erro recuperável; não simular sucesso nem bloquear nova busca.

## Assumptions

- A consulta começa por busca manual; não há cidade inicial nem geolocalização automática.
- A Open-Meteo fornece geocoding e dados meteorológicos sem chave de API.
- A busca aceita nomes de cidade ou cidade com região/país em Unicode; não aceita código postal nem endereço. A cobertura de nomes depende do catálogo do provedor.
- Resultados são apresentados em português do Brasil quando houver tradução fornecida; caso contrário, usa-se o nome retornado pelo provedor.
- **Rótulos WMO:** 0 céu limpo; 1 predominantemente limpo; 2 parcialmente nublado; 3 encoberto; 45/48 nevoeiro; 51/53/55 garoa; 56/57 garoa congelante; 61/63/65 chuva; 66/67 chuva congelante; 71/73/75 neve; 77 grãos de neve; 80/81/82 pancadas de chuva; 85/86 pancadas de neve; 95 trovoada; 96/99 trovoada com granizo. Qualquer outro código é “Condição indisponível”.
- “Cinco dias” significa hoje local e os quatro dias seguintes, não as próximas 120 horas.
- Celsius é padrão; a unidade selecionada persiste entre visitas somente no navegador local.
- A consulta não exige conta e não persiste buscas nem dados meteorológicos em servidor.
- As chamadas ocorrem somente após ações do usuário, sem polling ou retry automático, e devem respeitar a política pública de uso da Open-Meteo.
- Compatibilidade: versões estáveis atuais e imediatamente anteriores de Chrome, Edge e Firefox para desktop, Safari para macOS, Safari para iOS e Chrome para Android; larguras de 320 a 1920 pixels CSS.

## Risks

- **Indisponibilidade ou limite da Open-Meteo:** consultas podem falhar ou ser limitadas; solicitar dados somente após ações do usuário, sem polling/retry automático, respeitar a política pública e apresentar falha recuperável.
- **Homônimos ou catálogo incompleto:** nomes iguais ou ausentes podem impedir seleção correta; exibir metadados de localização e permitir que locais sem correspondência não possam ser consultados.
- **Fuso horário e mudança de data:** datas deslocadas podem resultar em previsão errada; usar o fuso do resultado e testar casos próximos à meia-noite.
- **Atualização do provedor:** `current.time` pode exceder duas horas; rejeitar como atual e permitir atualização manual.
- **Arredondamento:** diferenças entre escalas podem surgir se a conversão usar valores já formatados; converter valores não arredondados e arredondar somente na apresentação.
- **Conectividade móvel:** requisição interrompida pode deixar carregamento preso; encerrar no timeout/falha e manter retry manual disponível.

## Out of Scope

- Autenticação, contas de usuário e armazenamento de dados em servidor.
- Geolocalização automática ou solicitação de acesso à localização do dispositivo.
- Códigos postais, endereços, coordenadas inseridas manualmente e busca por localidades diferentes de cidades.
- Favoritos, histórico de buscas, comparação simultânea de cidades e alertas meteorológicos ou notificações push.
- Previsões horárias, mapas, radar, histórico climático, qualidade do ar, índices UV, clima marítimo e dados de poluição.
- Funcionamento offline, cache de dados meteorológicos e garantia de disponibilidade quando o provedor estiver indisponível.
- Aplicativos móveis nativos.
- Idiomas de interface além de português do Brasil e unidades além de Celsius/Fahrenheit.
- Exportação, compartilhamento, personalização de temas e analytics de comportamento.

## Open Questions

Não há perguntas bloqueantes para implementar a primeira versão dentro do escopo definido. As decisões sobre campos meteorológicos, busca, frescor, timeout, compatibilidade, persistência local e arredondamento estão fixadas nas seções acima. Mudanças no provedor, escopo ou política de disponibilidade exigem revisão desta especificação.