# Computação Gráfica 
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
Este trabalho foi desenvolvido para a disciplina de Introdução à Computação Gráfica, ministrada pelo Prof. Dr. Christian Azambuja Pagot, na Universidade Federal da Paraíba, UFPB. Com o objetivo de nos familiarizar com os algoritmos de rasterização utilizados
em computação gráfica. Aprendemos na prática, como rasterizar pontos, linhas e triângulos através da rasterização das linhas.


O trabalho trata da implementação do algorítmo de Bresenham para a rasterização de primitivas geométricas nas linguagens C/C++.
A compilação exige que os cabeçalhos do OpenGL e a GLUT (The OpenGL Toolkit) estejam instalados. Após instalá-los:

$ make

$ ./cgprog

Com a evolução das tecnologias, e por questões de segurança, os sistemas operacionais atuais impedem o acesso direto à memória de vídeo. Por esse motivo, será usado um framework desenvolvido pelo professor que simula esse acesso à memória.

---
