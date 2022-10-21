# Detecção de Fraudes em Uma Empresa de Gás e Energia

Os serviços de gás, energia e saneamento são grandes adventos da engenharia que permitem o crescimento e desenvolvimento das cidades e da sociedade. A infraestrutura fornecida por estes serviços incluem tubos, medidores, cabos e fios etc. que transportam recursos como energia, água e gás por milhares de quilômetros. A essencialidade destes serviços torna extremamente necessário que estes sejam mantidos em boas condições constantemente, demandando recursos contínuos para programas de manutenção e melhorias.

Entretanto, um problema comum que estas empresas encontram é o das perdas comerciais, também chamadas de não técnicas, que são resultantes da diferença entre a quantidade de recurso (e.g. energia elétrica) fornecida e a quantidade de recurso medida para cobrança. Este tipo de perda pode ocorrer devido ao mal funcionamento de medidores, fraudes ou ainda com o uso de "by pass", onde um tubo desvia o recurso do trecho onde está instalado o medidor, levando à uma subestimativa da quantidade consumida.

Neste projeto, busquei fornecer um modelo para detecção de fraudes, que com base em algumas variáveis que caracterizam o consumidor, infraestrutura e seu padrão de consumo, pode levar equipes de campo à encontrar mais facilmente situações de perdas comerciais. Os dados utilizados são da Empresa Tunisiana de Eletricidade e Gás, empresa esta que acumula um prejuízo de 200 milhões de Dinares Tunisianos (R$ 323 mi) devido à manipulação e fraudes dos consumidores.

O detalhamento das analises e códigos pode ser encontrado no notebook "Final Notebook - Fraud Detection in Gas and Energy.ipynb".

## Motivação

O conjunto de dados fornecidos pela empresa possui o cadastro de 135.493 clientes, e os dados de 4.476.749 cobranças. Os registros dos clientes datam da década de 1970, e aempresa tem crescido constantemente o número de consumidores desde então. Nos anos 2000, a empresa dobrou a quantidade anual de novos clientes. Apesar desta boa notícia, a quantidade de fraudes destes novos clientes é maior que o registrado nos clientes mais antigos.

A maior parte dos dados de cobrança é do serviço de energia (69%), que são realizadas em média a cada 4 meses de consumo acumulado. Os consumidores estão divididos em quatro distritos, que em média possuem 34 mil consumidores. No conjunto de dados, 94% dos registros são de clientes legais, enquanto 6% foram flagrados como fraudadores. Isto quer dizer que uma busca aleatória nos clientes teria uma probabilidade de apenas 6% de detectar um problema de perda comercial. Este é um dos motivadores deste trabalho, que busca fornecer uma ferrramenta capaz de aumentar a probabilidade da equipe de campo verificar efetivamente uma fraude, ao analisar características de consumo e de cadastro dos clientes previamente.

## Métodos

Para conduzir esta análise foi preciso avaliar a série histórica dos consumos e extrair atributos que os representassem. Foram obtidos, por exemplo, os consumos médio, desvios padrão dos consumo, valores mínimos e máximos, intervalo entre cobranças, entre outras características. Com os perfis de consumo caracterizados, foi possível agregar estes dados ao cadastro dos clientes, que possuia informações como o tipo de tarifa, o distrito, a data de criação entre outras.

Estes dados agregados serviram para treinar perliminarmente três modelos de machine learning: (i) Árvore de Decisão; (ii) XGBoost, e (iii) LightGBM. Estes modelos foram avaliados com base nas métricas de F1-Score, Accuracy-Score e principalmente ROC-AUC Score. Como a classe de clientes que não cometeram fraude possui uma quantidade de dados muito superior à dos clientes fraudadores, foi realizado um undersampling na clase de não fraudadores, reduzindo a desproporcionalidade entre elas. Caso isto não fosse realizado, os modelos provavelmente "aprenderiam" que a melhor performance seria simplesmente atribuir a cada instância a classificação de não fraudador, levando a uma acurácia elevada.

## Resultados

Os resultados preliminares indicaram que o modelo LightGBM possuia todas as métricas de teste superiores às dos modelos XGBoost e Árvore de Decisão, nesta ordem. Com ROC-AUC Score = 0.69, o modelo foi selecionado como o mais promissor para ser otimizado (fine tuning).

Na etapa de fine tuning, o modelo passou pelos algoritmos de RandomizedSearchCV e StratifiedKFold, que buscaram avaliar diferents configurações do modelo em diferentes sub-conjuntos de dados. Ao término deste processo, o modelo apresentou uma melhoria considerável no AUC-Scoer, o que é bastante significativo para um modelo de classificação com classes bastante desbalanceadas. Ao término desta etapa, o modelo apresentou apresentou AUC-ROC = 0.74. Para uma avaliação final, o modelo passou foi aplicado no conjunto de testes, que não foi rebalanceado e o qual não havia sido submetido ao modelo anteriormente. Nesta etapa, o modelo obteve AUC-ROC = 0.70.

Acredito ser possível melhorar as métricas citadas com o uso de:
- Seleção de features;
- O melhor modelo foi uma versão do LightGBM;
- A melhor métrica de treinamento no cross-validation com k-fold foi ROC-AUC = 0,76
- O melhor modelo teve ROC-AUC = 0,69 no conjunto de testes, que foi entendido aqui como uma boa - métrica para o problema, dado o desbalanceamento das classes;
- Podem ser utilizados outras estratégias de reamostragem e também modelos para aprimorar os resultdos;
- O desbalanceamento influenciou em uma grande quantidade de detecções do modelo como fraude incorretamente;
- Sob a perspectiva destas métricas, a precisão das idas a campo para inspeções será consideravelmente assertiva, levando a uma maior recuperação de receita ao mitigar estas fraudes.

## Referências
[1] Dataset - https://www.kaggle.com/datasets/mrmorj/fraud-detection-in-electricity-and-gas-consumption

