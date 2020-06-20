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
* [Pixels](#pixels)
* [Algoritmo de Bresenham](#algoritmo-de-bresenham)
* [Linhas](#linhas)
* [Triângulos](#triângulos)
* [Interpolação Linear de Cores](#interpolação-linear-de-cores)
* [Conclusão](#conclusão)
* [Contribuidores](#contribuidores)
* [Referências Bibliográficas](#referências-bibliográficas)

---


### Introdução
Este trabalho foi desenvolvido para a disciplina de Introdução à Computação Gráfica, ministrada pelo Prof. Dr. Christian Azambuja Pagot, na Universidade Federal da Paraíba, UFPB. 

Com o objetivo de nos familiarizar com os algoritmos de rasterização utilizados em computação gráfica. Aprendemos na prática, como rasterizar pontos, linhas e triângulos através da rasterização das linhas.

---

### Pixels

Os monitores da atualidade são compostos por uma malha de pixels, com X pixels de largura por Y pixels de altura, formando um espaço bidimensional. Entretanto, a disposição desses pixels na memória de vídeo se dá de forma linear, onde todos os pixels são armazenados de forma contínua no que chamamos de *colour buffer*. Este fato deve ser levado em consideração quando se trabalha com a manipulação de pixels.

A maioria dos monitores no mercado utilizam o padrão RGBA, onde cada pixel possui 4 componentes de cor: R para vermelho (RED), G para verde (GREEN), B para azul (BLUE) e A para transparência (ALPHA). Cada componente ocupa 1 byte do *colour buffer*, totalizando 4 bytes por pixel. Para facilitar o entendimento, vejamos o seguinte esquema:



<p align="center">
	<br>
	<img src="./images/Screenshot from 2019-07-14 11-30-10.png"/ width=720px height=400px>
	<h5 align="center">Figura 1 - Disposição dos pixels na memória</h5>
	<br>
</p>

Além disso, temos capacidade de endereçar cada pixel (e cada canal de cor) no *colour buffer* da seguinte forma:

<p align="center">
	<br>
	<img src="./Images/Figura1.png"/ width=720px height=400px>
	<h5 align="center">Figura 2 - Endereçamento de pixels no colour buffer</h5>
	<br>
</p>

Com essas informações, foi implementado um tipo estruturado **pixel** contendo todas as informações de coordenadas e cores dele:

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
	<img src="./images/Figura2.png"/ width=512px height=512px>
	<h5 align="center">Figura 3 - Função putPixel()</h5>
	<br>
</p>
