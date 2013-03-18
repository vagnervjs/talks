
#Git

Sistema para controle de versionamento de arquivos

- Introdução
- Instalação 
- Configuração
- Iniciando um projeto
- Áreas do git
- Commit
- Gerenciamento de índice
- Stash / Branch
- Merge / Rebase
- Modelo Centralizado / Modelo Distribuído
- Extras


---
Dica: não se preocupar, o ínicio sempre é díficil e doloros.

História: Criado por Linus Towards para gerenciar o desenvolvimento do kernel do linux (antes usando bitKeeper).
Linus tem a opnião de que CVS e Subversion são muito ruins.

Git não tem nada a ver com o Subversion.

Git é um filesystem distribuido. Ou seja não só codigo fontes, e sim qualquer tipo de arquivos.

Muito eficiente e confiável.

Utiliza SHA1 para identificação dos commits.

Dificilmente ocorre perca de arquivos no git.

---

##Instalando 

###Linux

- Instalando git:
	sudo apt-get install git-core git-svn ssh
- Gerando chaves ssh:
	ssh keygen -t rsa 

###Windows
- http://git-scm.com/download/win
ou
- msysgit http://code.google.com/p/msysgit/
- Download
- Instalar, next, next, folder, menus de contexto, usar como ambiente linux, checkout as (última opção), instalar, Done
- Git bash
- Gerando chaves ssh:
	ssh keygen -t rsa 

###Mac

- http://git-scm.com/download/mac
ou 
- github.com/mxcl/homebrew
- brew install git
- Gerando chaves ssh:
	ssh keygen -t rsa
	

##Primeiros passos

###Configuração

- Nome e email para identificar autor
git config --global user.name "vagnervjs"
git config --global user.email "vagnervjs@gmail.com"

cat /Users/vagner/.gitconfig

color: auto

###Iniciando um projeto

Dica: sempre ler as mensagens

- Iniciar git em um projeto
git init 

pasta .git

- Ajuda para comandos:
git help init 

- Status do projeto
git status

Mostra o branch atual.

Untracked files: arquivos não rastreados, git não conhece esses arquivos ainda.

Ações para fazer: ex: add arquivos

####Áreas de trabalho

1. Working directory: diretório atual de trabalho
2. .git: repositório de dados
3. Stage area: índice

####Continuando

- Adicionando arquivos para o repositório
git add . file.txt (arquivo file.txt)
git add . "*.txt" (todos arquivos .txt)
git add . (Todos arquivos do working directory)

Após add os arquivos ficam como new file, ou seja, a serem adicionados no próximo commit.

- Para retirar do índice e não enviar para o commit:
git rm --cached <file>

####Commit

git commit -m "Primeiro commit"

#####Log

git log 
Mostra os commits feitos e seus SHA1

##Estrutura do commit

Commit é um pacote, um envelope.

Tag (v0.1) 
	Commit (sha1)
		Tree (/public)
			Blob (index.html)
			
Branch e tags apontam sempre para commits

Commits posuem um identificador em SHA1, o que torna praticamente impossível conflito de commits.

Mesmo utilizando trechos do SHA1 (até 5 caracteres no máximo), ainda assim é possível identificar o commit.

O git nunca duplica conteúdos entre commits, apenas faz referência ao blob do commit anterior e adiciona os novos arquivos.


##Gerenciando o indice

Após commit e existindo um arquivo untracked

Adicionar arquivo ao commit feito anteriormente:

git add . <file>
git commit -m "Primeiro commit" --amend

git status: nothing to commit

*Válido para commits locais

--

Após modificar arquivo

git stauts: modified: <file>
git add <file>
git status: to be committed

Para desfazer isso: git reset HEAD <file>

voltar ao inicio: git checkout -- <file>

Verificar arquivo: alteração desfeita.

-- 

Criar novos arquivos

git stauts: modified / untracked files
git add .
git status: new file (todos)

Remover arquivo do indice que não é desejado

git reset HEAD (todos os arquivos voltam para untracked)
git reset HEAD <file> (apenas <file> fica untracked)

git commit -a -m "Fazendo op 2" 

-a adiciona apenas arquivos que o git conhece

--
git log --stach : para cada commit mostra o que foi modificado

###Gitignore

Arquivo .gitignore especifica arquivos ou pastas para serem ignoradas pelo git, ou seja, não vai para o commit.

Ex:

criar arquivo em subdiretorio que está no .gitignore

git add /pastaignorada/file.txt

Não vai pois está ignorada

git add -f /pastaignorada/file.txt

-f para forçar add mesmo estando no .gitignore

--

git reset HEAD~1 --hard 

Último commit é jogado fora e a cabeça aponta para o commit anterior.

Esta operação parece destrutiva mas não é:

git reflog: guarda todas as coisas que foram jogadas fora
git merge <sha1 do commit desejado>: volta o commit

