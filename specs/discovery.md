# Discovery — Aplicação de previsão do tempo

## Contexto

A empresa solicitou uma aplicação para que usuários consultem informações
meteorológicas de cidades. O produto deve permitir encontrar uma cidade, ver
as condições atuais e consultar a previsão para os próximos cinco dias. A
temperatura deve poder ser apresentada em Celsius ou Fahrenheit, e a experiência
deve funcionar em dispositivos móveis.

O briefing define o conjunto inicial de capacidades, mas ainda não determina
detalhes como público-alvo, critérios de busca, conteúdo meteorológico exibido,
ou metas de qualidade. A fonte inicial de dados já foi definida como
Open-Meteo; os demais pontos devem ser esclarecidos antes de fechar a
especificação.

## Decisões

| Decisão | Justificativa | Perguntas em aberto que resolve |
|---|---|---|
| **Fonte de dados: Open-Meteo, sem API key.** | Atende à necessidade de dados meteorológicos sem exigir uma chave de API para o fluxo definido. | Resolve a escolha inicial do provedor na pergunta 13. Permanecem pendentes a validação de cobertura, atualização, termos de uso e eventuais limites aplicáveis. |
| **Período da previsão: hoje mais os quatro dias seguintes.** | Torna explícito como contar os cinco dias e inclui o dia da consulta. | Resolve a contagem do período na pergunta 9. O fuso horário e a granularidade/conteúdo da previsão permanecem pendentes. |
| **Unidade padrão: Celsius.** | Define um padrão inicial coerente com a experiência em pt-BR. | Resolve a escolha da unidade inicial na pergunta 11. Permanecem pendentes as unidades das demais grandezas e a persistência da preferência. |
| **Sem autenticação e sem persistência de servidor.** | Mantém a experiência sem conta e evita depender de armazenamento de dados associado ao usuário no servidor. | Resolve a inclusão de autenticação no escopo do MVP (pergunta 4) e descarta persistência entre dispositivos por conta de usuário (pergunta 12). Regras de coleta/telemetria e armazenamento local ainda precisam ser definidas. |
| **Idioma da interface: pt-BR.** | Define o idioma inicial da experiência para o público brasileiro. | Resolve o idioma da interface na pergunta 17. Formatos regionais, cobertura geográfica e suporte a outros idiomas permanecem pendentes. |

## Personas

As personas abaixo são hipóteses iniciais derivadas do briefing, não segmentos
confirmados por pesquisa. Devem ser validadas com usuários reais antes de
orientar decisões finais de produto. Os sinais de sucesso descrevem o resultado
esperado por cada pessoa e podem servir de base para user stories e critérios de
aceite.

### Marina — usuária cotidiana

- **Objetivo principal:** consultar rapidamente o clima para decidir o que
  vestir ou como se deslocar.
- **Contexto de uso:** principalmente em dispositivo móvel, em consultas breves
  antes de sair de casa ou durante a rotina.
- **Métrica de sucesso para a persona:** encontrar a cidade desejada e entender
  a condição atual sem precisar repetir a busca ou pedir ajuda.

### Rafael — planejador de atividades

- **Objetivo principal:** usar a previsão dos próximos dias para escolher
  quando realizar atividades ao ar livre.
- **Contexto de uso:** consulta pelo celular durante a rotina e pelo desktop ao
  planejar com antecedência.
- **Métrica de sucesso para a persona:** localizar os dias relevantes e
  identificar com clareza as condições que podem afetar seus planos.

### Alex — viajante entre regiões

- **Objetivo principal:** consultar o clima de diferentes cidades e compreender
  as temperaturas na unidade de sua preferência.
- **Contexto de uso:** principalmente em dispositivo móvel durante a viagem e
  em desktop no planejamento prévio.
- **Métrica de sucesso para a persona:** selecionar a localidade correta e
  interpretar sem confusão a temperatura e as datas apresentadas.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** permitir que o usuário pesquise uma cidade e
  selecione a localidade desejada para consultar seu clima.
