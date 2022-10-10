## Requerimentos

```
pip install -r python_requirements.txt
```

```
ansible-galaxy collection install 'kubernetes-core:==2.3.2'
```

## Variaveis

| Nome | Descrição | Tipo | Opções | Exemplo | Padrão | Requerido |
|------|-----------|------|--------|:-------:|--------|-----------|
| K8S_AUTH_KUBECONFIG | Arquivo requerido para autenticação no cluster k8s | string | | /Users/"meu usuario"/.kube/config | Null | Sim |
| DEPLOY_TYPE | Tipo de Deploy | string | simple, bluegreen, canary ou rollback | bluegreen | simple | Sim |
| APP_NAME | Label Principal da Aplicação | string | | myapp | Null | Sim |
| APP_NEW_VERSION | Label da nova versão a ser feito o deploy | string | | 1.23.4 | Null | Sim |
| DEPLOYMENT_TEMPLATE_FILE | Caminho do template em jinja2 do deployment | string | | /Users/"meu usuario"/Documents/deploy-example.j2 | Null | Sim |
| Namespace | Namespace a ser feito o deploy | string | | myapp | Null | Sim |
| REMOVE_UNSERVICED_DEPLOYMENT | Remover o deployment que ficará fora do service (Apenas na estrategia bluegreen) | bool | True ou False | True | Null | Não |
| CANARY_PORCENTAGE | Porcentagem de replicas em cima do novo deploy (Apenas na estrategia canary) | bool | True ou False | True | Null | Não |
| DEPLOYMENT_DELAY_SECONDS_CHECK | Tempo em segundos que a task vai esperar ate que o deployment contenha pelo menos 1 replica em estado "Ready" | int | | 5 | 5 | Sim |
| DEPLOYMENT_RETRIES_CHECK | Quantidade em tentativas que a task vai executar a checagem do deployment após execeder o tempo do "DEPLOYMENT_DELAY_SECONDS_CHECK" | int | | 5 | 5 | Sim |

## Tipos de Estratégia

### Blugreen, Canary ou Simple

```
ansible-playbook main.yaml -i inventory/localhost \
    -e APP_NAME=<APP NAME LABEL> \
    -e APP_NEW_VERSION=<VERSION> \
    -e DEPLOYMENT_TEMPLATE_FILE=<FILE>.j2 \
    -e NAMESPACE=<namespace>
```

### Rollback

```
ansible-playbook main.yaml -i inventory/localhost \
    -e APP_NAME=<APP NAME LABEL> \
    -e NAMESPACE=<namespace>
```

> ***bluegreen*** faz o deploy de uma nova versão e atualiza o service para jogar conexão de rede apenas pra ela.

> ***canary*** faz o deploy de uma nova versão e divide a quantidade de replicas baseado na variavel "CANARY_PORCENTAGE" entre as duas versões. Caso já exista dois deployments, o com a menor versão será removido.

> ***simple*** faz o deploy de um único deployment.

> ***rollback*** pega a menor versão atual entre os deployments e atualiza o service para ele.