--

Vários arquivos e um deles indesejado:

git add .
git rm --cached <file>

ou apenas arquivos desejados

git add -i

--

git reset HEAD~1 --soft: arquivos voltam para indice

git clean -f: limpa arquivos do working directory

##Stashs e branchs

Situação: desenvolvendo uma nova funcionalidade e chega um pedido pare resolver um bug. Não quero fazer commit pois nao terminei.

Quarta area (temporária): stash

git stash (cria stash anonimo)
gti stash list

pode corrigir bug e depois voltar usando:

git stach apply: volta o último

git stash clear

--

git stash save "fazendo algo"

git stash apply stash@[0]: volta  stash desejado

git stash pop: tira da lista, aplica e apaga o stash

git stash drop stash{0}: retira da lista de stash

-- 
Não trabalhe em cima do master

git checkout -b "desenvolvimento"

git checkout desenvolvimento

git branch: lista os branchs disponiveis

git log --graph

git merge bugfix: soma alteraçoes feitas no branch bugfix

Aplicativo gráfico:
gitk --all & 

git rebase master

Repetindo:

git checkout bugfix
edita algo em bugfix
git commit -m "arrumando algo"
gitk --all: vendo o log
Voltando para o master
git checkout master: volta para o master
git merge bugfix: adiciona alteraçoes ao master

Para igualar todos:
git checkout desenvolvimento
git rebase master

git branch -d bugfix: apagando o branch bugfix

Dica: o ideal é que cada branch deve ter uma funcionalidade

Misturar pequenos commits em um unico

git merge <branch> --squash

##Merge e rebase


Estou no master e alguem alterou 

Conflito: editando o mesmo arquivo.

Manter branch atual atulizado:

git rebase master: trazer coisas do master para o branch atual

Conflito: alteração no mesmo arquivo do master e no desenvolvimento.

((no branch))

O git mostra um conflito de cada vez

Verificar arquivo que está em conflito

Após alterar, adiciona arquivo

e continua usando

git rebase --continue

Fazer mesmo exemplo usango merge.

Se você está em um servidor remoto, não deve ser utilizado rebase. Utiliza-se o merge. Pois o rebase é destrutivo.

Com rebase a linha de histório fica reta, com merge é criado um devio.

Merge traz os commits e adiciona mais um.
Rebase junta todos os commits linearmente.

##Repositórios Remotos

git clone teste/.git teste2
git remote: mostra origin
git remote show origin

git checkout -b desenvolvimento origin/desenvolvimento
git branch -a

Editando arquivo do teste2
git commit -m "Update de teste2"
git push origin desenvolvimento: Enviando update para o remote

Ir até o remote e ver alteração

No remote, adicionar

git remote add origin ../teste2/.git

Agora existe mão dupla.

Alterar em teste2 e puxar no teste:
git pull

-- 
git remote rm origin: removendo 

rm -rf teste2
--

###Modelo centralizado

Mudando o cenário:

Dois desenvolvedores, cada um com uma cópia local e um server remoto.

Entra no servidor

mkdir teste.git
cd teste.git

git init --bare

cd .ssh 

nano autorized_keys

No usuario:
cat -/.ssh/id_ras.pu

No servidor
copiar e colar em autorized_keys

echo "chave" >> autorized_keys

Temos uma conexão segura, e sem pedir senha.

--
No usuario:

git remote add origin vagner@172.16.100.198:/home/git/repositorios/teste.git

git remote show origin

git push origin master

git fech origin master: traz alteraçoes do remote para o branch especial <remote>/<branch>

No segundo usuario

git clone vagner@172.16.100.198:/home/git/repositorios/teste.git teste_clone

cd teste_clone

Criando um branch local apontando para um remoto:
git checkout -b desenvolvimento origin/desenvolvimento

Puxando no usuario 1
git pull origin desenvolvimento

Antes de enviar (push)

git fech origin/desenvolvimento 
ou git pull: é o fech seguido do merge
e depois
git rebase origin/desenvolvimento
e por fim
git push origin desenvolvimento (envia já resolvido os conflitos)

###Modelo distribuido

Github

adicionar origem remota 

git fech 
git diff

##Comandos extras

Fato de mudar de branch muda o working directory.

###Tags

É um tipo de etiqueta para identificar momentos do projeto.

git tag v1.0
git push origin v1.0

pode criar novos branhcs e add tag e depois deletar branch

git push --tags: envia todas as tags

git -b teste3 v0.8

###Log

git log --stash
git log --pretty=oneline
git log --pretty=format:"[%an %ad] %h - %s"
git log --pretty=format:"%h - %s" --graph
git log --since=30.minutes
git log --since=30.minutes
git log --since=4.hours --until=2.hours
git log --since=4.hours --until=2.hours --before="2013-03-15"