- **RF-02 — Consultar clima atual:** apresentar as condições meteorológicas
  atuais da cidade selecionada.
- **RF-03 — Consultar previsão:** apresentar a previsão do tempo para cinco
  dias da cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** permitir alternar a exibição da
  temperatura entre Celsius e Fahrenheit.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade e suporte móvel:** busca, clima atual e previsão
  devem permanecer legíveis e utilizáveis em larguras de tela de 320 px a
  1440 px, sem rolagem horizontal da página. Validar também a orientação
  retrato e paisagem em dispositivos móveis. A faixa de telas é uma meta
  proposta e precisa ser confirmada.
- **RNF-02 — Usabilidade:** em teste com usuários representativos, pelo menos
  90% devem conseguir pesquisar uma cidade, consultar o clima e alternar a
  unidade sem ajuda. O roteiro, o tamanho da amostra e a meta precisam ser
  validados com produto.
- **RNF-03 — Desempenho:** em condições de rede a definir, a busca de cidades
  deve apresentar resultados em até 3 segundos no percentil 95 (p95), e os
  dados meteorológicos em até 5 segundos no p95. Medir a partir da ação do
  usuário até a exibição de resultados utilizáveis; confirmar as metas e as
  condições do teste.
- **RNF-04 — Acessibilidade:** atender ao nível AA da WCAG 2.2. Os fluxos de
  busca, seleção de cidade e alternância de unidade devem ser operáveis por
  teclado, ter foco visível e nomes acessíveis para tecnologias assistivas.
  Validar também contraste e semântica por testes automatizados e revisão
  manual.
- **RNF-05 — Disponibilidade:** manter a aplicação disponível em pelo menos
  99,5% do tempo em cada mês, excluindo manutenção programada comunicada com
  antecedência. A meta e a janela de manutenção são propostas e precisam de
  aprovação; a indisponibilidade da fonte meteorológica deve ser medida
  separadamente.
- **RNF-06 — Atualidade e confiabilidade dos dados:** exibir o horário da última
  atualização dos dados meteorológicos. A idade máxima aceitável, a cobertura
  geográfica e a disponibilidade da fonte devem ser definidas após a escolha
  do provedor; dados que ultrapassem o limite acordado devem ser identificados
  como desatualizados.
- **RNF-07 — Compatibilidade:** suportar as duas versões estáveis mais recentes
  de Chrome, Edge, Firefox e Safari, em desktop e dispositivos móveis. Confirmar
  navegadores e versões com base no público-alvo.

## Riscos

As probabilidades abaixo são avaliações iniciais com base no briefing incompleto;
devem ser revistas quando público, provedor, arquitetura e metas forem definidos.

