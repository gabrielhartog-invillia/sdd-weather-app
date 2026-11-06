# Especificação de Produto — Weather App

**Fonte:** [Discovery](./discovery.md) e solução de referência do módulo 03.
**Status:** baseline funcional definida para implementação e validação.

## Overview

Aplicação web SPA, mobile-first, para buscar uma cidade e consultar o clima
atual e a previsão de cinco dias. A interface usa tema dark glassmorphism,
idioma pt-BR e permite alternar temperaturas entre Celsius e Fahrenheit. Não
exige login nem chave de API; os dados são fornecidos pela Open-Meteo e a
aplicação é publicada como site estático no GitHub Pages.

### Decisões de produto

| Tema | Decisão |
|---|---|
| Plataforma e publicação | SPA web responsiva, mobile-first, publicação estática no GitHub Pages. |
| Fonte de dados | Open-Meteo, sem API key. |
| Busca | Consulta por nome de cidade; sugestões mostram cidade, estado/região e país quando disponíveis. A seleção é explícita. |
| Período | Cinco datas consecutivas no fuso horário da cidade selecionada: hoje e os quatro dias seguintes. |
| Clima atual | Temperatura, condição, umidade, vento, pressão e precipitação. |
| Previsão diária | Temperaturas mínima e máxima, condição e precipitação em cada dia. |
| Unidades | Celsius padrão; alternância para Fahrenheit afeta todos os valores de temperatura sem nova requisição. Vento em km/h, pressão em hPa, precipitação em mm e umidade em %. |
| Conta e persistência | Sem autenticação ou persistência de servidor. A unidade volta a Celsius ao iniciar uma nova visita. |
| Interface | pt-BR, tema dark glassmorphism. |

### Personas

As personas são hipóteses do discovery, usadas aqui para contextualizar as
stories.

- **Marina — usuária cotidiana:** consulta o clima rapidamente antes de sair.
- **Rafael — planejador de atividades:** usa a previsão para organizar
  atividades ao ar livre.
- **Alex — viajante:** consulta cidades diferentes e prefere compreender
  temperaturas na unidade escolhida.

## Functional Requirements

| ID | Requisito | Comportamento |
|---|---|---|
| RF-01 | Buscar e selecionar cidade | Permitir busca por nome, apresentar sugestões com contexto de estado/região e país quando fornecido e consultar o clima somente após seleção explícita. |
| RF-02 | Consultar clima atual | Exibir temperatura, condição, umidade, vento, pressão e precipitação da cidade selecionada. |
| RF-03 | Consultar previsão de cinco dias | Exibir exatamente hoje e os quatro dias seguintes, com mínima, máxima, condição e precipitação por dia. |
| RF-04 | Alternar unidade de temperatura | Alternar Celsius/Fahrenheit para todos os valores exibidos sem nova requisição, preservando cidade e condições. |
| RF-05 | Comunicar estados da interface | Apresentar estados de carregamento, vazio e erro. Erros de requisição oferecem ação de tentar novamente. |

## User Stories

| ID | Story | Rastreabilidade |
|---|---|---|
| US-01 | Como Marina, quero buscar minha cidade para ver o clima atual rapidamente. | RF-01, RF-02 |
| US-02 | Como Rafael, quero ver a previsão de cinco dias para organizar atividades ao ar livre. | RF-03 |
| US-03 | Como Alex, quero alternar entre °C e °F para entender a temperatura na unidade que conheço. | RF-04 |
| US-04 | Como Marina, quero usar uma interface responsiva no celular para consultar o clima com conforto. | RNF-01 |
| US-05 | Como Marina, quero receber uma mensagem clara e poder tentar novamente quando uma requisição falhar. | RF-05 |

## Acceptance Criteria

Executar os cenários com respostas de API controladas; testes não devem depender
da disponibilidade da Open-Meteo nem de previsões variáveis.

### RF-01 / US-01 — Busca de cidade

- **AC-01**
  - **Given** uma busca com resultados de geocoding
  - **When** a busca termina
  - **Then** cada sugestão exibe o nome da cidade e o estado/região e país quando disponíveis; nenhuma cidade é selecionada automaticamente.
