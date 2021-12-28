# Como escrever um Git Commit
## A mensagem de um commit é importante. Veja aqui como escrevê-la bem.
---
## Tradução livre do '[How to Write a Git Commit Message](https://cbea.ms/git-commit/)' para português
---

![git log](https://imgs.xkcd.com/comics/git_commit.png)


[Introdução](#introdução-por-quê-uma-boa-mensagem-no-commit-importa) | [As Sete Regras](#as-sete-regras-para-um-commit-foda) | [Dicas](#dicas) 


## Introdução Por quê uma boa mensagem no commit importa

Se você procurar em um log de algum repositório Git aleatóriamente, você provavelmente vai pensar que seus commits são mais ou menos bagunçados. Por exemplo, dê uma olhada [aqui](https://github.com/spring-projects/spring-framework/commits/e5f4b49?author=cbeams) nesses meus commits antigos no repo do Spring:

    $ git log --oneline -5 --author cbeams --before "Fri Mar 26 2009"
  
    e5f4b49 Re-adding ConfigurationPostProcessorTests after its brief removal in r814. @Ignore-ing the testCglibClassesAreLoadedJustInTimeForEnhancement() method as it turns out this was one of the culprits in the recent build breakage. The classloader hacking causes subtle downstream effects, breaking unrelated tests. The test method is still useful, but should only be run on a manual basis to ensure CGLIB is not prematurely classloaded, and should not be run as part of the automated build.
    2db0f12 fixed two build-breaking issues: + reverted ClassMetadataReadingVisitor to revision 794 + eliminated ConfigurationPostProcessorTests until further investigation determines why it causes downstream tests to fail (such as the seemingly unrelated ClassPathXmlApplicationContextTests)
    147709f Tweaks to package-info.java files
    22b25e0 Consolidated Util and MutableAnnotationUtils classes into existing AsmUtils
    7f96f57 polishing

Cristo! Compare com esse, [mais recente](https://github.com/spring-projects/spring-framework/commits/5ba3db?author=philwebb) 
do mesmo repo:

    $ git log --oneline -5 --author pwebb --before "Sat Aug 30 2014"
    
    5ba3db6 Fix failing CompositePropertySourceTests
    84564a0 Rework @PropertySource early parsing logic
    e142fd1 Add tests for ImportSelector meta-data
    887815f Update docbook dependency and generate epub
    ac8326d Polish mockito usage

Qual desses você acha mais fácil de entender?

O primeiro varia em tamanho e forma; o segundo é consciso e consistente.
O primeiro é o que sempre acontece por padrão; o segundo nunca acontece por acaso.

Enquanto muitos logs de repos parecem com o primeiro, há exceções. O [Linux](https://github.com/torvalds/linux/commits/master) e o próprio [Git](https://github.com/git/git/commits/main) são ótimos exemplos. Olhe o [Spring Boot](https://github.com/spring-projects/spring-boot/commits/master), ou qualquer repo do [Tim Pope](https://github.com/tpope/vim-pathogen/commits/master).

Os _contributors_ desses repos sabem que um commit bem escrito é o melhor jeito de comunicar o *context* da alteração para os *fellow* devs (e também para seu eu do futuro). Um `diff` irá te dizer *o quê* mudou, mas apenas um commit pode realmente te dizer o *por quê*. Peter Hutterer [explica bem](http://who-t.blogspot.co.at/2009/12/on-commit-messages.html):

> Reestabelecer o contexto de um pedaço de código é perda de tempo. A gente não pode achar que nunca irá acontecer, então nossos esforços devem ser em [reduzi-los](http://www.osnews.com/story/19266/WTFs_m) o máximo possível. Commits podem fazer exatamente isso, *um commit mostra se um dev é um bom collaborator ou não*

Se você ainda não pensou muito sobre o que faz um commit ser decente, pode ser porque você ainda não usou muito `git log` e outras ferramentas relacionadas. Isso é um ciclo vicioso: Porque o histórico dos commits é desustruturado e inconsistente, a pessoa não perde tempo usando ou se importando com isso. E porque não é usado ou cuidado, continua sendo desestruturado e inconsistente.

Mas um histórico bem cuidado é uma coisa linda e bem útil. `git blame`, `revert`, `rebase`, `log`, `shortlog` e outro comandos fazem maravilha. Revisar outros commits e pull requests começa a fazer sentido, e de repente podem ser feito separadamente. Entender porque algo aconteceu meses ou anos atrás se torna não só possível mas eficiente. 

O sucesso de longo prazo de um projeto está (entre outras coisas) na sua manutenibilidade, e o mantenedor tem poucas ferramentas mais poderosas do que o log do próprio projeto. É valido usar um tempo para aprender como cuidar dele decentemente. O que pode ser demorado no começo passa a ser um hábito, e eventualmente uma fonte de orgulho e produtividade para todos os envolvidos.

Neste post, Eu vou mostrar apenas o elemento mais básico de como manteu seu histórico de commits saudavél: como escrever uma mensagem de commit. Há outras práticas importantes como squashar commits que eu não irei cobrir aqui. Talvez eu irei escrever sobre isso no próximo post.

A maioria das linguagens de programação tem convenções bem estabelecidas sobre o que constitui idiomatic style (//TODO), como nomenclatura, formatação, etc. Há variações dessas convenções, claro, mas a maioria dos dev concorda que escolher apenas uma é muito melhor do que o caos que orbita quando todo mundo faz o que quer.

O foco de um time sobre seus commtis não devia ser diferente. Para criar um histórico util, o time deve primeiro definir uma convenção que defina pelo menos 3 coisas:

**Estilo.** Sintaxe de marcação, quantidade de colunas, gramática, letras maiusculas/minusculas, pontuação. Deixe isso definido, sem que ninguém precise adivinhar e o faça o mais simples possível. O resultado final vai ser um log extraordinariamente consistente que não é só bom fácil de ler mas que realmente *é lido* regularmente.

**Conteúdo.** Que tipo de informação o corpo do commit (se existir) deve conter? O que *não* deve conter?

**Metadata.** Como deve ser o número do Pull Request, ID do issue, etc. deve ser referenciado?

Felizmente, existem convenções bem estabelecidas que dizem o que um commit bem feito tem que ter. (//TODO) Indeed, many of them are assumed in the way certain Git commands function. Não há nada que você precise reinventar. Apenas siga as [sete regras](#The-seven-rules-of-a-great-git-commit-message) abaixo e você está no caminho de commitar como um profissa.


## As sete regras para um commit foda
>*Lembre-se que: [Tudo](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html) [isto](https://www.git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#_commit_guidelines) [já](https://github.com/torvalds/subsurface-for-dirk/blob/master/README.md#contributing) [foi](http://who-t.blogspot.co.at/2009/12/on-commit-messages.html) [dito](https://github.com/erlang/otp/wiki/writing-good-commit-messages) [anteriormente](https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#format-commit-messages).*


1. [Separe o assunto do corpo com uma linha vazia](#separate)
1. [Limite o assunto usando até 50 caracteres](#limit-50)
1. [Comece o assunto com letra maiúscula](#capitalize)
1. [Não termine o assunto com um ponto final](#end)
1. [Use o tempo verbal imperativo no assunto](#imperative)
1. [Limite a largura do corpo usando até 72 caracteres](#wrap-72)
1. [Use o corpo para explicar *o que* e *por que* ao invés de *como*](#why-not-how)

Por exemplo:

    Summarize changes in around 50 characters or less
    
    More detailed explanatory text, if necessary. Wrap it to about 72
    characters or so. In some contexts, the first line is treated as the
    subject of the commit and the rest of the text as the body. The
    blank line separating the summary from the body is critical (unless
    you omit the body entirely); various tools like `log`, `shortlog`
    and `rebase` can get confused if you run the two together.
    
    Explain the problem that this commit is solving. Focus on why you
    are making this change as opposed to how (the code explains that).
    Are there side effects or other unintuitive consequences of this
    change? Here's the place to explain them.
    
    Further paragraphs come after blank lines.
    
     - Bullet points are okay, too
    
     - Typically a hyphen or asterisk is used for the bullet, preceded
       by a single space, with blank lines in between, but conventions
       vary here
    
    If you use an issue tracker, put references to them at the bottom,
    like this:
    
    Resolves: #123
    See also: #456, #789


### 1. Separe o assunto do corpo com uma linha vazia

Direto do `git commit` [manpage](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion):

> Though not required, it’s a good idea to begin the commit message with a single short (less than 50 character) line summarizing the change, followed by a blank line and then a more thorough description. The text up to the first blank line in a commit message is treated as the commit title, and that title is used throughout Git. For example, Git-format-patch(1) turns a commit into email, and it uses the title on the Subject line and the rest of the commit in the body.

Primeiramente, nem todo commit requer um assunto e um corpo. As vezes uma linha é o suficiente, especialmente quando a alteração é tão simples que nenhum contexto extra é necessário. Por exemplo:

    Fix typo in introduction to user guide

Nada precisa ser dito; se o leitor quiser saber o que estava errado, ele pode simplesmente olhar a alteração em si (usando `git show` ou `git diff` ou `git log -p`)

Se você estiver commitando algo como isso pela linha de comando, é facil usar a flag `-m` no `git commit`:

    $ git commit -m"Fix typo in introduction to user guide"

Porém, quando um commit precisa de um pouco de explicação e contexto, voce precisa escrever um corpo. Por exemplo:

    Derezz the master control program
    
    MCP turned out to be evil and had become intent on world domination.
    This commit throws Tron's disc into MCP (causing its deresolution)
    and turns it back into a chess game.

Commits com corpo não são tão simples de escrever usando a flag `-m`. É melhor escrever o commit usando um editor de texto. Se você ainda não tiver um editor configurado para o Git na linha de comando, leia [essa seção do Pro Git](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

De qualquer jeito, a separação do assunto e do corpo vale a pena quando alguém estiver lendo o log. Aqui está o log completo:

    $ git log
    commit 42e769bdf4894310333942ffc5a15151222a87be
    Author: Kevin Flynn &lt;kevin@flynnsarcade.com&gt;
    Date:   Fri Jan 01 00:00:00 1982 -0200
    
     Derezz the master control program
    
     MCP turned out to be evil and had become intent on world domination.
     This commit throws Tron's disc into MCP (causing its deresolution)
     and turns it back into a chess game.
E com o `git log --oneline`, que printa só o assunto do commit:

    $ git log --oneline
    42e769 Derezz the master control program
Ou `git shortlog`, que agrupa os commits por usuário, ainda só mostrando o assunto:

    $ git shortlog
    Kevin Flynn (1):
          Derezz the master control program
    
    Alan Bradley (1):
          Introduce security program "Tron"
    
    Ed Dillinger (3):
          Rename chess program to "MCP"
          Modify chess program
          Upgrade chess program
    
    Walter Gibbs (1):
          Introduce protoype chess program
Há varios outros contextos em Git que a distinção entre assunto e corpo aparece - mas em nenhum caso eles funcionam sem uma linha de separação no meio.

### 2. Limite o assunto usando até 50 caracteres

50 caracteres não é uma obrigação, apenas uma regra de ouro. Manter o assunto com esse tamanho garante que será legível, e força o autor a pensar duas vezes em como ser mais conciso em explicar o que está acontecendo.
>*Dica: Se você está sofrendo para resumir, você provavelmente está commitando muitas alterações de uma vez só. Foque em commits atomicos (um tópico para um post separado)*

A interface do Github sabe sobre essas convenções. Ela irá te avisar se você passar dos 50 caracteres:

<figure class="kg-card kg-image-card">![gh1](https://i.imgur.com/zyBU2l6.png)</figure>

E irá truncar qualquer assunto maior que 72 caracteres:

<figure class="kg-card kg-image-card">![gh2](https://i.imgur.com/27n9O8y.png)</figure>

Então mire em 50 caracteres, mas considere 72 o máximo possível.

### 3. Comece o assunto com letra maiúscula

Este é simples como parece. Comece o assunto com letra maiúscula.
Por exemplo:

- Acelere a 100 quilometros por hora

Ao invés de:

- <s>acelere a 100 quilometros por hora</s>

### 4. Não termine o assunto com um ponto final

Ponto final é desnecessário no assunto. Além disso, o espaço é precioso quando você está tentando manter o assunto com [50 caracteres ou menos](#limite-o-assunto-usando-até-50-caracteres).

Exemplo:
- Abra a porta devagar

Ao invés de :
- <s>Abra a porta devagar.</s>

### 5. Use o tempo verbal imperativo no assunto

*Modo imperativo* apenas significa "fale ou escreva como se estivesse dando um comando ou instrução". Alguns exemplos:

- Limpe o quarto
- Feche a porta
- Tire o lixo

Cada uma das sete regras que você está lendo agora está escrita no modo imperativo ("Comece o assunto com letra maiúscula", etc.)

O modo imperativo pode soar um pouco rude: e este é o motivo pelo qual a gente não costuma usá-lo. Mas ele é perfeito para o assunto dos commits. Uma motivo é que **o prŕoprio Git usa o modo imperativo sempre quando cria um commit por você**.

Por exemplo, a messagem padrão criado quando `git merge` é usado é:

    Merge branch 'myfeature'
E quando usamos `git revert`:

    Revert "Add the thing with the stuff"
    
    This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.
Ou quando clicamos em "Merge" num Pull Request do Github:

    Merge pull request #123 from someuser/somebranch
Então quando você escreve o commit no imperativo, você está seguindo a convenção interna do próprio Git. Por exemplo:

- Refatore subsistema X para melhor legibilidade
- Remova método deprecated
- Release versão 1.0.0

Escrever desse jeito pode ser um pouco esquitiso no começo. Nós estamos mais acostumados a falar no *Modo indicativo*, que é como reportamos acontecimentos. É por isso que commits normalmente ficam assim:

- <s>Fixed bug with Y</s>
- <s>Changing behavior of X</s>

E as vezes os commits são escritos como uma descrição do que foi feito:

- <s>More fixes for broken stuff</s>
- <s>Sweet new API methods</s>

Para que não fique nenhuma confusão, fica aqui uma regra para que você sempre acerte.

**O assunto de um commit propriamente formatado deve sempre completar a seguinte frase**: 

- Se aplicado, esse commit irá *<u>insira seu assunto aqui</u>*

Por exemplo:

- Se aplicado, esse commit irá *refactor subsystem X for readability*
- Se aplicado, esse commit irá *update getting started documentation*
- Se aplicado, esse commit irá *remove deprecated methods*
- Se aplicado, esse commit irá *release version 1.0.0*
- Se aplicado, esse commit irá *merge pull request #123 from user/branch*

Perceba que isso não funciona para as formas não-imperativas:

- Se aplicado, esse commit irá *<s>fixed bug with Y</s>*
- Se aplicado, esse commit irá *<s>changing behavior of X</s>*
- Se aplicado, esse commit irá *<s>more fixes for broken stuff</s>*
- Se aplicado, esse commit irá *<s>sweet new API methods</s>*

>*Lembre-se: O uso do modo imperativo é importante apenas para o assunto. Você pode relaxar esta restrição quando estiver escrevendo o corpo.*

### 6. Limite a largura do corpo usando até 72 caracteres

Git nunca pula linha automaticamente. Quando você escreve o corpo de um commit, você deve considerar o tamanho da margem, e pular linha manualmente.

A recomendação é para fazer isso em 72 caracteres, para que o Git tenha espaço suficiente para identar o texto enquanto ainda não ultrapassa os 80 caracteres totais.

Um bom editor de texto pode ajudar aqui. É fácil de configurar o Vim, por exemplo, para pular linha em 72 caracteres quando você estiver escrevendo um commit. Tradicionalmente, porém, as IDEs são terríves em prover um suporte decente para formatar commits (Mas em versões mais recentes, IntelliJ IDE [finalmente](https://youtrack.jetbrains.com/issue/IDEA-53615) [melhorou](https://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299) [bastante](https://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-446912) neste contexto)

### 7. Use o corpo para explicar *o que* e *por que* ao invés de *como* 

Esse [commit do Bitcoin Core](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) é bom ótimo exemplo de como explicar o que mudou e porque:

    commit eb0b56b19017ab5c16c745e6da39c53126924ed6
    Author: Pieter Wuille &lt;pieter.wuille@gmail.com&gt;
    Date:   Fri Aug 1 22:57:55 2014 +0200
    
       Simplify serialize.h's exception handling
    
       Remove the 'state' and 'exceptmask' from serialize.h's stream
       implementations, as well as related methods.
    
       As exceptmask always included 'failbit', and setstate was always
       called with bits = failbit, all it did was immediately raise an
       exception. Get rid of those variables, and replace the setstate
       with direct exception throwing (which also removes some dead
       code).
    
       As a result, good() is never reached after a failure (there are
       only 2 calls, one of which is in tests), and can just be replaced
       by !eof().
    
       fail(), clear(n) and exceptions() are just never called. Delete
       them.

Dê uma olhada no [diff completo]() e pense quanto tempo o autor está economizando de futuros desenvolvedores por ter tido o tempo de prover todo o contexto neste commit. Se ele não tivesse o feito, provavelmente o contexto teria se perdido para sempre

Na maioria dos casos, você pode deixar de lado os detalhes de como uma alteração foi feita. O código é geralmente auto explicativo (e se ele for tão complexo que precisa de uma melhor explicação, você pode comentar dentro do próprio código). Apenas foque em deixa claro os motivos porque você fez a mudança em primeiro lugar-como aquilo funcionava antes da alteração (e os problemas disso), o jeito que funciona agora, e porque você decidiu resolver daquele jeito.

O próximo mantenedor que irá te agradecer pode ser você mesmo

## Dicas
### Aprenda a amar a linha de commando. Deixe a IDE para trás.

Por tantas razões quantas existem Git subcommands, é aconselhavel adotar a linha de commando. Git é incrivelmente poderoso; IDEs também são, mas cada uma de um jeito diferente. Eu uso IDE todo dia (IntelliJ IDEA) e eu já usei outras extensivamente, mas eu nunca vi uma extensão de IDE para Git que conseguisse chegar perto da facilidade e poder da linha de commando (desde que você saiba usá-la).

Certamente as funções de IDEs acopladas ao Git são inestimavéis, como chamar `git rm`quando você deleta um arquivo, e a coisa certa com `git` quando você renomeia um. Onde tudo começa a se perder é quando você tenta usar commit, merge, rebase, ou fazer alguma análise sofisticada de histórico pela IDE.

Quando se trata de usar todo o poder do Git, é sempre a linha de comando.

Lembre-se que se você usa Bash ou Zsh ou Powershell, existem [tab](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Bash) [completion](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Zsh) [scripts](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell) que tira muito da dor de ter de se lembrar dos subcomandos e switches. 

## Leia Pro Git
O livro [Pro Git](https://git-scm.com/book/en/v2) está disponível online gratuitamente e é fantástico. Aproveite! 

*header image credit: [xkcd](https://xkcd.com/1296/)*
- - -