| Categoria | Risco | Probabilidade | Impacto | Estratégia de mitigação |
|---|---|---|---|---|
| Produto | Escopo de “clima atual” e previsão de cinco dias não definido; usuários podem esperar campos ou períodos diferentes. | Alta | Alto — entrega pode não atender às expectativas e exigir retrabalho de produto e interface. | Definir conteúdo, granularidade, fuso horário e interpretação de “cinco dias”; validar com usuários e critérios de aceite antes da implementação. |
| Produto | Busca ambígua para cidades homônimas ou com grafias alternativas. | Média | Alto — usuário pode consultar a localidade errada ou não encontrar a cidade. | Definir cobertura e regras de busca; mostrar contexto geográfico (por exemplo, região e país) e testar casos ambíguos. |
| Produto | Unidade ou convenções regionais inadequadas ao público. | Média | Médio — leituras podem ser confusas ou inconsistentes entre temperatura, vento, precipitação e datas. | Definir unidade inicial, escopo da alternância, persistência da preferência e formatos regionais; manter rótulos explícitos e testar conversões. |
| Produto | Experiência móvel insuficiente para busca e leitura da previsão. | Média | Alto — tarefas centrais podem ficar difíceis ou indisponíveis em telas pequenas. | Confirmar plataforma e dispositivos prioritários; testar fluxos em larguras e orientações representativas e validar critérios de responsividade. |
| Produto | Lacunas de acessibilidade impedem parte do público de usar funções principais. | Média | Alto — exclusão de usuários e necessidade de correções estruturais tardias. | Adotar critérios de acessibilidade verificáveis, testar teclado e tecnologias assistivas desde o início e incluir revisão manual além de verificações automatizadas. |
| Técnico | Indisponibilidade, limite de requisições, mudança de termos ou custo do provedor meteorológico. | Média | Alto — consultas podem falhar ou a operação tornar-se inviável. | Avaliar contratos, limites, custos e SLA antes da escolha; monitorar uso; definir estratégia de degradação e plano de contingência compatível com os termos do provedor. |
| Técnico | Cobertura desigual ou dados imprecisos/desatualizados para determinadas localidades. | Média | Alto — perda de confiança e apresentação de informação potencialmente enganosa. | Verificar cobertura e atualização por região; definir idade máxima aceitável, exibir horário dos dados e identificar explicitamente conteúdo desatualizado ou indisponível. |
| Técnico | Latência de busca ou consulta meteorológica excede o aceitável, sobretudo em redes móveis. | Média | Médio | Medir p95 em dispositivos e redes representativos; definir metas e orçamento de latência; otimizar chamadas e usar cache apenas dentro de limites de frescor acordados. |
| Técnico | Falhas de rede, respostas inválidas ou ausência de resultados não são tratadas com clareza. | Média | Alto — telas podem ficar vazias, travar ou apresentar dados incorretos como atuais. | Especificar estados de carregamento, vazio, erro e dado desatualizado; validar respostas; comunicar falha sem apresentar sucesso falso e testar indisponibilidade do provedor. |
| Técnico | Uso de localização ou telemetria sem definição de necessidade, consentimento e retenção. | Baixa | Alto — risco à privacidade, à confiança e à conformidade regulatória. | Não coletar localização por padrão; definir finalidade, consentimento, minimização e retenção antes de habilitar geolocalização ou analytics; revisar requisitos aplicáveis. |
| Técnico | Crescimento de tráfego excede capacidade ou limites do serviço externo. | Baixa | Alto — degradação generalizada, indisponibilidade e custos inesperados. | Estimar carga, monitorar tráfego e limites do provedor, definir alertas e testar comportamento sob carga antes de ampliar disponibilidade. |

## Perguntas em Aberto

O briefing define capacidades gerais, mas não detalha o público, as regras de
produto, o conteúdo, as metas de qualidade nem as condições operacionais.
Decidir esses pontos implicitamente pode levar a escopo, experiência e custo
inadequados. As perguntas abaixo devem ser respondidas ou aceitas explicitamente
como decisões pendentes antes de fechar a especificação.

1. **Público e necessidade:** Quem são os usuários prioritários e em quais
   situações precisam consultar o clima?
   **Impacto de seguir sem resposta:** a equipe pode otimizar para usuários,
   contextos ou necessidades errados, reduzindo adoção e tornando decisões de
   interface difíceis de validar.
2. **Objetivo e sucesso do produto:** Que resultado de negócio a aplicação deve
   gerar e como será medido o sucesso (por exemplo, consultas concluídas,
   usuários recorrentes ou satisfação)?
   **Impacto:** sem métricas, não será possível priorizar trade-offs nem avaliar
   se o produto atende ao objetivo da empresa.
3. **Plataforma e distribuição:** “Aplicação” significa um web app responsivo,
   PWA instalável ou aplicativos nativos? Como os usuários chegarão ao produto?
   **Impacto:** a decisão altera arquitetura, custo, distribuição, recursos de
   dispositivo e estratégia de suporte.
4. **Escopo do MVP:** As quatro capacidades descritas constituem todo o escopo
   inicial? Favoritos, histórico, alertas, compartilhamento, localização
   automática e uso offline estão dentro ou fora? Autenticação foi decidida
   como fora do escopo.
   **Impacto:** itens implícitos podem ampliar escopo, prazo e custo; excluí-los
   sem validação pode deixar de atender uma necessidade central.
