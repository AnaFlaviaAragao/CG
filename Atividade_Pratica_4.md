Introdução á Computação Gráfica - Trabalho 3
---

Disciplina: [GDSCO0051] Introdução à Computação Gráfica - Turma 02.

Professor: Christian Azambuja Pagot (email: christian@ci.ufpb.br).

Alunas: 
        
        Ana Flavia S. Aragão Moura; 20160164270

        Thuane Mikaella de França; 11325835

---

## Índice

* [Introdução](#introdução)
* [Setup](#setup)
* [A Atividade](#a-atividade)
	* [Exercício 1 Implementação do Modelo de Reflexão Difuso](#exercício-1-implementação-do-modelo-de-reflexão-difuso)
	* [Exercício 2 Implementação do Modelo de Reflexão Especular](#exercício-2-implementação-do-modelo-de-reflexão-especular)
* [Conclusão](#conclusão)
* [Referências Bibliográficas](#referências-bibliográficas)

---


### Introdução

Este trabalho foi desenvolvido para a disciplina de Introdução à Computação Gráfica, ministrada pelo Prof. Dr. Christian Azambuja Pagot na Universidade Federal da Paraíba, UFPB. 

Com o objetivo de nos familiarizar os modelos de iluminação tradicionalmente utilizados na rasterização: ambiente, difuso e especular (ou Phong). Para isto, é necessario implementar os três modelos utilizando o vertex shader do OpenGL.

---



### Setup

Utilizamos o sistema operacional Linux distribuição Unbuntu 18.04.04 LTS. Para a compilação exige requisitos do OpenGL o OpenGL Mathematics (GLM) e o GLEW OpenGL (Extension Wrangler Library) sejam intsalados. Após isso, para  compilação e execução do Código Template usamos os seguintes comandos dentro da pasta 04_shading,  um comando apoś o outro:

```sh
$ make

$ g++ -Wall -Wextra -Wpedantic -std=c++17 -O0 -g3 -DDEBUG main.cpp -lglut -lGLEW -lGLU -lGL -o shading_gl

$./shading_gl
```

<p align="center">
	<br>
	<img src="./Imagens/figura4.1.png"/ width=720px height=400px>
	<h5 align="center">Figura 1 - Resultado do codigo Template</h5>
	<br>
</p>

### A Atividade


### Exercício 1: Implementação do Modelo de Reflexão Difuso

Nesta atividade modificamo o vertex shader (arquivo vertex_shader.glsl), disponibilizado pelo professor, de forma que ele passe a incluir, além do modelo de iluminação ambiente, também o modelo difuso. Para isto, será necessário calcular o novo valor do vetor normal N, após as transformações feitas pela matriz Model, bem como o vetor L que aponta do vértice para a fonte de luz.Utilizando a sugestões de cálculo destes dois vetores, definido pelo professor.


Foram feitas modificaçãoes nos arquivos, vertex_shader.glsl e nomain.cpp. Para isto, foi necessario calculars cálculos dos vetores N e L, o modelo de iluminação difuso. Modificando assim o arquivo vertex_shader.glsl para adicionar estes passos, utilizandoas sugestões de cálculo dos dois vetores indicada pelo professor.



<p align="center">
	<br>
	<img src="./Imagens/figura4.2.png"/ width=720px height=400px>
	<h5 align="center">Figura 3 - : Renderização do Utah Teapot utilizando o modelo I = Iaκa +Ipκd cos θ (ambiente + difuso),onde Ia = (0.2, 0.2, 0.2), κa = (0, 0, 1), posição da fonte de luz igual a (−2, 2, 1.5), Ip = (0.8, 0.8, 0.8) e κd = (0, 0, 0.8).
	<br>
</p>


### Exercício 2: Implementação do Modelo de Reflexão Especular

<p align="center">
	<br>
	<img src="./Imagens/figura4.3.png"/ width=720px height=400px>
	<h5 align="center">Figura 3 -  Renderização do Utah Teapot utilizando o modelo I = Iaκa + Ip(κd cos θ + κs(cos α)n)
(Phong), onde Ia = (0.2, 0.2, 0.2), κa = (0, 0, 1), posição da fonte de luz igual a (−2, 2, 1.5), Ip =(0.8, 0.8, 0.8), κd = (0, 0, 0.8), κs = (1, 1, 1) e n = 64.</h5>
	<br>
</p>


### Conclusão

### Referências Bibliográficas