- **AC-02**
  - **Given** sugestões que incluem cidades homônimas
  - **When** o usuário seleciona uma sugestão
  - **Then** o contexto exibido distingue as localidades e a consulta meteorológica usa as coordenadas da sugestão selecionada.
- **AC-03**
  - **Given** um campo de busca vazio ou contendo apenas espaços
  - **When** o usuário envia a busca
  - **Then** a aplicação orienta a informar uma cidade e não envia requisição de geocoding ou meteorológica.
- **AC-04**
  - **Given** uma busca por cidade inexistente ou uma resposta de geocoding sem resultados
  - **When** a busca termina
  - **Then** é exibido um estado vazio informando que nenhuma cidade foi encontrada; nenhuma requisição meteorológica é enviada e o usuário pode editar a busca.
- **AC-05**
  - **Given** consultas equivalentes como “São Paulo”, “sao paulo” e “  SÃO PAULO  ”
  - **When** cada consulta é enviada
  - **Then** espaços externos são removidos, a busca não diferencia acentos ou caixa e todas as variantes retornam os resultados equivalentes do mesmo fixture de geocoding.

### RF-02 — Clima atual

- **AC-06**
  - **Given** uma cidade selecionada e uma resposta válida
  - **When** os dados atuais são exibidos
  - **Then** são apresentados temperatura com unidade, condição, umidade (%), vento (km/h), pressão (hPa) e precipitação (mm), todos associados à cidade selecionada.
- **AC-07**
  - **Given** uma resposta atual em que um ou mais campos estão ausentes ou nulos
  - **When** os dados são exibidos
  - **Then** cada campo ausente mostra “—”; os demais campos válidos continuam visíveis e o layout permanece utilizável.

### RF-03 / US-02 — Previsão

- **AC-08**
  - **Given** uma cidade selecionada e uma resposta diária completa
  - **When** a previsão é exibida
  - **Then** são mostradas exatamente cinco datas consecutivas no fuso horário da cidade selecionada, de hoje até hoje + 4, cada uma com mínima, máxima, condição e precipitação.
- **AC-09**
  - **Given** uma resposta de previsão em que um campo ou valor de um dia está ausente ou nulo
  - **When** a previsão é exibida
  - **Then** o campo ausente mostra “—”, os demais valores válidos são exibidos e nenhuma data é removida, deslocada ou preenchida com dado inventado.

### RF-04 / US-03 — Unidade

- **AC-10**
  - **Given** temperaturas exibidas em Celsius
  - **When** o usuário alterna para Fahrenheit
  - **Then** todas as temperaturas atuais e previstas são convertidas usando °F = (°C × 9/5) + 32, a unidade é atualizada imediatamente e nenhuma requisição de rede é enviada.
- **AC-11**
  - **Given** temperaturas exibidas em Fahrenheit
  - **When** o usuário alterna de volta para Celsius
  - **Then** os valores de origem são restaurados, cidade e demais condições permanecem inalteradas e nenhuma requisição é enviada.

### RF-05 / US-05 — Loading, vazio, erro e recuperação

- **AC-12**
  - **Given** uma requisição de geocoding ou meteorológica pendente
  - **When** a resposta ainda não chegou
  - **Then** a interface mostra um indicador de carregamento; ao receber resposta ou falha, o indicador é removido.
- **AC-13**
  - **Given** uma falha de API, rede ou timeout em uma requisição
  - **When** a falha é recebida
  - **Then** a interface apresenta mensagem de erro e ação “Tentar novamente”; não permanece carregando nem apresenta a falha como dado válido.
- **AC-14**
  - **Given** uma mensagem de erro e uma ação “Tentar novamente”
  - **When** o usuário aciona a ação
  - **Then** a mesma operação é requisitada novamente; durante a nova tentativa aparece loading e a interface apresenta o resultado correspondente ao término.
- **AC-15**
  - **Given** uma resposta meteorológica parcial com campos ausentes
  - **When** os dados são renderizados
  - **Then** cada campo ausente é representado por “—”, os dados válidos permanecem disponíveis e a interface não quebra.

## Non-Functional Requirements

