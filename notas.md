# Módulo 16 - Automatize tarefas com Gulp
## Notas
### Aula 1 - Configure o Gulp
#### **Sobre a aula**
* realizar a instalação do Gulp de forma global e como dependência de projeto;
* configurar um projeto gulp;
* criar arquivos .gitignore.
#### **Anotações**
O Gulp é uma ferramenta de automação de processos, por exemplo a compilação do SASS e do LESS, compressão de imagens e arquivos JS;  
gulpjs.com é o site do gulp, instalamos o gulp globalmente com o comando npm install --global gulp-cli;  
em seguida chamamos o comando npm init já na pasta de desenvolvimento para criarmos o arquivo package.json que é utilizado para gestão de pacotes e configurações do projeto, contem informações como dependências, scripts e metadados;  
depois instalamos o gulp nas dependencias do projeto através do comando npm install --save-dev gulp, isso vai criar o package-lock.json e também a pasta node_modules, por fim rodamos o gulp com npm run gulp, porem aparece uma mensagem de erro: no gulpfile found, então criamos manualmente a pasta gulpfile.js;  
por fim criamos o .gitignore para a pasta node_modules, que somente é necessária no desenvolvimento do projeto, portanto não precisa ser comitada;  
### Aula 2 - Crie tarefas
#### **Sobre a aula**
* compreender o coneito de automação de tarefas;
* criar tarefas no Gulp, utilizando funções;
* entender como essas tarefas podem ser executadas automaticamente;
* distinguir entre tarefas públicas e privadas no Gulp.
#### **Anotações**
antes de inicializarmos a automação com o Gulp precisamos conhecer uma tecnologia que é o ES Modules (ESM) que é uma forma da gente modularizar o javascript, assim a gente consegue escrever varios arquivos importando um arquivo no outro, para isso vamos criar o arquivo sum.js, ele vai conter a função somar, e a função module.exports=somar, em seguida criamos o arquivo aritimetica.js que idealmente conteria também a função subtrair, dividir, multiplicar, mas para exemplificar nós vamos importar a função somar do outro arquivo, para isso criamos uma variável **const** somar = require('./sum'), dai dentro do arquivo aritmetica chamamos console.log('somar:, somar(10,20)), dai executamos o arquivo através do node e o comportamento esperado é mostrado na tela;  
o gulpfile é um arquivo js que é chamado pelo comando run gulp, no gulpfile podemos ter uma tarefa default através da função exports.default = funcaoPadrao ou entao outras tarefas com outros nomes através de exports.dizOi = dizOi;, podemos chamar npm run gulp dizOi para realizar somente a tarefa desejada, ambos esses exemplos são de tarefas públicas, elas também podem ser tarefas privadas, as públicas são aquelas que exportamos e conseguimos chamar via linha de comando, uma tarefa privada é uma função js que será chamada por outra função (essa precisa ser pública).  
### Aula 3 - Execute tarefas em série
#### **Sobre a aula**
* compreender o conceito e a importância da execução de tarefas em série no Gulp;
* configurar e executar tarefas em série.
#### **Anotações**
tarefas no gulp podem ser executas em serie ou em paralelo, nessa aula aprenderemos sobre tarefas em serie, vamos importar o proprio pacote do gulp que instalamos, fazemos isso com **const** gulp = require('gulp');  
isso vai permitir que utilizemos funções nativas do gulp, por exemplo podemos chamar como exports.default = gulp.series (funcaoUm, funcaoDois, funcaoTres), sendo series uma função nativa do gulp, que nada mais é do que a realização em serie de tarefas.
### Aula 4 - Execute tarefas em paralelo
#### **Sobre a aula**
* executar tarefas em paralelo no Gulp;
* compreender quando essa abordagem é benéfica em relação as tarefas em série.
#### **Anotações**
basicamente para executar as tarefas em paralelo utilizamos outra função do gulp o gulp.parallel(funcaoUm, funcaoDois, funcaoTres), rodamos o npm run gulp e o node roda ambas as tarefas paralelamente, para exemplificar melhor adicionamos o conteudo da funcaoPadrao dentro de outra função setTimeout, isso fez com que no segundo teste a funcaoPadrao que terminava antes agora termina depois, mais do que isso, todo o tempo de rodagem do gulp é 2.02 s, ou seja ambas as funções rodaram em paralelo, já que o tempo setado para funcaoPadrao foi de 2 segundos;  
Há um ganho de performance usando o Gulp de forma paralela, nem sempre vamos poder utilizar o paralelismo no Gulp, digamos que queremos compilar um arquivo sass e minificá-lo, essas duas ações somente podem ser feitas em serie, já que a segunda depende da primeira;
### Aula 5 - Use Gulp para compilar SASS
#### **Sobre a aula**
* compreender o propósito da automação de tarefas com o Gulp no trabalho de desenvolvimento Front-End;
* instalar e configurar os plugins necessários no Gulp para lidar com compilação de arquivos SASS, compressão de imagens e outras tarefas comuns;
* criar tarefas personalizadas no Gulp para automatizar processos de desenvolvimento.
#### **Anotações**
Até agora conhecemos o gulp como ferramento de forma bem crua, investigando as tarefas, vendo como elas são executadas (seriais e paralelas), mas isso até então não é funcional pra gente, não agrega nenhum valor como desenvolvedor front-end;  
Nosso objetivo nessa aula é criar uma tarefa que execute a compilação dos arquivos Sass; para isso vamos precisar acessar os plugins do gulp (são diversos, estilos, compressão de imagens, compressão de arquivos js); os plugins são pacotes do npm, primeiro instalamos npm install --save-dev gulp-sass, em seguida npm install --save-dev sass;  
agora no gulpfile.js vamos importar ambos os plugins, essa é uma importação dupla, **const** sass = require('gulp-sass')(require('sass')), o primeiro pacote é responsável por integrar o sass com o gulp, mas quem vai fazer a compilação do sass é o pacote do sass;  
feito a importação vamos programar a tarefa; criada a função compilaSass com return gulp.src('./source/styles/*scss).pipe(sass()); .src é uma função do gulp que pega os arquivos fonte, .pipe é outra função do gulp que encadeia funções que recebe como argumento a função que será encadeada, que no caso é a função sass(), agora precisamos definir o destino da compilação, fazemos isso adicionando mais uma .pipe e dentro dela a função gulp.dest('./build/styles');  
agora só falta exportar a função compilaSass, fazemos isso exportando a tarefa sass através do comando exports.sass = compilaSass;  
ao rodar encontramos um erro, estamos compilando todos os arquivos com extensão .scss ('/*.scss'), ou seja, o arquivo variaveis.css vem vazio, já que variaveis.scss não possui nenhuma regra, vamos corrigir esse comportamento, alterando o parametro da função src para ('./source/styles/main.scss);  
alem disso adicionamos dentro de chaves o parametro outputStyle: 'compressed' para a função sass, isso faz com que o arquivo main.css seja comprimido, ou seja, tem seus espaços e quebras de linha deletados para reduzir os bits do arquivo;  
agora o professor vai demonstrar como o gulp trata os erros de compilação do sass, ele deixa de importar as variáveis mas as utiliza, isso faz com que a compilação apresente mensagem de erro, isso torna o ambiente de desenvolvimento mais seguro, já que um erro será percebido pelo gulp;  
ao abrirmos a pagina index.html observamos através do devTools que a regra css que esta sendo usada pelo navegador é aquela do arquivo final, ou seja, o navegador omite o 'entendimento' do arquivo fonte, queremos que o navegador reconheça o caminho no arquivo fonte por motivos de depuração de código, para isso usamos o source mapping, o gulp possui uma dependencia propria chamada gulp-sourcemaps, instalamos ela no ambiente de desenvolvimento através do --save-dev;  
para utilizar essa dependencia vamos importá-la, fazemos isso com **const** sourcemaps = require('gulp-sourcemaps'), em seguida canalizamos (com pipe()) a função sourcemaps.init entre a gulp.src e a pipe(sass), e depois entre essa e a gulp.dest canalizamos sourcemaps.write('./maps') (ou seja, a função write tem como parametro a localização onde será escrito o mapa, porem já leva como default o mesmo destino do arquivo css, ou seja, nossa função vai escrever o mapa na pasta maps dentro da pasta do gulp.dest);  
para não termos que rodar o comando npm run gulp sass a cada alteração, podemos utilizar os watchers do gulp, funciona da mesma forma que o sass -watch; para configurá-lo vamos exportar uma nova tarefa, no caso demos o nome de watch, ou seja, exports.watch = function () {
    gulp.watch ('./source/styles/*.scss', {ignoreInitial: false}, gulp.series(compilaSass))
};  
nesse caso criamos a função na propria declaração da tarefa, mas suponho que poderiamos te-la criado separadamente e chamado-a na hora da exportação, como temos feito até então;  
a função consiste na função gulp.watch () que recebe como argumentos o que deve ser observado, no caso, todos os arquivos com extensão .scss da pasta styles dentro de source, pode receber entre chaves a configuração ignoreInitial: false, para que alterações feitas antes que o comando passe a rodar também sejam observadas e por fim qual tarefa deve ser realizada caso alterações no primeiro parametro sejam percebidas;  
### Aula 6 - Minifique JS com o Gulp
#### **Sobre a aula**
* compreender a minificação de JavaScript e como ela otimiza o desempenho da web;
* configurar tarefas de minificação de JavaScript usando o Gulp;
* instalar plugins necessários como o uglify;
* tornar arquivos de JavaScript ilegíveis co o obfuscate.
#### **Anotações**
vamos agora criar mais uma tarefa, responsável por comprimir os arquivos JavaScript, para isso vamos fazer uso do plugin gulp-uglify, instalado o plugin nas dependencias de desenvolvimento, importamos 'gulp-uglify' na const uglify e criamos a função comprimeJavaScript cujo retorno é gulp.src('./source/scripts/*.js'), encadeamos com a função uglify e com gulp.dest('./build/scripts');  
ao rodar a tarefa javascript os dois arquivos js aritimetica e sum são comprimidos, interessante notar que o uglify também faz alterações no código, no caso ele alterou o nome dos parametros e inclusive fez com que uma const se tornasse uma let;  
outra funcionalidade interessante é a obfuscação