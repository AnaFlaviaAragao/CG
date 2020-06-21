Introdução á Computação Gráfica - Trabalho I
---

Disciplina: [GDSCO0051] Introdução à Computação Gráfica - Turma 02.

Professor: Christian Azambuja Pagot (email: christian@ci.ufpb.br).

Alunas: 
        
        Ana Flavia S. Aragão Moura; 20160164270

        Thuane

---

## Índice

* [Introdução](#introdução)
* [Setup](#setup)
* [Pixels](#pixels)
	* [Ponto](#ponto)
* [Algoritmo de Bresenham](#algoritmo-de-bresenham)
	* [Linhas](#linha)
	* [Triângulos](#triângulos)
* [Interpolação Linear de Cores](#interpolação-linear-de-cores)
* [Conclusão](#conclusão)
* [Referências Bibliográficas](#referências-bibliográficas)

---


### Introdução
Este trabalho foi desenvolvido para a disciplina de Introdução à Computação Gráfica, ministrada pelo Prof. Dr. Christian Azambuja Pagot na Universidade Federal da Paraíba, UFPB. 

Com o objetivo de nos familiarizar com os algoritmos de rasterização utilizados em computação gráfica. Aprendemos na prática como rasterizar pontos, linhas e triângulos através da rasterização das linhas.

Simulando o acesso direto a memória de vídeo utilizando um framework, fornecido pelo professor, que simula o acesso à memória de vídeo. 

---



### Setup

Utilizamos o sistema operacional Linux distribuição Ubunto 2018. Para a compilação exige  requisitos do OpenGL e o GLUT (The OpenGL Toolkit) sejam armazenados. Após a instalação:

```sh
$ make

$ ./cgprog
```



### Pixels

Os monitores da atualidade são compostos por uma malha de pixels, formando um espaço bidimensional. Porém, a disposição desses pixels na memória de vídeo se dá de forma linear, onde todos os pixels são armazenados de forma contínua no que chamamos de *colour buffer*.Assim, é necessário calcular um offset para que cada pixel da tela, dada sua posição (x,y) ocupe um único e distinto espaço em memória. Este fato deve ser levado em consideração quando se trabalha com a manipulação de pixels.

A maioria dos monitores no mercado utilizam o padrão RGBA, onde cada pixel possui 4 componentes de cor: R para vermelho (RED), G para verde (GREEN), B para azul (BLUE) e A para transparência (ALPHA). Cada componente ocupa 1 byte do *colour buffer*, totalizando 4 bytes por pixel. O esquema de Image Storage está exemplificado na imagem apresentada:



<p align="center">
	<br>
	<img src="./Imagens/Figura1.png"/ width=720px height=400px>
	<h5 align="center">Figura 1 - Disposição dos pixels na memória</h5>
	<br>
</p>

Portanto, podemos de endereçar cada pixel (e cada canal de cor) no *colour buffer* da seguinte forma:

<p align="center">
	<br>
	<img src="./Imagens/Figura2.png"/ width=720px height=400px>
	<h5 align="center">Figura 2 - Endereçamento de pixels no colour buffer</h5>
	<br>
</p>

### Ponto


```Desenvolvimento Função: PutPixel(...): Rasteriza um ponto na memória de vídeo recebendo como parâmetros as coordenadas (x,y) do pixel na tela e sua cor (RGBA).```

Foi implementado um tipo estruturado **pixel** contendo todas as informações de coordenadas e cores dele:

```C++
typedef struct Pixel {
    int x;
    int y;

    double red;
    double green;
    double blue;
    double alpha;
} Pixel;
```

A fim de desenhar um pixel na tela, foi criada a função **putPixel()** que recebe como parâmentro um pixel (com suas informações de cor e coordenadas) e rasteriza um ponto na tela. No framework desenvolvido pelo professor, existe um ponteiro **FBPtr** que aponta para a primeira posição do *colour buffer*, e isso possibilitou a implementação de tal função:

``` C++
void putPixel(Pixel p) {
	
	if(p.x>IMAGE_WIDTH || p.x<0 || p.y>IMAGE_HEIGHT || p.y<0) return; 
	
	int endereco = p.x*4 + p.y*4*IMAGE_WIDTH;
	FBptr[endereco] = p.red;
	FBptr[endereco + 1] = p.green;
	FBptr[endereco + 2] = p.blue;
	FBptr[endereco + 3] = p.alpha;
}
```
Obtivemos esses resultados:
<p align="center">
	<br>
	<img src="./Imagens/Figura3.png"/ width=512px height=512px>
	<h5 align="center">Figura 3 - Função putPixel()</h5>
	<br>
</p>



### Algoritmo de Bresenham

A etapa mais complicada do projeto foi a rasterização de linhas. Para isso, foi criada a função **drawLine()**, que recebe dois pixels como parâmetro, e desenha uma reta aproximada entre eles.

Para essa função foi utilizado o algoritmo de Bresenham, muito utilizado na computação para rasterização de linhas. O algoritmo de Bresenham, de forma simplificada, busca aproximar uma reta (que sabemos que é contínua), para um universo discreto. Para isso, transforma-se a linha a ser desenhada em uma equação de reta, da forma y = mx + b.

A cada incremento de x, y incrementa baseado na inclinação m. Isso, porém, quer dizer que y assumirá valores de pontos flutuante, o que dificulta a representação por meio de pixels. O algoritmo de Bresenham chega então na etapa de decisão: a cada vez que x muda, verifica-se se o valor de y equivalente está mais próximo do y atual, ou de y + 1. Após a decisão, o pixel escolhido é acendido. Isso pode ser visto de uma forma melhor na seguinte imagem: 

<p align="center">
	<br>
	<img src="./Imagens/Figura4.4.png"/ width=512px height=256px>
	<h5 align="center">Figura 4 - Linha de Bresenham </h5>
	<br>
</p>

Porém, da forma apresentada, o algoritmo funciona apenas para linhas no primeiro octante. Isso é, o octante em que o deslocamento em xe y são positivos, e a inclinação da reta está entre 0º e 45º graus. 

<p align="center">
	<br>
	<img src="./Imagens/Figura5.png"/ width=512px height=512px>
	<h5 align="center">Figura 5 - Octantes </h5>
	<br>
</p>

### Linha

```Desenvolvimento Função: DrawLine(...): Rasteriza uma linha na tela, recebendo como parâmetros as coordenadas dos seus vértices inicial e final (representados respectivamente pelas tuplas (x0,y0) e (x1,y1)) e as cores (no formato RGBA) de cada vértice. As cores dos pixels ao longo da linha rasterizada devem ser obtidas por meio de interpolação linear das cores dos vértices. ```

Para a implementação funcionar para qualquer reta, foi necessário generalizar o algoritmo. Além disso, retas horizontais e retas verticais foram implementados separadamente das retas inclinadas. O algoritmo foi implementado da seguinte forma: 

```C++
void drawLine(Pixel inicial, Pixel final){
    int xi = inicial.x;
    int xf = final.x;
    int yi = inicial.y;
    int yf = final.y;
    int dx = abs(xf - xi);
    int dy = abs(yf - yi);
    int controle = 0;   //Controla se a direção menor vai crescer ou nao;
    int incX = 0;
    int incY = 0;

    //Define se Y e X estão indo nas direções positivas ou negativas
    if(xf > xi) incX = 1;
    else incX = -1;

    if(yf > yi) incY = 1;
    else incY = -1;

    putPixel(inicial);
    Pixel linha = {inicial.x, inicial.y, inicial.red, inicial.green, inicial.blue, inicial.alpha};  //Esse pixel é o que se moverá e pintará a linha
```

Essa é a parte inicial da função, que vai definir em qual octante será desenhada linha, baseado nos pixels passados como parâmetro. Também é criado o pixel "linha", que será nosso pixel "ambulante". Isso é, será ele que percorrerá a linha, mudando de coordenadas, e pintando cada pixel individual.

```C++
    if(dx == 0){
        if(yf > yi){    //linha pra baixo
            while(linha.y != yf)
            {

                linha.y++;              
                putPixel(linha);

            }
        }
        else{           //linha pra cima
            while(linha.y != yf)
            {

                linha.y--;               
                putPixel(linha);

            }
        }

    }
    else if(dy == 0){
        if(xf > xi){    //linha pra direita
            while(linha.x != xf)
            {

                linha.x++;                
                putPixel(linha);

            }
        }
        else{           //linha pra esquerda
            while(linha.x != xf)
            {

                linha.x--;                
                putPixel(linha);

            }
        }
    }
``` 
Essa parte ficou responsável por pintar linhas sem inclinação.
```C++
else {
        if (dx >= dy) {

            controle = dx / 2;
            putPixel(inicial);
            while (linha.x != xf) {
                linha.x += incX;
                controle = controle - dy;
                if (controle < 0) {
                    linha.y += incY;
                    controle += dx;
                }                
                putPixel(linha);
            }

        } else {
            controle = dy / 2;
            putPixel(inicial);
            while (linha.y != yf) {
                linha.y += incY;
                controle = controle - dx;
                if (controle < 0) {
                    linha.x += incX;
                    controle += dy;
                }                
                putPixel(linha);
            }

        }
```

O primeiro if desenha linhas em que o deslocamento x é maior do que deslocamento y. Isso é, nos octantes 1, 4, 5, e 8. Por consequência, o else é responsável pelos octantes 2, 3, 6, e 7. Após a implementação, podemos verificar o resultado: 




<p align="center">
	<br>
	<img src="./images/DrawLine sem interpolação.png"/ width=512px height=512px>
	<h5 align="center">Figura 6 - Linhas rasterizadas </h5>
	<br>
</p>



### Triângulos

```Desenvolvimento Função: DrawTriangle(...): Função que desenha as arestas de um triângulo na tela, recebendo como parâmetros as posições dos três vértices (x0,y0), (x1,y1) e (x2,y2) bem como as cores (RGBA) de cada um dos vértices. As cores dos pixels das arestas do triângulo devem ser obtidas através da interpolação linear das cores de seus vértices. Não é necessário o preenchimento do triângulo!```

Passada a implementação mais complicada do projeto, vamos tirar proveito do funcionamento da função drawLine() para desenvolver a função drawTriangle(). Esta função recebe como parâmentro 3 pixels que representam os vértices do triângulo. A função, então, rasteriza 3 linhas ligando os vértices:



Como resultado, obtivemos:
<p align="center">
	<br>
	<img src="./Imagens/Figura7.png"/ width=512px height=512px>
	<h5 align="center">Figura 7 - Função drawTriangle()</h5>
	<br>
</p>
<p align="center">
	<br>
	<img src="./images/triangulo1.png"/ width=512px height=512px>
	<h5 align="center">Figura 8 - Função drawTriangle()</h5>
	<br>
</p>

### Interpolação Linear de Cores

Como última tarefa do projeto, fizemos a interpolação das cores desde o começo da linha até o fim. Com isso, uma transição gradual de cores acontece dependendo da distância entre seus pontos finais e iniciais.

Primeiro, foi criada uma função chamada **setarDist()**, que recebe como parâmetro dois pixels (inicial e final) e um ponteiro para *struct Steps*, que por sua vez armazena a variação de cor de cada canal dos pixels. A função **setarDist()** é responsável por calcular a hipotenusa desses dois pixels (ou seja, a distância entre eles), a distância entre os componentes RGBA e definir o quanto cada cor irá incrementar/decrementar a cada pixel. Vejamos:

```C++
typedef struct Steps {

    double redStep;
    double greenStep;
    double blueStep;
    double alphaStep;

} Steps;

void setarDist(Pixel inicial, Pixel final, Steps *dist){
    int dx = final.x - inicial.x;
    int dy = final.y - inicial.y;

    double hypo = sqrt(dx*dx + dy*dy);

    //Define o quanto cada cor irá incrementar/decrementar a cada pixel
    dist->redStep = (final.red - inicial.red)/hypo;
    dist->greenStep = (final.green - inicial.green)/hypo;
    dist->blueStep = (final.blue - inicial.blue)/hypo;
    dist->alphaStep = (final.alpha - inicial.alpha)/hypo;
}
```

Por fim, foi criada a função **interpolar()** que recebe como parâmetro um ponteiro para o pixel inicial que será acendido na tela e todas as suas informações de distância contidas em *Steps*. Nessa função, é realizado o cálculo para se obter os novos valores RGBA, baseados na distância que falta para chegar nos valores RGBA do pixel final. Cada ponto a ser gerado na linha vai possuir valores de cor mais próximos do valor final, resultando numa mudança gradual da cor da linha.
```C++
void interpolar(Pixel *inicial, Steps dist){
    inicial->red += dist.redStep;
    inicial->green += dist.greenStep;
    inicial->blue += dist.blueStep;
    inicial->alpha += dist.alphaStep;
}
```

Após isso, temos que implementar a interpolação na função **drawLine()**. De começo, temos que setar os incrementos de cores a cada pixel. Isso é feito logo antes de se colocar o pixel inicial:

```C++
...
Steps steps;
setarDist(inicial,final,&steps);
putPixel(inicial);
...
```
 
Esses cálculos serão sempre realizados antes da impressão do pixel na tela, portanto, sempre antes da chamada **putPixel()**.

```C++
...
interpolar(&linha,steps);
putPixel(linha);
...
```

Esses são os resultados:
<p align="center">
	<br>
	<img src="./images/triangulointerpolado1.png"/ width=512px height=512px>
	<h5 align="center">Figura 9 - Função interpolar()</h5>
	<br>
</p>

<p align="center">
	<br>
	<img src="./images/linhasinterpoladas.png"/ width=512px height=512px>
	<h5 align="center">Figura 10 - Função interpolar()</h5>
	<br>
</p>



### Conclusão


### Referências Bibliográficas

* Notas de aula do Prof. Christian
* [Bleeptrack - Bresenham Algorithmus](http://www.bleeptrack.de/tutorials/bresenham/)
* [Bresenham's line algorithm](https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm)