| ID | Requisito | Verificação |
|---|---|---|
| RNF-01 | Responsividade mobile-first, de 320 px a desktop, sem rolagem horizontal que impeça o uso. | Verificar busca, sugestões, clima, previsão e estados de loading/erro em viewport de 320 px e em desktop; validar retrato e paisagem móvel. |
| RNF-02 | Desempenho: carga inicial inferior a 2 segundos. | Medir do início da navegação até a interface central estar utilizável em uma execução de produção do site estático. |
| RNF-03 | Acessibilidade: navegação por teclado, roles e labels acessíveis, foco visível e contraste WCAG AA. | Verificar fluxo completo por teclado, nomes acessíveis dos controles, foco e contraste por checagem automatizada e revisão manual. |
| RNF-04 | Resiliência a falhas de rede e API. | Simular erro, timeout e resposta parcial; a interface termina loading, comunica o estado e permite recuperação conforme AC-12 a AC-15. |
| RNF-05 | Sem API key e deploy 100% estático no GitHub Pages. | Confirmar ausência de chave secreta necessária à execução e validar que o build de produção pode ser servido como arquivos estáticos. |
| RNF-06 | Qualidade: lint, build e testes passam no CI. | O pipeline de CI executa `pnpm lint`, `pnpm build` e `pnpm test`; qualquer etapa com falha impede o resultado verde do pipeline. |

## Edge Cases

| Caso | Comportamento esperado | Referência |
|---|---|---|
| Cidade inexistente | Mostrar “Nenhuma cidade encontrada”; não solicitar dados meteorológicos. | AC-04 |
| Input vazio ou só espaços | Orientar o usuário; não disparar busca. | AC-03 |
| Acentos, caixa ou espaços excedentes | Normalizar a busca sem erro; manter caracteres válidos. | AC-05 |
| Falha da API de forecast | Exibir erro e ação “Tentar novamente”. | AC-13, AC-14 |
| Timeout de rede | Encerrar loading e exibir erro com tentativa novamente. | AC-13, AC-14 |
| Geocoding sem resultados | Exibir estado vazio informativo, distinto de erro de rede. | AC-04, AC-13 |
| Campo ausente em resposta parcial | Exibir “—” no campo, mostrar os demais dados válidos e manter o layout utilizável. | AC-07, AC-09, AC-15 |
| Cidades homônimas | Mostrar estado/região e país quando disponíveis; exigir seleção explícita. | AC-01, AC-02 |

## Assumptions

- A conexão com a internet está disponível na maior parte do tempo; falhas são
  tratadas conforme RF-05.
- O público usa navegadores modernos evergreen.
- O produto é de uso individual e não exige login.
- Os dados meteorológicos são fornecidos pela Open-Meteo; não são produzidos
  pela aplicação.

## Risks

| Risco | Mitigação |
|---|---|
| Rate limiting ou indisponibilidade da Open-Meteo | Comunicar erro e permitir nova tentativa; não apresentar dados inválidos como sucesso. |
| Cidades homônimas | Exibir estado/região e país nas sugestões quando disponíveis. |
| Conversão de unidade incorreta | Implementar conversão como função pura e cobrir com testes para ambas as unidades. |
| Inconsistência mobile/desktop | Validar os fluxos em viewport mobile e desktop, incluindo testes E2E. |

## Out of Scope (v1)

- Autenticação e contas de usuário.
- Favoritos e histórico persistente.
- Geolocalização automática.
- Internacionalização além de pt-BR.
- Notificações push e alertas meteorológicos.
- Edição ou produção própria de dados meteorológicos.
- Funcionamento offline garantido.
- PWA ou aplicativos nativos; a entrega v1 é uma SPA web estática.
- Previsões além dos cinco dias definidos ou previsões horárias.

## Open Questions

Nenhuma pergunta bloqueante. As decisões funcionais e de entrega desta versão
estão definidas neste documento; alterações de escopo devem atualizar RFs,
critérios de aceite e rastreabilidade antes da implementação correspondente.

## Rastreabilidade

| Story | Requisito | Critérios |
|---|---|---|
| US-01 | RF-01, RF-02 | AC-01 a AC-07 |
| US-02 | RF-03 | AC-08, AC-09 |
| US-03 | RF-04 | AC-10, AC-11 |
| US-04 | RNF-01 | Verificação RNF-01 |
| US-05 | RF-05 | AC-12 a AC-15 |
