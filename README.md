![wave](media/image/wave.gif)

# Sumário  

1.  **[Apresentação](#apresentação)**
2.  **[A Matriz](#a-matriz)**
3.  **[Pré-Processamento](#pré-processamento)**  

# **Apresentação**
Olá! Meu nome é **[Vinícius Fonseca](https://www.linkedin.com/in/vinicius-silva-fonseca/)** e sou estudante de graduação em **Matemática** na **[UEL - Universidade Estadual de Londrina](https://portal.uel.br/conheca-a-uel/)**.  

Desde o meu primeiro "Hello World!" me apaixonei por programação — e desde então, não parei mais.  

Naturalmente, a proximidade entre Matemática e Ciência de Dados me levou a aprender ``Python`` e, com ele, as principais bibliotecas da área:

  - `NumPy`
  - `Pandas`
  - `matplotlib`
  - `OpenCV`
  - `YOLO`

As três primeiras foram fundamentais no desenvolvimento do meu projeto ***Uso de LLMs para Detecção de Fake News no Brasil***, no qual utilizei LLMs para gerar embeddings com o Ollama e os classifiquei com um Random Forest Classifier do `scikit-learn`, alcançando métricas ***f1-macro*** entre ***88%*** (pior cenário) e ***99%*** (cenário ideal).

>Confira o projeto aqui **→** [LLM4FakeNews](https://github.com/Viniks07/LLM4FakeNews)   

Embora esse projeto tenha trazido importantes aprendizados, meu principal interesse sempre foi a área de **Visão Computacional**.  

Ao experimentar diversos exemplos com `OpenCV` e `YOLO`, percebi que estava adquirindo familiaridade com as bibliotecas, mas sentia falta de uma compreensão mais aprofundada dos princípios que fundamentam seu funcionamento.

Com esse pensamento, decidi iniciar este novo projeto chamado [MovRecCNN](https://github.com/Viniks07/MovRecCNN):
  
 **Desenvolver um pipeline de detecção e reconhecimento de movimentos sem uso de bibliotecas externas, utilizando apenas:**

- Bibliotecas nativas do ``Python``

- ``numpy`` para manipulação de matrizes

- ``OpenCV`` ***será utilizado exclusivamente para leitura e escrita de vídeos e imagens***

E por falar em matrizes, está na hora de abordarmos o elemento central de todo este projeto.

# **A Matriz**

A matriz é uma das principais formas de representar uma imagem digitalmente, vamos abordar abaixo sua estrutura.

### **Resolução**

Na matriz — como representação de imagem — o numero e disposição de pixel define a **resolução** da imagem. Por exemplo, imagens *Full HD* correspondem a  $\mathbf{1920\times1080}$.  

Normalmente quando trabalhamos com imagens a primeira dimensão
costuma indicar o numero de colunas $\mathbf{(1920)}$ e a segunda
o numero de linhas $\mathbf{(1080)}$. Porém, quando tratamos do
ponto de vista algébrico — que é o nosso caso — a primeira
dimensão será o número de linhas, e a segunda, o número de
colunas, portanto, seu formato seria $\mathbf{(1080 \times 1920)}$.  
( ***Mantenha isso em mente para evitar confusões*** )

Portanto a resolução é definida pela matriz ${m \times n}$, sendo ${m}$ o numero de linhas e ${n}$ o numero de colunas.


### **Canais**

Vamos restringir nosso foco a **dois tipos** de matrizes: as que possuem 1 canal e as que possuem 3 canais

#### **Escala de cinza**
A matriz com apenas 1 canal produz uma imagem em escala de cinza onde cada elemento $p_{m \times n}$ é um número de **0** a **255** (***1 byte***).
Sendo **0** o mais proximo do **preto** e **255** o mais o proximo de **branco**  

A imagem abaixo representa uma matriz em escala de cinza mas pode nos mostrar conceitualmente como funciona o posicionamento de cada pixel e os valores dentro deles de ambos os tipos de matrizes.

![Representação Matriz](media/image/matrix_representation.png)  

Abaixo temos um exemplo de como isso funciona em escala de cinza 

![Matriz RGB](media/image/gray_matrix.png)

#### **RGB**
A matriz **RGB** é uma matriz tridimensional 
(***[Tensor](https://medium.com/@michel.macario/sem-tensores-n%C3%A3o-h%C3%A1-intelig%C3%AAncia-artificial-62cae98b7e88)***) composta por 3 canais de cores onde cada elemento $p_{m \times n \times c}$ é a representação de cada uma dessas cores **RGB** — (***Vermelho***, ***Verde***, ***Azul***) — e vai de 0 a 255  
(3 canais de ***1 byte*** cada; Logo, cada pixel ocupa ***3 bytes***)  

Podemos pensar essa matriz tridimensional como um conjunto de matrizes bidimensionais **empilhadas** como mostra a figura.  

<img src="media/image/rgb_matrix.png" width="600" height="600" style="object-fit: contain;" />  

Outra maneira de pensarmos essa matriz RGB é imaginar uma matriz bidimensional onde cada elemento $p_{m \times n}$ são triplas **[R , G , B]** e a imagem completa é uma matriz $M_{(m \times n \times 3)}$ que definem a cor daquele pixel como mostra a figura.  

![RGP Representado por lista](media/image/rgb_list_representation.png)

>Caso queira ver de maneira interativa como funciona uma [pixel](https://viniks07.github.io/MovRecCNN/media/html/simulador_de_pixel.html)

Compreender a estrutura matricial será fundamental para o entendimento deste projeto especialmente em **processamento de imagens** e **aprendizado de máquina**. A matriz não é apenas uma forma de organizar os pixels, mas sim a base que possibilita manipulações, análises e transformações visuais. Agora que entendemos como uma imagem pode ser representada por uma matriz — seja em escala de cinza ou em cores RGB —, estamos prontos para explorar como operar sobre esses dados e extrair informações úteis a partir deles.

# **Pré-Processamento**

Após uma breve explicação sobre matrizes, entraremos de fato na primeira etapa do projeto: o pré-processamento. Nesta fase, veremos código real e exploraremos as funções que foram criadas e implementadas por mim. Para isso, utilizaremos a melhor biblioteca do `Python` — na minha humilde opinião — o **`NumPy`**.  

Todas essas funções foram criadas para auxiliar o processo e todas se encontram no modulo **[data_processing.py](https://github.com/Viniks07/MovRecCNN/blob/main/src/data_processing.py)**

Essa é a Nina e ela vai nos ajudar a demonstrar o efeito das funções nas imagens  

![Nina](media/image/nina.png)

## Mirroring

A primeira função criada foi a de espelhamento, pois estamos acostumados a nos ver no espelho — e por isso, quando vemos uma imagem espelhada, nos parece mais natural. Essa função é importante para ajustar imagens que, de outra forma, poderiam parecer “ao contrário” ou invertidas.

![Nina Mirroring](media/image/nina_mirroring.png)

## Gray Scale

A segunda função multiplica o vetor de canais de cor por uma matriz (ou vetor) de pesos pré-estabelecida. Pelas propriedades da multiplicação matricial, o resultado é um escalar que corresponde ao valor em escala de cinza.

Matematicamente:

![Gray Scale Formula](media/image/grayscale_formula.png)  

onde $GS$ — um número decimal que é convertido para um inteiro — representa o tom de cinza.  

<b>*Aviso</b> : <i>Apesar da matriz ser **RGB** o `OpenCv` lê as matrizes como **BGR**</i>

![Nina Gray Scale](media/image/nina_grayscale.png)

## Background Subtraction

A remoção do fundo foi, sem dúvida, o maior desafio desta etapa do projeto.Embora estejamos demonstrando as funções com imagens estáticas, o objetivo final é trabalhar com vídeos, o que exige uma técnica que possa remover o fundo de forma dinâmica.


A primeira abordagem que implementei foi um método de **``Chroma Key``**, que removia a cor de fundo dinamicamente, baseando-se no **desvio padrão de um valor RGB** capturado dentro de uma região delimitada por outra função chamada ``Target``. Essa função analisava todos os pixels contidos dentro do quadrado gerado para definir a cor a ser excluída.


Apesar de ser uma solução criativa, essa abordagem apresentava uma limitação significativas: ela dependia do uso de uma cor sólida e dentro de um espectro de cores bastante específico, o que restringia seu uso.

Depois de pesquisar, encontrei uma alternativa que atendeu muito melhor ao propósito: a técnica de ``Background Subtraction``.


A ideia é simples, mas eficaz:

1. Assume-se uma câmera estática.

2. Um frame vazio (sem o objeto de interesse) é capturado como referência.

3. A partir desse frame de referência, um limite é definido. Qualquer pixel nos frames subsequentes que varie menos que esse limite em comparação com o frame de referência é considerado parte do fundo e é "apagado".

Dessa forma, quando o fundo é igual ao frame de referência, a tela permanece preta. Mas assim que um objeto entra em cena, seus pixels diferem do frame de referência, fazendo com que o objeto se destaque na imagem. Essa abordagem provou ser muito mais robusta para a detecção de movimento em vídeos.

![Nina Background Subtraction](media/image/nina_background_subtraction.png)

## Binarization

Após a remoção do fundo, a imagem é **binarizada** — *objeto vs. fundo*.

- Todos os pixels pertencentes ao fundo permanecem ($0$).

- O objeto destacado recebe um valor de contraste máximo, normalmente branco ($255$).

Essa binarização facilita os proximos passos — como segmentação, contorno e detecção de movimento.


![Nina Binarizada](media/image/nina_binarization.png)

## Dilate

Normalmente, após a binarização, a imagem destacada pode apresentar "buracos" — como, coincidentemente, aconteceu na imagem de referência — então aplicamos [Morfologia Matemática (***Dilatação***)](https://www.youtube.com/watch?v=E8cqrkK4L_M&list=PLLFpILRpgx7lJlHOl3TXGuUVWnqzgTITh&index=13) para preenchê-los.

![Nina Dilatada](media/image/nina_dilate.png)

## Down Sampling

O downsampling foi o segundo grande desafio desta parte do projeto. Apesar de todos os processos de pré-processamento, não é viável usar a imagem completa como input do modelo. Pense na resolução que usamos aqui, de ($480 \times 640$), que corresponde a aproximadamente $\textbf{307}$ **mil pixels**!  
Processar tantos dados seria ineficiente e pesado.

É exatamente por isso que implementei a função ``down_sampling``. Ela é responsável por diminuir o tamanho da imagem. Basicamente, a função cria uma nova matriz em branco, cujo shape (***dimensões***) é o da imagem original dividido por um valor ``division``.

Em seguida, ela percorre a imagem original usando uma "**janela**" (***matriz quadrada***) com o tamanho definido por ``division``. Para cada uma dessas janelas, a função calcula a média dos valores dos pixels contidos nela. Esse valor médio é então usado para preencher o pixel correspondente na matriz de amostra reduzida.

Dessa forma, conseguimos uma representação menor da imagem, mantendo as informações essenciais, mas com um volume de dados muito mais adequado para as próximas etapas do projeto.

*Ex:* $\ 480 \times 640\ (307\ mil\ pixels)  →  \ 30\times40\ (1200 \ pixels)$

A função pode ser usada em ambos os formatos:

- Cinza

 ![Nina Downsample Gray](media/image/nina_downsample_gray.png)

- RGB 


 ![Nina Downsample Gray](media/image/nina_downsample.png)

<b>*Aviso</b> : <i>As referencias a direita não são os resultados reais eles são apenas representações. As imagens reais são minusculas comparadas a imagem original(***não processada***)</i>