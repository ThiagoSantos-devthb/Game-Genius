# Genius era um brinquedo muito popular na década de 1980 distribuído pela Brinquedos Estrela
## Projeto para praticas em javascript

## Técnologias
Para esse projeto foram usados Javascript, CSS e Html.

## Descrição
O brinquedo buscava estimular a memorização de cores e sons. Com um formato semelhante a um OVNI, possuía botões coloridos que emitiam sons harmônicos e se iluminavam em sequência. Cabia aos jogadores repetir o processo sem errar.
- Ainda falta implementar os sons.

## Funcionamento
  No Html foram declarados 3 conteiners e estilizado com CSS para gerar o padrão de disco do jogo
 ```sh
  div class="genius">
            <div class="blue"></div>
            <div class="yellow"></div>
            <div class="red"></div>
            <div class="green"></div>
        </div>
        
body{
    margin: 0;
    background-color: rgb(159, 205, 226);
}

.main-game{
    height: 100vh;
    width: 100vw;
    display: flex;
    justify-content: center;
    align-items: center;
}

.genius{
    display: grid;
    grid-template-areas: 'verde vermelho'
    'amarelo azul';
    grid-gap: 10px ;
    border: 1px solid #fff;
    background-color: #fff;
    border-radius: 100%;
    width: 700px;
    height: 700px;
}

.blue{
    grid-area: azul;
    background-color:var(--blue);
    border-bottom-right-radius: 100%;
}

.red{
    grid-area: vermelho;
    background-color:var(--red);
    border-top-right-radius: 100%;
}

.yellow{
    grid-area: amarelo;
    background-color:var(--yellow);
    border-bottom-left-radius: 100%;
}

.green{
    grid-area: verde;
    background-color:var(--green);
    border-top-left-radius: 100%;
}

```
As variaveis da cores foram declaradas

```sh
:root {
    --deep-blue: #110f1b;
    --blue: rgb(23, 101, 173);
    --blue-glow: rgb(73, 167, 255);
    --blue-shadow: rgb(33, 148, 255);
    --green: rgb(30, 180, 0);
    --green-glow: rgb(154, 255, 134);
    --green-shadow: rgb(72, 255, 35);
    --red: rgb(156, 20, 10);
    --red-glow: rgb(252, 89, 48);
    --red-shadow: rgb(255, 39, 39);
    --yellow: rgb(187, 162, 21);
    --yellow-glow: rgb(255, 240, 157);
    --yellow-shadow: rgb(255, 229, 83);
}
```
No jogo original os led´s ascendiam, e para simular foi criado classes para cada cor anterando cor , brilho e saturação.

```sh
.selected0, .selected1, .selected2, .selected3 {
    transition: ease all .125s;
    --light: saturate(1.2) brightness(1.8);
    --light-inside: 0 0 2em;
    --light-halo: 0px 0px 8px;
}

.selected3 {
    box-shadow: inset var(--blue-glow) var(--light-inside); 
    filter: var(--light) drop-shadow(var(--light-halo) var(--blue-shadow));
}

.selected0 {
    box-shadow: inset var(--green-glow) var(--light-inside); 
    filter: var(--light) drop-shadow(var(--light-halo) var(--green-shadow));
}

.selected1 {
    box-shadow: inset var(--red-glow) var(--light-inside); 
    filter: var(--light) drop-shadow(var(--light-halo) var(--red-shadow));
}

.selected2 {
    box-shadow: inset var(--yellow-glow) var(--light-inside); 
    filter: var(--light) drop-shadow(var(--light-halo) var(--yellow-shadow));
}



```
No javascript foram criados dois arrays, um será populado de forma randômica que será o numero da respectiva cor e outro que armazenará a ordem dos clicks. Uma função verifica a ordem das cores geradas com a dos clicks. 

```sh
//checa se os botoes clicados são os mesmos da ordem gerada no jogo
let checkOrder = () => {
    for(let i in clickedOrder) {
        if(clickedOrder[i] != order[i]) {
            gameOver();
            break;
        }
    }
    if(clickedOrder.length == order.length) {
        alert(`Pontuação: ${score}\nVocê acertou! Iniciando próximo nível!`);
        nextLevel();
    }
}

//funcao para o clique do usuario
let click = (color) => {
    clickedOrder[clickedOrder.length] = color;
    createColorElement(color).classList.add(`selected${color}`);

    setTimeout(() => {
        createColorElement(color).classList.remove(`selected${color}`);
        checkOrder();
    },250);
}
```
Cada cor é representada por um numero inteiro.
```sh

//funcao que retorna a cor
let createColorElement = (color) => {
    if(color == 0) {
        return green;
    } else if(color == 1) {
        return red;
    } else if (color == 2) {
        return yellow;
    } else if (color == 3) {
        return blue;
    }
}
```
A função shuffleOrder popula o array order e para cada iteração do loop for "ascende " a cor corresponde ao numero armazenado no array order.
```sh
let shuffleOrder = () => {
    let colorOrder = Math.floor(Math.random() * 4);
    order[order.length] = colorOrder;
    clickedOrder = [];
    

    for(let i in  order) {
        
        let elementColor = createColorElement(order[i]);
        
        lightColor(elementColor, +i, order[i])
       
    }
}

```
A função lightColor é responsavél por "ascender" as cores. A propriedade element é responsavel por apontar o retorno de cada cor, a propriedade turns retorna a variavel de iteração do loop for e a propriedade order retorna o conteudo do array no respectivo indice.
Com isso é possivel obter exatamente o numero da cor para adicionar e remover a classe selected + numero da cor.
Para que uma cor "ascenda" somente após a anterior "apagar" foi adicionado o setTimeout, o setTimeout interno "apaga" a cor e setTimeout externo aguarda o valor do indice do array order + 1 multiplicado por 0.8 segundos. Assim para cada iteração o valor em segundos é dobrado fazendo com que a proxima cor "ascenda" somente depois da anterior "apagar".
```sh
let lightColor = (element, turns, order) => {
   
    setTimeout(() => {
        element.classList.add(`selected${order}`);
        setTimeout(() => {
            element.classList.remove(`selected${order}`);
        },500);

    },800 * turns + 1);
    
}

