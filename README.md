# Easy-Docker-k8s
An easy way to install kubernetes locally


Uma maneira facil e simples de instalar localmente (para teste e desenvolvimento!) o Docker + Kubernetes + Rancher.

Resumo:
Será instalado o Docker, após, será rodado o container em Single Node do Rancher (interface web e gerenciamento), e com ele sera configurado o kubernetes (kubectl) com certificado auto-assinado.

Para rodar o kubernetes localmente, você pode usar o minikube, kind, e outros... 
Mas neste tutorial, não usaremos nenhum deles. https://kubernetes.io/docs/tasks/tools/
O k8s é instalado no rancher, sem a necessidade de alguma outra solução.


OS: Ubuntu (pode ser feito no Raspberry Pi4 tbm).
Ver as paginas oficiais para instalação com Debian e outros...




## Install Docker:

Pacotes necessários:

    sudo apt-get update
    sudo apt-get install apt-transport-https ca-certificates curl gnupg


Chave GPG do repositorio

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg


Repositório

    echo \
    "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Instalação:

    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io



## Install kubectl:

Pacotes necessários:

    sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2 curl

Chave do repo:

    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

Repo:

    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

Instalação do kubectl:

    sudo apt-get update
    sudo apt-get install -y kubectl



## Install helm (opcional!):

    curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
    sudo apt-get install apt-transport-https --yes
    echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    sudo apt-get update
    sudo apt-get install helm



## Instalação do rancher.

Abaixo, vamos rodar o container do rancher:

    docker run -d --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher:latest

pronto :-)

Com o *"unless-stopped"* mesmo reiniciando o computador, ao sbir novamente, o docker voltará com o container. Caso queira, na pagina do rancher há o passo-a-passo sobre configurar um backup.


## Configuração do kubectl:

### certificado:
Acesse a interface do Rancher (https://localhost) - irá definir a senha da interface.

Após logado, vá em **"Settings"** > e clique em **Show cacert** para exibir o certificado. Sem ele, o kubectl irá reclamar ;-)

Copie o conteudo do cacert e crie um arquivo ca-rancher.pem e cole nele.

Após, no mesmo diretório, crie um crt usando o pem:

    openssl x509 -outform der -in ca-rancher.pem -out ca-racher.crt

Copie o crt para o diretório de certificados:

    sudo cp CA_racher.crt /usr/local/share/ca-certificates/CA_rancher.crt

Atualize:

    sudo update-ca-certificates

### kubeconfig

Ao lado do icone do rancher, deve estar como "Global", vá para "local".

Na tela de Local, no lado direito, haverá o botão "kubeconfig file".

Clique nele e siga os passos da tela, ou seja, coloque o conteudo do arquivo dentro ~/.kube/config





## Fim:


Vai trazer os pods, como não há nada instalado no namespace default, ele vai mostrar os pods que o rancher precisa para funcionar.

    kubectl get pods --all-namespaces

Se tiver outro host (uma VM ou outro raspberry Pi, por exemplo), dá para configurar como mais um node do rancher, permitindo assim um load balancer real entre os servidores.



## links:

https://docs.docker.com/engine/install

https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl

https://helm.sh/docs/intro/install/

