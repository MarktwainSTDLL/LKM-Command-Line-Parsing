# LKM-Command-Line-Parsing

  👾 Hoje iremos falar sobre Command Line Parsing diretamente no LKM. Esse é a <b>segunda parte</b> da nossa série de estudos para Rootkits.


  - Compreendendo a estrutura do nosso LKM.
  - Criando novos módulos e funções de int, string, long, array e short.
  - Imprimindo as funções nos Registros do Kernel.
    - Entendendo para que servem as FLAGS de Registros.
    - Entendendo sobre as novas bibliotecas.
<br>
<br>

# Compreendendo a estrutura do nosso LKM.

<img src="https://imgur.com/oAcCbs8.png">
<br>
  - Temos então todo nosso cabeçalho de <code>#include's</code> com algumas bibliotecas que expliquei no artigo passado que ensinei a construir um <a href="https://github.com/MarktwainSTDLL/Linux-Kernel-Module-basic-explanation/">LKM</a> simples. Nesse artigo estarei explicando sobre as novas bibliotecas que foram importadas.
  <br>
  <br>
  - Possuímos abaixo do nosso cabeçalho as funções de licenciamento e descrição. Como não expliquei no artigo passado, nesse artigo irei explicar para que elas servem e como vê-las em nosso LKM montado.
  <br><br>
  <code>Bom, podemos notar pela seguinte função após a montagem do nosso LKM: A partir da versão do Kernel 2.6, tiveram algumas mudanças na nomenclatura de arquivos que são compilados para trabalhar com Kernel. A antiga extensão .o que hoje é utilizada a .ko (Kernel Object) é uma forma mais fácil de distinguir os arquivos de objetos convencionais. O motivo pela qual isso ocorre se dá ao fato da existência de um arquivo chamado .modinfo onde são mantidas as informações adicionais sobre os módulos.
  <br>
  <br>
  Podemos checar nosso LKM para sabermos qual é o tipo de informação dele. Digite o comando ( modinfo CommandParsing.ko ) e veja os resultados que são retornados em seu terminal.
 </code>
 <br>
 <br>
 <img src="https://imgur.com/Ej6qWOt.png">
 <br><br>
 <code>Podemos então verificar que as opções que foram escritas dentro do campo MODULE_LICENSE e MODULE_DESCRIPTION tiveram um retorno dentro do terminal. Podemos notar que além dessas informações, foram retornadas outras informações do PARM que estaremos falando sobre elas daqui a pouco. Bom, acredito que surgiu uma dúvida em sua mente a respeito disso, certo? "Para que essas informações servem?" Respondendo a essa dúvida, essas informações dão mais credibilidade ao usuário do nosso LKM. As funções como MODULE_AUTHOR, MODULE_LICENSE, MODULE_DESCRIPTION e etc, servem para as informações abaixo.</code>
<br><br>

  - MODULE_AUTHOR -> Identifica quem é o autor do módulo escrito.
  - MODULE_LICENSE -> Identifica de quem é a licença do módulo escrito.
  - MODULE_DESCRIPTION -> Essa função permite que o autor do módulo explique um pouco mais sobre a sua finalidade para que o usuário não fique com um pé atrás a respeito do módulo a ser usado.

  <b>Uma coisa interessante que vale ressaltar, é que todas essas macros estão no <code>linux/modules.h</code> que foi importado em nosso projeto. Ou seja! Elas não são usadas pelo próprio kernel. Elas são apenas documentações e podem ser visualizadas pelo <code>objdump</code> e caso você tenha dúvida de como elas são usadas, procure em <code>linux/drivers</code> e entenda como os desenvolvedores de módulos utilizam elas em seus projetos.</b>
<br><br>