5. **Território e cobertura:** Quais países, regiões e localidades precisam ser
   atendidos? Há prioridade para cidades brasileiras ou cobertura global?
   **Impacto:** cobertura influencia seleção do provedor, qualidade dos
   resultados, idioma, unidades e disponibilidade do serviço.
6. **Busca — entradas:** O usuário poderá buscar somente por nome de cidade ou
   também por estado/região, país, código postal, coordenadas ou localização
   atual?
   **Impacto:** sem definir entradas, os critérios de busca, a interface e a
   necessidade de permissões de localização ficam indeterminados.
7. **Busca — resultados e seleção:** Quantos resultados devem ser apresentados
   e quais dados (como estado, país ou coordenadas) distinguem cidades
   homônimas? Como lidar com grafias alternativas e acentos?
   **Impacto:** usuários podem selecionar o lugar errado ou não encontrar a
   localidade desejada.
8. **Clima “atual”:** Quais campos devem aparecer (temperatura, sensação
   térmica, condição, umidade, vento, precipitação, pressão, entre outros) e o
   que “atual” significa em termos de horário e atualização?
   **Impacto:** diferentes interpretações mudam o layout, o contrato de dados e
   a expectativa de precisão.
9. **Previsão de cinco dias — fuso horário:** Como definir o fuso horário que
   determina a data local e a virada do dia? A decisão é hoje mais os quatro
   dias seguintes.
   **Impacto:** sem um fuso definido, datas e dias da semana podem ser rotulados
   incorretamente, especialmente para usuários que consultam outras regiões.
10. **Previsão — granularidade e conteúdo:** A previsão será um resumo diário,
    por períodos do dia ou horária? Quais variáveis, probabilidades e alertas
    devem ser apresentados?
    **Impacto:** a quantidade de informação altera a experiência móvel, o
    volume de dados e a escolha do provedor.
11. **Unidades:** Celsius/Fahrenheit se aplica apenas à temperatura? Quais
    unidades devem ser usadas para vento, precipitação, distância e pressão?
    Celsius está definido como unidade inicial.
    **Impacto:** unidades misturadas ou inadequadas podem confundir usuários e
    levar a interpretações erradas dos dados.
12. **Preferência de unidade:** A escolha deve persistir durante a sessão,
    entre visitas no mesmo dispositivo ou deve voltar ao padrão em nova visita?
    Sem persistência de servidor, como a preferência será tratada entre
    dispositivos? Deve haver detecção automática por região?
    **Impacto:** comportamento inconsistente entre telas e visitas pode frustrar
    usuários; sincronização entre dispositivos não está disponível sem outro
    mecanismo de armazenamento identificado.
13. **Fonte e condições de uso:** Open-Meteo está definido como provedor inicial.
    Quais termos de uso, atribuição, limites, custos, cobertura e garantias
    aplicáveis precisam ser considerados?
    **Impacto:** condições incompatíveis podem limitar funcionalidades ou
    tornar a operação inviável, mesmo com o provedor escolhido.
14. **Frescor e precisão:** Qual idade máxima dos dados é aceitável? É
    necessário exibir horário de atualização ou comunicar a incerteza da
    previsão?
    **Impacto:** sem limites, dados antigos podem ser apresentados como atuais e
    comprometer confiança e decisões dos usuários.
15. **Falhas e estados vazios:** O que deve ocorrer quando não há resultados,
    a cidade não tem cobertura, a conexão falha, o provedor excede limites ou
    retorna dados inválidos/desatualizados? É aceitável mostrar dados em cache?
    **Impacto:** fluxos críticos ficam indefinidos e podem resultar em telas
    vazias, mensagens enganosas ou indisponibilidade completa.
16. **Atualização e cache:** Com que frequência clima e previsão devem ser
    atualizados? É permitido reutilizar resultados em cache e por quanto tempo?
    **Impacto:** atualizações excessivas podem elevar custo e atingir limites do
    provedor; cache excessivo pode exibir dados desatualizados.
