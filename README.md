# Githook

Repositório de exemplo para utilização do Git Hooks. 

### O que é o Git Hook? 

Hooks são scripts que podem ser executados antes ou depois que o git execute um comando como "git commit", "git push", "git fetch" e etc. Um ponto importante é que os hooks são pre-definidos pelo Git, sendo assim cada nome de arquivo possível indica em qual momento será executato o script. 

As definições de scrit sempre ficam no diretório: "${PATH_PROJETO}/.git/hooks/", por padrão existem vários scripts de exemplo dentro desse diretório todos com o sufixo "sample".

## Pre-Push - O exemplo 1.

O hook "pre-push" permite executar um script antes de ser realizado o push de uma branch. O script de exemplo nesse repositório verifica em um repositório com 2 projetos .Net Core, um projeto WebAPI e outro de UnitTests.

Para validar o build e executar os unit tests antes de ser efetuado o push, segue o script abaixo:

```

#!/bin/bash
protected_branch='master'
current_branch=$(git symbolic-ref HEAD | sed -e 's,.*/\(.*\),\1,')
RED='\033[0;31m'
GREEN='\033[1;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# only run this if you are pushing to master

echo -e "${YELLOW}Running pre push to master check...${NC}"

echo -e "${YELLOW}Trying to build tests project...${NC}"
    
#Let's speed things up a little bit
DOTNET_CLI_TELEMETRY_OPTOUT=1
DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1

cd src
cd Hooks
    
# build the project
dotnet build

# $? is a shell variable which stores the return code from what we just ran
rc=$?
if [[ $rc != 0 ]] ; then
   echo -e "${RED}Failed to build the project, please fix this and push again${NC}"
   echo ""
   exit $rc
fi

# navigate to the test project to run the tests
# TODO: change this to your test project directory
cd ..
cd tests
cd Hooks.Tests

echo -e "${YELLOW}Running unit tests...${NC}"
echo ""

# run the unit tests
dotnet test

# $? is a shell variable which stores the return code from what we just ran
rc=$?
if [[ $rc != 0 ]] ; then
    # A non-zero return code means an error occurred, so tell the user and exit
    echo -e "${RED}Unit tests failed, please fix and push again${NC}"
    echo ""
    exit $rc
fi

# Everything went OK so we can exit with a zero
echo -e "${GREEN}Pre push check passed!${NC}"
echo ""
exit 0

```

O arquivo deve ficar no diretório dos hooks com o nome "pre-push.sh". Após salvar o arquivo basta realizar uma alteração no código e executar um commit e push para o script ser executado.