<img src="https://imgur.com/bYERex4.png">
<br>

 - Bom, agora vamos falar sobre toda essa estrutura do nosso LKM na qual definimos todas essas variáveis no corpo do nosso LKM.
    - Definimos então as seguintes variáveis: <code> short, int, char e long </code>.
    <br><br>
    🌟 - short/long: assim como long, ele é um modificador do tipo inteiro que altera o tamanho dos bytes.<br>
    🌟 - int: a variável do tipo inteiro é declarada como uma função que trabalha apenas com números.<br>
    🌟 - char: a variável do tipo char é declarada como uma função que trabalha apenas com caracteres.<br>
    <br>
    
  - O que é a module_param()?
    - Bom, ela é um macro que é importadas da biblioteca <code>linux/moduleparam.h</code> para configurar o mecanismo das variáveis que setamos em nosso código. A macro module_param() oferece suporte para 3 argumentos: o nome da nossa variável, seu tipo e permissão para os arquivos que correspondem ao sysfs. Variáveis do tipo inteiro podem ser assinados normalmente mas caso você desejar usar arrays de inteiros e strings, recomendo que procure por <code>module_param_array() e module_param_string()</code>.
    <br><br>
    - Vale ressaltar que arrays também são suportados mas a história é totalmente diferente do que era na versão do Kernel 2.4. Para que você possa controlar o número de parametros, é necessário que você passe um ponteiro por uma variável de contagem como terceiro parâmetro. A seu critério você pode ignorar a contagem e passar <b>NULL</b>.
    <br><br>
    
 - O que é MODULE_PARM_DESC()?
    - Assim como a sua companheira <code>module_param()</code>, ela é uma macro que é usada para documentar argumentos que o módulo pode receber. Ela oferece suporte para 2 argumentos: o nome da nossa variável e uma string de formato livre.
    <br><br>

<img src="https://imgur.com/lYvbnWa.png">
<br>

  - Temos então as nossas duas funções de inicialização e saída do nosso LKM. Essas funções são compostas por alguns argumentos que foram escritos dentro delas. Vamos analisar!
   
   <code>Possuímos então a nossa variável do tipo inteiro I que foi setada para trabalharmos a nossa função de intArray. Logo abaixo, temos a nossa função printk() que imprime mensagens no Registro do Kernel. E se você deseja entender sobre ela e qual a sua diferença para o printf(), clique <a href="https://github.com/MarktwainSTDLL/Linux-Kernel-Module-basic-explanation/">aqui</a>. Mas vamos dar continuidade ao nosso documento. Dentro do printk() existe uma flag chamada KERN_INFO. Essa flag, assim como as suas <a href="https://www.kernel.org/doc/html/latest/core-api/printk-basics.html">irmãs</a>, é uma função que permite registrar o nível de logs dentro do buffer de registros do kernel. No final de cada printk() contém o nome de uma variável, pois, isso permitirá que cada valor das variáveis sejam impressos no registro do kernel. Temos a função FOR que foi escrita para trazer dois tipos de valores: valor 0 e valor 1. O valor 0 sempre será o valor que foi passado por linha de comando e o valor 1 será o valor que foi setado na variável intArray.</code>
<br><br>
<code>Confira abaixo:</code><br>
<img src="https://imgur.com/HEoxhpq.png">
<br><br>

  <code>Além da primeira função de inicialização, possuímos também a função de saída, ou seja, de descarregamento do buffer no Registros do Kernel. A mensagem aparecerá após digitarmos { rmmod CommandParsing.ko } e com isso a gente reconhece que nosso buffer do kernel foi descarregado.</code>.
<br><br>

<img src="https://imgur.com/p2RykqD.png">
<br><br>
  - E por fim, temos os nossos macros de inicialização e descarregamento do nosso LKM no Kernel que são importadas da biblioteca <code>linux/modules.h</code>.
<br><br>

<h1> Como funciona o Makefile? </h1>

<br>
<img src="https://imgur.com/mvSTHSO.png"><br>
  - 🔥 Possuimos o nosso arquivo de Makefile e irei explicar sobre como ele funciona mas basicamente do ponto de vista técnico, a única função importante dele é o primeiro comando, já que as funções "all" e "clean" foram adicionados por pura conveniência. Sabendo disso, apenas digite o queridíssimo <code>make</code> para que seu LKM seja criado com sucesso.
<br><br>
  - 💥 Após a compilação de todo projeto, digite <code>insmod CommandParsing.ko</code> e logo afrente passe um parametro como por exemplo: <code>insmod CommandParsing.ko intArray=-1337 strings="Hello World!"</code>. E para que você descarregue seu buffer de registros do kernel, digite <code>rmmod CommandParsing.ko</code>.

<br><br><br>

Bom, isso é tudo pessoal! Vou continuar postando artigos sobre LKM e RK aqui em meu GitHub! Espero vocês no próximo Git!
<br><br>

~# Mark Disconnected... 