17. **Idiomas e localização regional:** pt-BR está definido para a interface.
    Quais formatos de data/hora, convenções de endereço e regras regionais devem
    ser suportados? Há necessidade de outros idiomas?
    **Impacto:** sem definição, a interface pode usar formatos inadequados para
    parte do público e das localidades atendidas; não oferecer outros idiomas
    pode limitar o alcance do produto.
18. **Dispositivos e compatibilidade:** Quais larguras, orientações, sistemas
    operacionais e navegadores são prioritários? As metas propostas de 320 px a
    1440 px e duas versões estáveis recentes dos navegadores são adequadas?
    **Impacto:** a equipe pode testar uma matriz errada, deixando usuários
    importantes sem suporte ou assumindo custos não aprovados.
19. **Performance:** As metas propostas (busca em até 3 s e clima em até 5 s no
    p95) são aceitáveis? Em quais redes, dispositivos, regiões e condições serão
    medidas?
    **Impacto:** números sem condições de medição não são verificáveis e podem
    gerar metas impossíveis ou uma experiência lenta para o público real.
20. **Disponibilidade:** A meta proposta de 99,5% mensal é aprovada? Como serão
    contadas manutenção programada e falhas do provedor externo?
    **Impacto:** sem definição de escopo e cálculo, disponibilidade não pode ser
    monitorada nem cobrada de forma consistente; a dependência externa pode
    comprometer a promessa do produto.
21. **Acessibilidade e usabilidade:** WCAG 2.2 AA e a meta proposta de 90% de
    sucesso em teste são adequadas? Quais usuários, tecnologias assistivas e
    critérios de avaliação devem ser considerados?
    **Impacto:** necessidades de pessoas com deficiência podem ficar sem
    atendimento, e a equipe não terá critério objetivo para validar a
    experiência.
22. **Privacidade e permissões:** A aplicação coletará localização, consultas,
    identificadores ou analytics? Por quanto tempo e para quais finalidades os
    dados serão retidos?
    **Impacto:** coleta não definida pode violar expectativas dos usuários,
    exigir consentimento ou controles adicionais e criar riscos regulatórios.
23. **Segurança:** Há requisitos corporativos para proteção de tráfego,
    dependências, abuso de busca, limitação de requisições ou resposta a
    incidentes?
    **Impacto:** ausência de critérios pode expor dados, permitir abuso de
    recursos ou deixar riscos operacionais sem tratamento.
24. **Escala e operação:** Quantos usuários e consultas são esperados? Quem
    monitora o serviço e responde a incidentes, e quais indicadores devem gerar
    alerta?
    **Impacto:** capacidade, custos e suporte podem ser subdimensionados; falhas
    podem persistir sem detecção ou responsável definido.
25. **Conteúdo e avisos:** A aplicação deve exibir atribuição do provedor,
    avisos de precisão, alertas meteorológicos oficiais ou orientação sobre
    uso dos dados?
    **Impacto:** podem faltar informações importantes para interpretar os
    dados, atender aos termos do provedor ou evitar confiança excessiva na
    previsão.

## Suposições

- A experiência descrita é para consulta; o briefing não solicita cadastro,
  autenticação ou edição de dados meteorológicos, e foi decidido que não haverá
  persistência de servidor.
- O usuário inicia a consulta pesquisando e selecionando uma cidade; não se
  presume uso de geolocalização automática.
- A previsão de cinco dias se refere a uma cidade selecionada e deve ser
  apresentada junto à consulta do clima, sem implicar atualização em tempo real.
- Celsius e Fahrenheit são unidades de apresentação da temperatura; Celsius é
  o padrão definido, mas o escopo da conversão para outras grandezas ainda
  precisa ser esclarecido.
- “Usar em dispositivos móveis” implica uma interface responsiva, mas não
  determina se o produto será um site, uma aplicação web instalável ou um
  aplicativo nativo.
- As funcionalidades e os requisitos desta análise representam o escopo inicial
  indicado no briefing; qualquer capacidade não mencionada permanece sujeita a
  confirmação.
