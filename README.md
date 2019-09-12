a. Como foi a definição da sua estratégia de
modelagem?

Primeiro fiz uma análise exploratória para corrigir alguns problemas que encontrei no dataset (outliers e valores aparentemente errados, no python notebook eu detalhei mais). Após dividir o conjunto de treinamento e testes eu comecei com os modelos mais simples como ridge regression e lasso. Acho que uma boa estratégia é começar com modelos simples e ir aumentando a complexidade ao invés de partir de uma vez para os mais complexos, existem casos famosos que modelos mais complexos (como redes neurais) produzem resultados piores que uma regressão restrita (por exemplo, o dataset de preço de casas no kaggle, é mais facil ajustar uma regressão do que uma MLP). Depois de tentar regressão, eu notei que o dataset tem duas características: a- ele parece mais com um problema de classificação com os labels sendo as notas, então parti para modelos de classificação e b- ele é um dataset desbalanceado, porque as notas 3 e 9 são raríssimas. 

Para classificação, eu apenas criei modelos simples clássicos e bem conhecidos e testei a acurácia pra ver se melhoravam, não me importei nesse primeiro momento em fazer validação cruzada ou em tunar os parâmetros, apenas queria ter uma ideia se melhoraria. 
Aparentemente melhorou e resolvi investir em clasificação.

Para o dataset desbalanceado, acho que temos basicamente 3 opçoes: (i) usar métodos de oversampling ou undersampling (ii) penalizar na função de custo os elementos mais raros e (iii) usar métodos que levam em conta o desbalanceamento. Optei pela opção (iii) porque, pela minha experiência com dados assim (trabalhei em uma empresa de marketing que meu primeiro projeto foi com dataset desbalanceado pra prever chance de turnover de clientes). A opção (ii) é mais complicada e na prática você tem que encontrar o valor do peso certo da penalidade, isso por si só já é dificil. 

O problema é que as estratégias de dataset desbalanceados não deram certo. Porque os classificadores passaram a acertar mais a classe desbalanceada (3 e 9) ao custo de errar mais as classes com mais exemplos. Então abandonei essas estratégias e fui dar mais uma olhada nos dados. Claramente os dados parecem bastante ruidosos: input parecidos levavam a notas diferentes (percebi usso com as matrizes de confusão e com o t-SNE).

E aí veio a outra ideia, unir os labels, ao inves de notas de 3 até 9, eu teria apenas 1, 2 e 3. Após isso, sem muito esforço a acurácia subiu pra 83%. 

b. Como foi definida a função de custo utilizada?

Eu sinceramente não defini uma função de custo diferente das funções default que os métodos empregam. Em um emprego anterior eu criei um classificador para definir se um cliente iria ou nao comprar um produto com base na navegação dele. O dataset era extremamente desbalanceado e nesse caso usei lightGBM com uma função custom que penalizava mais a classe dos clientes que compravam, mas esse hyperparâmetro foi bastante trabalhoso para ser tunado e no final desistimos dessa abordagem. Acho que eu poderia ter feito o mesmo aqui, mas resolvi tentar abordagens mais simples.

c. Qual foi o critério utilizado na seleção do modelo
final?

Como descrevi no item a, foi meio que um processo. Percebi que random forest dava bons resultados e resolvi investir nele. No final testei o lightGBM e obtive resultados quase iguais. Usei bastante a matriz de confusão e curva ROC e a própria medida de acurácia para comparar os modelos e na seleção dos parâmetros usei busca (grid e random) com validação cruzada. 

d. Qual foi o critério utilizado para validação do
modelo? Por que escolheu utilizar
este método?

Não entendi muito bem essa pergunta. Tentei usar validação cruzada sempre que possível (até porque é bastante simples de usar no sklearn, quase todos os métodos possuem o parâmetro cv=3). A vantagem da validação cruzada é evitar o overfitting e é uma solução que acho mais simples do que usar outras técnicas como regularização. 

Também separei os dados em conjunto de testes e treinamento. Depois de ajustar os modelos sempre tentei ver a matriz de confusão e outros gráficos (no notebook plotei alguns) para avaliar o quão bem o modelo se sai nos dados de testes.


e. Quais evidências você possui de que seu modelo é
suficientemente bom?

Um modelo é suficientemente bom quando ele tem uma boa capacidade de generalização. Existem estratégias para avaliar a capacidade de generalizaçao, por exemplo a mais trivial de todas é separar um conjunto para treinamento e outro para testes e usar o de testes para avaliar o modelo. Outras formas de avaliar os classificadores é através do uso da matriz de confusão e da curva ROC (que usei ambas). O melhor resultado que obtive ficou em torno de 83% o que dependendo da aplicação é bem ruim, mas para esse dataset é ruim? Acho que não muito, acredito que teria espaço para melhorar um pouco a acurácia, mas esse dataset tem linhas com valores muito parecidos e notas diferentes (acredito que devido a subjetividade de dar uma nota para um vinho). Em casos de dados ruidosos como esses, é difícil conseguir uma generalização boa.
