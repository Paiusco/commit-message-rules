# Tradução livre do '[How to Write a Git Commit Message](https://cbea.ms/git-commit/)' para português

## Free translation of [How to Write a Git Commit Message](https://cbea.ms/git-commit/) in Brazilian Portuguese (PT-BR)
---

//TODO: FIX IT
[Introduction](#Introduction-why-good-commit-messages-matter) | [The Seven Rules](#seven-rules) | [Tips](#tips) 


## Introdução: Por quê uma boa mensagem no commit importa

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

O sucesso de longo prazo de um projeto está (entre outras coisas) na sua manutenabilidade(//TODO), e o mantenedor tem poucas ferramentas mais poderosas do que o log do próprio projeto. É valido usar um tempo para aprender como cuidar dele decentemente. O que pode ser demorado(//TODO) no começo passa a ser um hábito, e eventualmente uma fonte de orgulho e produtividade para todos os envolvidos.

Neste post, Eu vou mostrar apenas o elemento mais básico de como manteu seu histórico de commits saudavél: como escrever uma mensagem de commit. Há outras práticas importantes como squashar commits que eu não irei cobrir aqui. Talvez eu irei escrever sobre isso no próximo post.

A maioria das linguagens de programação tem convenções bem estabelecidas sobre o que constitui idiomatic style (//TODO), como nomenclatura, formatação, etc. Há variações dessas convenções, claro, mas a maioria dos dev concorda que escolher apenas uma é muito melhor do que o caos que orbita quando todo mundo faz o que quer.

O foco de um time sobre seus commtis não devia ser diferente. Para criar um histórico util, o time deve primeiro definir uma convenção que defina pelo menos 3 coisas:

**Estilo.** Sintaxe de marcação, quantidade de colunas, gramática, letras maiusculas/minusculas, pontuação. Deixe isso definido, sem que ninguém precise adivinhar e o faça o mais simples possível. O resultado final vai ser um log extraordinariamente consistente que não é só bom fácil de ler mas que realmente *é lido* regularmente.

(//TODO)**Content.** Que tipo de informação o corpo do commit (se existir) deve conter? O que *não* deve conter?

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

50 caracteres não é uma obrigação, apenas uma (//TODO) rule of thumb. Manter o assunto com esse tamanho garante que será legível, e força o autor a pensar duas vezes em como ser mais conciso em explicar o que está acontecendo.
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

Ponto final é desnecessãrio no assunto. Além disso, o espaço é precioso quando você está tentando manter o assunto com [50 caracteres ou menos](https://cbea.ms/posts/git-commit/#limit-50)//TODO.

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

Writing this way can be a little awkward at first. We’re more used to speaking in the *indicative mood*, which is all about reporting facts. That’s why commit messages often end up reading like this:

- <s>Fixed bug with Y</s>
- <s>Changing behavior of X</s>

And sometimes commit messages get written as a description of their contents:

- <s>More fixes for broken stuff</s>
- <s>Sweet new API methods</s>

To remove any confusion, here’s a simple rule to get it right every time.

**A properly formed Git commit subject line should always be able to complete the following sentence**:

- If applied, this commit will *<u>your subject line here</u>*

For example:

- If applied, this commit will *refactor subsystem X for readability*
- If applied, this commit will *update getting started documentation*
- If applied, this commit will *remove deprecated methods*
- If applied, this commit will *release version 1.0.0*
- If applied, this commit will *merge pull request #123 from user/branch*

Notice how this doesn’t work for the other non-imperative forms:

- If applied, this commit will *<s>fixed bug with Y</s>*
- If applied, this commit will *<s>changing behavior of X</s>*
- If applied, this commit will *<s>more fixes for broken stuff</s>*
- If applied, this commit will *<s>sweet new API methods</s>*

>*Remember: Use of the imperative is important only in the subject line. You can relax this restriction when you’re writing the body.*

### 6. Wrap the body at 72 characters

Git never wraps text automatically. When you write the body of a commit message, you must mind its right margin, and wrap text manually.

The recommendation is to do this at 72 characters, so that Git has plenty of room to indent text while still keeping everything under 80 characters overall.

A good text editor can help here. It’s easy to configure Vim, for example, to wrap text at 72 characters when you’re writing a Git commit. Traditionally, however, IDEs have been terrible at providing smart support for text wrapping in commit messages (although in recent versions, IntelliJ IDEA has [finally](https://youtrack.jetbrains.com/issue/IDEA-53615) [gotten](https://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299) [better](https://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-446912) about this).
### 7. Use the body to explain what and why vs. how

This [commit from Bitcoin Core](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) is a great example of explaining what changed and why:

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
Take a look at the [full diff](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) and just think how much time the author is saving fellow and future committers by taking the time to provide this context here and now. If he didn’t, it would probably be lost forever.

In most cases, you can leave out details about how a change has been made. Code is generally self-explanatory in this regard (and if the code is so complex that it needs to be explained in prose, that’s what source comments are for). Just focus on making clear the reasons why you made the change in the first place—the way things worked before the change (and what was wrong with that), the way they work now, and why you decided to solve it the way you did.

The future maintainer that thanks you may be yourself!
## Tips
### Learn to love the command line. Leave the IDE behind.
For as many reasons as there are Git subcommands, it’s wise to embrace the command line. Git is insanely powerful; IDEs are too, but each in different ways. I use an IDE every day (IntelliJ IDEA) and have used others extensively (Eclipse), but I have never seen IDE integration for Git that could begin to match the ease and power of the command line (once you know it).

Certain Git-related IDE functions are invaluable, like calling `git rm` when you delete a file, and doing the right stuff with `git` when you rename one. Where everything falls apart is when you start trying to commit, merge, rebase, or do sophisticated history analysis through the IDE.

When it comes to wielding the full power of Git, it’s command-line all the way.

Remember that whether you use Bash or Zsh or Powershell, there are [tab](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Bash) [completion](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Zsh) [scripts](https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell) that take much of the pain out of remembering the subcommands and switches.
### Read Pro Git
The [Pro Git](https://git-scm.com/book/en/v2) book is available online for free, and it’s fantastic. Take advantage!

*header image credit: [xkcd](https://xkcd.com/1296/)*
- - -

