---
title: "Básico da Linguagem e Data Frames"
author: "Leonardo Sangali Barone"
date: "25 de Outubro de 2017"
output: pdf_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# A linguagem R e a centralidade Data frames

Uma característica distintiva da linguagem de programação R é ter sido desenvolvida para a análise de dados. E quando pensamos em análise de dados, a protagonista do show é a _base de dados_ ou, como vamos conhecer a partir de agora, __data frame__.

Por esta razão, em vez de aprender como fazer aritmética, elaborar funções ou executar loops para repetir tarefas e outros aspectos básicos da linguagem, neste  minicurso vamos colocar o R como um "software concorrente"" dos demais utilizados para análise de dados, em particular o MS. Excel e outros editores de planilha, os demais softwares estatísticos como SPSS, Stata e SAS, e a linguagem SQL.

Se você entrar no [portal de dados abertos](http://dados.prefeitura.sp.gov.br/) da Prefeitura Municipal de São Paulo, sobretudo na [área de dados da educação](http://dados.prefeitura.sp.gov.br/group/educacao) verá que a maioria dos arquvios são __data frames__.

As principais características de um data frame são: (1) cada coluna representa uma variável (ou característica) de um conjunto de observações; (2) cada linha representa uma observação e contém os valores de cada variável para tal observação. Vejamos um exemplo:

| Candidato | Partido | Votos | 
| --------- |:-------:| -----:|
| Beatriz   | PMDB    |   350 | 
| Danilo    | SOL     |  1598 | 
| Pedro     | PTB     |   784 | 
| Davi      | PSD     |   580 | 
| Mateus    | PV      |   2   | 

Se destacamos uma coluna do nosso data frame, temos um __vetor__. Por exemplo, a variável "Votos" pode ser presentado da seguinte maneira: {350, 1598, 784, 580, 2}. Um data frame é um conjunto de variáveis (vetores) dispostos na vertical e combinados um ao lado do outro.

Data frame e vetores são __objetos__ na linguagem R.

Vamos ver como o R representa vetores e data frames na tela. Antes disso, é preciso "abrir" um data frame.

## Pausa para falar de pacotes

Uma das características mais atrativas da linguagem R é o desenvolvimento de __pacotes__ pela comunidade de usuários. Pacotes são conjuntos de funções (aka comandos) e, por vezes, guardam também dados em diversos formatos.

Neste minicurso utilizaremos dois dos pacotes mais populares da linguagem: _dplyr_ (para manipulação de data frames), _ggplot2_ (para gráficos), _readr_ (para abrir arquivos de dados) e _ggmap_ (mapas).

A comunidade de R mantém um repositório online de pacotes a partir do qual você pode instalar diretamente com o seguinte comando:

```{r}
install.packages("dplyr")
```

Repita você mesm@ o comando para os demais pacotes.

Uma vez instalados os pacotes no seu computador, você nunca mais precisará repetir o processo (a não ser para atualizar uma nova versão do pacote, se desejar). Ainda assim, sempre que abrir o R é preciso "chamar/carregar o pacote" para suas funções estejam disponíveis naquela sessão. Para carregar um pacote, utilizamos a função _library_:

```{r}
library(dplyr)
```

Excelente! Voltemos aos _data frames_

## Fazendo download dos dados "sem clicar"

Na linguagem R há diversas funções para obter dados da web (lá no final deste tutorial indicarei um curso que dei só sobre isso). A mais simples dela é _donwload.file_. Basta ter um url de uma base de dados e ter um nome para dar à base.

Vamos começar "guardando" o endereço de uma base de dados em um url. Nosso exemplo será o [cadastro de escolas](http://dados.prefeitura.sp.gov.br/dataset/cadastro-de-escolas-municipais-conveniadas-e-privadas/resource/52b8a68b-ad4f-4f56-95ec-c2b0da052849) que encontramos no portal de dados abertos. Você verá que o portal fornece um URL da base. Vamos copiá-lo e guardá-lo no objeto que chamaremos de "url\_cadastro". Veja como:

```{r}
url_cadastro <- "http://dados.prefeitura.sp.gov.br/dataset/8da55b0e-b385-4b54-9296-d0000014ddd5/resource/8f37dbaf-dcbd-46cc-9e34-1b4802690485/download/escolasr34abril2017.csv"
```

No R, podemos guardar informações em objetos. No nosso exemplo, pense como se estivéssemos separando uma caixinha na memória do computador com a etiqueta "url\_cadastro" e guardando o URL dentro. Os objetos do R ficam no __workspace__ e você verá na janela "Enviroment" do RStudio consta o objeto "url\_cadastro".

O símbolo "<-", que utilizaremos o tempo todo, é o símbolo de atribuição (por isso se parece com um seta). Estamos atribuindo uma informação -- URL, que está do lado direito -- ao objeto cujo nome escolhemos -- lado esquerdo. Se você tiver ficado com dúvidas neste ponto, não se preocupe. Logo você se abituará.

Vamos, então, usar a função _download.file_ para baixar o arquivo que está no URL que "guardamos". Veja:

```{r}
download.file(url_cadastro, "base_cadastro.csv")
```

Há vários aspectos importantes da linguagem R que podemos entender com o exemplo acima.

Em primeiro lugar, note que não utilizamos o símbolo de atribuição. Como não guardaremos nenhum objeto novo no R, não precisamos utilizar " <-". O arquivo irá direto para o diretório do seu computador em que você estiver trabalhando (você pode digitar "getwd()" para saber qual é o seu "working directory").

Utilizamos depois do nome função um parênteses. Este parêtenses carrega os __argumentos__ (ou __parâmetros__) da função. A função _download.file_ precisa de ao menos dois parâmetros: o URL de onde faremos o download e o nome do arquivo que será salvo.

Veja que utilizamos um objeto que estava na memória como primeiro parâmetro. Se quiséssemos, porém, poderíamos digitar o URL diretamente, entre aspas, no lugar de "url\_cadastro". É indiferente.

Os parâmetros de uma função são separados por vírgulas, como no exemplo. O nome com o qual o arquivo será salvo deve ir entre aspas, pois não é um objeto do R, mas um texto. Objetos "chamados" não precisam de aspas. Observe também que o nome contém a extensão do arquivo (".csv", no caso).

OBS: é possível que usuários de Windows precisem adicionar o parâmetro "mode = wb" (que trata da forma em que o arquivo é escrito na memória do computar). Basta incluir mais uma vírgula e o texto indicado antes de fechar o parênteses.

## Abrindo arquivos

Se você estiver utilizando o RStudio, um jeito simples de abrir o arquivo: basta clicar no botão "Import Dataset", escolher "From Text (readr)", pois o arquivo é um .csv ("comma separated values", ou seja, um arquivo de texto cujos valores das colunas separados por vírgula), "Browse", encontrar o arquivo e definir os parâmetros para abrí-lo. 

Note que no caso do cadastro de escolas o símbolo que separa as colunas (delimitador, ou "delimiter", em inglês) é o ponto e vírgula. Espaço. tab, vírgula e ponto e vírgula são os separadores mais comumente utilizados.

Além disso, a primeira linha do arquivo já contém os nomes das colunas. Os nomes dão uma ideia do conteúdo das variáveis, mas você pode explorar a definição de cada coluna lendo o [Dicionário de Dados](http://dados.prefeitura.sp.gov.br/dataset/cadastro-de-escolas-municipais-conveniadas-e-privadas/resource/52b8a68b-ad4f-4f56-95ec-c2b0da052849).

Você pode escrever o comando de abertura de dados em vez de clicar no botão. Vamos utilizar uma função do pacote _readr_ e, antes de seguir, vamos carregá-lo:

```{r}
library(readr)
```

Vamos utilizar a função _read\_delim_ para abrirmos no R os dados. Note que agora queremos que o resultado da função (dados abertos) sejam atribuídos a um objeto ("escolas"). Precisamos, então, do símbolo de atribuição "<-":

```{r}
escolas <- read_delim(file = "base_cadastro.csv", delim = ";")
```

Veja que desta vez colocamos entre parênteses os nomes dos parâmetros ("file" e "delim") e o valor que receberiam após o símbolo de "=". Usar o nome dos parêmtros é opcional (desde que você saiba a ordem deles na função, como fizemos com "download.file").

Vamos ver os dados que carregamos com a função _View_ (obs: com "V" maiúsculo, o que raramente acontece com funções em R):

```{r}
View(escolas)
```


## Do editor de planilhas ao R

A partir desse ponto no meetup vamos resistir à tentação de "olhar" para os dados. O hábito de quem utiliza com editores de planilha como MS Excel ou Libre Office, ou ainda com algums softwares de análise de dados como SPSS e Minitab, é trabalhar "olhando" para os dados, ou seja, para os valores de cada célula de uma base dados.

Você perceberá em pouco tempo que isso não é necessário. Na verdade, é contraproducente. Vamos nos munir de ferramentas que nos permitirão conhecer os dados sem olhá-los diretamente.

Por exemplo, podemos substituir a função _View_ pela função _head_. Veja o resultado:

```{r}
head(escolas)
```

Com apenas as 6 primeiras linhas do _data frame_ temos noção do conjunto. Alternativamente, podemos usar a função _str_ (atalho para "structure"):

```{r}
str(escolas)
```

Com _str_ sabemos qual é a lista de variáveis (colunas) no _data frame_, de qual tipo são -- no caso, todas são numéricas e vamos falar sobre esse tema mais tarde -- e os primeiros valores de cada uma, além do número total de observações e variáveis mostrados no topo do __output__.

Há outras maneiras de obter o número linhas e colunas de um _data frame_:

```{r}
nrow(escolas)
ncol(escolas)
dim(escolas)
```

_nrow_ retorna o número de linhas; _ncol_, o de coluna; _dim_ as dimensões (na ordem linha e depois coluna) do objeto.

_names_, por sua vez, retorna os nomes das variáveis do _data frame_

```{r}
names(escolas)
```

## Pausa para um comentário

Podemos fazer comentários no meio do código. Basta usar # e tudo que seguir até o final da linha não será interpertado pelo R como código. Por exemplo:

```{r}
# Imprime o nome das variaveis do data frame escolas
names(escolas)

names(escolas) # Repetindo o comando acima com comentario em outro lugar
```

Comentários são extremamente úteis para documentar seu código. Documentar é parte de programar e você deve pensar nas pessoas com as quais vai compartilhar o código e no fato de que com certeza não se lembrará do que fez em pouco tempo (garanto, você vai esquecer).

## Manipulando data frames

Há diversas maneiras de manipular data frames em R. A mais popular delas, e na minha opinião a mais fácil, é utilizar a "gramática" do "dplyr". Vamos começar carregando o pacote (se já não estiver carregado):

```{r}
library(dplyr)
```

A gramática do _dplyr_ tem "verbos" bastante fáceis de utilizarmos. No nosso exemplo faremos um mapa com todas as EMEIs de São Paulo. Então, começaremos separando as informações (ou melhor, as colunas) que precisamos dentre as 53 do cadastro de escolas.

Para um mapa confecionar um mapa precisaremos apenas de latitude e longitude (colunas LATITUDE e LONGITUDE). Como vamos precisar selecionar apenas as EMEIs, precisamos da variável que descrever o tipo da unidade escolar (TIPOESC).

O primeiro verdo do _dplyr_ que utilizaremos sera o _rename_, para renomear as variáveis. Vamos dar nomes "bonitos" às 3 variáveis. è bastante simples.

```{r}
escolas <- rename(escolas, lat = LATITUDE, lon = LONGITUDE, tipo = TIPOESC)
```

Questão: como saber os nomes das variáveis originais? Consultando o [Dicionário de Dados](http://dados.prefeitura.sp.gov.br/dataset/cadastro-de-escolas-municipais-conveniadas-e-privadas/resource/52b8a68b-ad4f-4f56-95ec-c2b0da052849). 

Veja que estamos atribuindo o resultado da renomeação ao objeto "escolas", que já existe. Porque? Por que queremos que o data frame resultante da aplicação da função _rename_ sobrescreva o data frame que já existe. Em outras palavras, não é um problema atribuir algo a um objeto já existente. Basta lembrar que haverá uma substituição.

O primeiro argumento da função _rename_ é o próprio data frame que será transformado. Na sequência, separando por vírgula, damos nomes novos às variáveis existentes.

Simples, não?

Vamos agora liberar a memória do computador e selecionar apenas as 3 variáveis -- e jogar fora todo o resto. Faremos isso com o verbo _select_:

```{r}
escolas <- select(escolas, lat, lon, tipo)
```

Utilizando a função _head_, vemos que só temos 3 variáveis no data frame agora:

```{r}
head(escolas)
```

A função _select_, faz, portanto uma seleção de colunas.

E se quisermos transformar o conteúdo de uma coluna? No nosso caso, o formato dos valores de latitude e longitude estão em formato diferente do convenional. Latitudes são representadas por números entre -90 e 90, com 8 casas decimais, e Longitudes por números entre -180 e 180, também com 8 casas decimais. Em nosso par de variáveis, o separador de decimal está omitido e por esta razão faremos esta pequena modificação na variável (divindo por 1 milhão).

A função (verbo) para modificar variáveis do pacote _dplyr_ é mutate. Vamos ver como utilizá-la:

```{r}
escolas <- mutate(escolas, lat = lat / 1000000, lon = lon / 1000000)
```

Novamente, o primeiro argumento é o data frame e, separadas por vígulas, colocamos as transformações das variáveis que nos interessam. As operações matemáticas no R lembram as de uma calculadora (+, -, * e /, além de outras mais complicadas).

Tal como _select_, _mutate_ promove modificações nas colunas.

No cadastro de escolas temos todos os tipos de unidades: CEI, EMEI, EMEF, EJA, CEU, etc. Precisamos, agora, de uma função que faça a seleção de linhas para excluirmos todas as unidades que não sejam EMEI. O verbo do _dplyr_ para seleção de linha é _filter_. Veja seu uso:

```{r}
escolas <- filter(escolas, tipo == "EMEI")
```

O uso é semelhante ao dos demais verbos. Note, porém, que utilizamos dois símbolos de "igual". Sempre que nosso objetivo for comparar valores (exemplo: tipo de escola igual a EMEI; número de alunos menor ou igual a 1000, etc) utilizamos operadores relacionais.

## Pausa operadores relacionais

Uma das especialidades do computador é verificar se proposições simples são verdadeiras ou falsas. __Operadores relacionais__ servem para verificar se objetos são iguais, diferentes, maiores ou menores. São seis operadores relacionais e a tabela abaixo apresenta os seus símbolos.

| Operador       | Símbolo |
| -------------- |:-------:| 
| Igual          | ==      |
| Diferente      | !=      |
| Maior          | >       |
| Maior ou igual | >=      |
| Menor          | <       |
| Menor ou igual | <=      |

## Produzindo mapas no R

Temos, agora, um data frame que contém apenas EMEIs e a informação de latitude e longitude de cada uma. Conseguimos "reduzir" os dados usando quatro "verbos" (funções) fundamentais de manipulação de dados: _rename_, _select_, _mutate_ e _filter_.

Vamos à produção do mapa. Precisaremos do pacote _ggmap_, que "importa" mapas de rua de serviços da web (Google e Open Street Maps, por exemplo) e permite a combinação dos mapas com os pontos (escolas, a partir da latitude e longitude).

```{r}
library(ggmap)
```

Para importar um mapa, precisamos de um ponto -- ou seja, um par latitude e longitude -- que será o "centro" do mapa. Para simplificar, utilizaremos a Praça da Sé como referência. Vamos criar um vetor (com a função _c_) que tem um par de valores de nomes "lon" e "lat" referentes à localização da praça:

```{r}
se <- c(lon = -46.6362714, lat = -23.5500806)
```

A seguir, vamos importar o mapa utilizando a função _get\_map_. Vamos fazer um mapa focando no centro e por isso escolheremos o "zoom" igual a 12. Quanto maior o zoom, mais focado será o mapa. A API utilizada será a do Google -- padrão da função -- caso não informemos alguma API diferente:

```{r}
map_sp <- get_map(se, zoom = 12)
```

Podemos plotar o mapa que baixamos com a função _plot_:

```{r}
plot(map_sp)
```

Mas ainda não é isso que queremos. Precisamos sobrepor ao mapa que baixamos os pontos correspondentes a cada uma das EMEIs. Veja como:

```{r}
ggmap(map_sp) +
  geom_point(aes(lon, lat), data = escolas)
```

Lindo, não?

O código acima tem uma estrutura bastante diferente do que havíamos visto. Ele é parte de outra "gramática" do R, a "grammar o graphics" do pacote _ggplot2_. Precisaríamos de bastante mais tempo para aprendê-la, mas vamos tentar destrinchar o código do mapa acima.

Começamos plotando o mapa de São Paulo obtido na API do Google com a função _ggmap_. Ela criará a primeira camada do mapa e "organizará" o sistema de coordenadas. Com _geom\_point_, adicionaremos pontos ao mapa. Os pontos vem do data frame "escolas" -- por isso "data = escolas" -- e a informação utilizada, "lat" e "lon", faz parte da "aesthetics" do mapa -- por isso dentro do parênteses "aes".

Se você quiser aprender um pouco mais sobre mapas no R (e fazer mapas mais bonitos) pode fazer o tutorial do meu [curso no DCP-USP](https://github.com/leobarone/FLS6397/blob/master/tutorials/tutorial8.Rmd).

## Fim

Espero que com este tutorial você tenha se familiarizado com a linguagem R e compreendido como utilizá-la trabalhar com os dados abertos da educação municipal. Organizando o que fizemos em um único "bloco" de cógido, vemos que é preciso pouco código para fazer algo relevante:

```{r}
library(readr)
library(dplyr)
library(ggmap)

url_cadastro <- "http://dados.prefeitura.sp.gov.br/dataset/8da55b0e-b385-4b54-9296-d0000014ddd5/resource/8f37dbaf-dcbd-46cc-9e34-1b4802690485/download/escolasr34abril2017.csv"

download.file(url_cadastro, "base_cadastro.csv")

escolas <- read_delim(file = "base_cadastro.csv", delim = ";")

escolas <- rename(escolas, lat = LATITUDE, lon = LONGITUDE, tipo = TIPOESC)

escolas <- select(escolas, lat, lon, tipo)

escolas <- mutate(escolas, lat = lat / 1000000, lon = lon / 1000000)

escolas <- filter(escolas, tipo == "EMEI")

se <- c(lon = -46.6362714, lat = -23.5500806)

map_sp <- get_map(se, zoom = 12)

ggmap(map_sp) +
  geom_point(aes(lon, lat), data = escolas)
```

Vamos agora seguir agora para outros exemplos com dados da SME e aprender um pouco mais sobre R.







