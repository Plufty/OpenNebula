# OpenNebula
Projeto SIN142 - Sistemas distribuídos Desafio 1, Open Nebula
Gleidson Vinícius Gomes Barbosa - 6331
OpenNebula - Como fazer?

A instalação do OpenNebula FrontEnd
Neste momento observaremos apenas a máquina FrontEnd para instalar o módulo principal do OpenNebula.
1. Como estamos trabalhando com máquinas virtuais, estarei acessando primeiramente via SSH a máquina FrontEnd para trabalhar sem problemas de desempenho do VMWare.
2. Logo após, entrarei em modo root para não ter problemas com permissões. Para isso utiliza-se o comando sudo -i.
3. Adicionarei o repositório do miniOne que será nossa ferramenta para instalar o OpenNebula FrontEnd. Utilizarei o seguinte comando: wget ‘https://github.com/OpenNebula/minione/releases/latest/download/minione’ Após essa instalação utilizarei o comando apt update para atualizar os pacotes do inux
4. Por fim utilizando a ferramenta minione eu realizarei a instalação do OpenNebula com o comando bash minione – frontend –force nesse caso o –force é necessário pois o disco do VMWare foi alocado dinamicamente, ou seja, apesar de seu tamanho máximo ser 50gb, ele não tem 50gb alocado inicialmente assim sendo necessário ignorar a verificação de espaço.
Durante a execução desse procedimento será solicitado ao usuário uma confirmação para continuar, apenas digite yes e continue.
Observe que ao fim deste procedimento nos é dado um Report com os dados de nossa máquina assim como usuário e senha do OpenNebula. Devemos lembrar de salvá-los, esses dados são essenciais, são eles: User: oneadmin Password: 5UQ4MjEULn
Configurando Open Nebula Node
1. Como no caso da máquina FrontEnd, aqui também utilizarei o ssh e o modo root. Vide passos 1 e 2 do tópico anterior.
2. Importaremos o repositório do OpenNebula com os seguinte comandos: wget -q -O- https://downloads.opennebula.org/repo/repo.key | sudo apt-key add -
3. Após isso adicionaremos o repositório com o seguinte comando: echo "deb https://downloads.opennebula.org/repo/6.2/Ubuntu/20.04 stable opennebula" | sudo tee /etc/apt/sources.list.d/opennebula.list
Nesse ponto também utilizaremos o comando apt update para atualizar os pacotes.
4. Agora faremos a instalação do OpenNebula node com o comando apt-get -y install opennebula-node-kvm
5. Após essa instalação, devemos editar o arquivo libvirtd.conf para isso podemos usar o comando nano /etc/libvirt/libvirtd.conf e devemos editá-lo da seguinte forma:
This is restricted to 'root' by default.
unix_sock_group = "oneadmin"
If not using PolicyKit and setting group ownership for access
control, then you may want to relax this too.
unix_sock_rw_perms = "0777" Original Novo
6. Agora devemos reiniciar o serviço libvirtd com o comando systemctl restart libvirtd
Configurando SSH
1. O primeiro passo é entrar no usuário oneadmin na máquina FrontEnd e executar o comando ssh-keyscan <frontend> <node> >> /var/lib/one/.ssh/known_hosts onde <frontend> será o nome ou IP da máquina FrontEnd e <node> será o IP ou nome da máquina Node.
2. Realizaremos alteração da senha na máquina Node com o comando passwd oneadmin
3. Para habilitar o acesso ssh sem senha ou passwordless ssh utilizaremos o seguinte comando na máquina FrontEnd ssh-copy-id -i /var/lib/one/.ssh/id_rsa.pub <node> onde <node> é o IP ou nome da máquina Node. Logo após esse, também executaremos o comando scp -p /var/lib/one/.ssh/known_hosts <node>:/var/lib/one/.ssh/
4. Por fim o comando scp -p /var/lib/one/.ssh/id_rsa <node> :/var/lib/one/.ssh/ finalizará a questão do passwordless SSH, lembrando que ele deve ser executado na máquina FrontEnd.
5. Como último passo testaremos se nosso SSH sem senha está funcionando normalmente, conectando com ssh <node> e assim que conectar faremos ssh <frontend>, ao confirmar que o acesso foi realizado sem senha, basta dar o comand exit até voltar ao root. Lembrando que onde temos <node> e <frontend> entramos com nome ou IP das máquinas.
Configurando Host
Ao fim do nosso tópico de instalação do OpenNebula FrontEnd já temos disponível a interface gráfica, mas optei por citá-la apenas aqui para não confundir as etapas.
1. A GUI pode ser acessada por qualquer máquina na rede utilizando o IP da máquina FrontEnd e seu acesso é feito com o usuário e senha gerados na primeira etapa.
2. Para adicionar o Host iremoms em Infrastructure>Hosts e clicaremos no sinal de +
3. Aqui entraremos com o IP ou nome da máquina Node e clicaremos em Create.
4. Por fim, basta clicar em atualizar até seu host ficar em estado ON.
Subindo uma VM no OpenNebula
Devido a limitações de espaço em minhas VM’s não foi possível utilizar a imagem padrão do CentOS, então foi necessário upar um minimal Linux e gerar um novo template com a minha imagem.
1. Para adicionar a nova imagem vamos a Storage>Images e clicaremos no + e em seguida em Create.
2. Preenchemos o nome e escolhemos upload, depois disso fazemos upload da imagem.
Após fazer o upload da imagem, clicamos em Create.
3. Após esse passo substituiremos a imagem no template padrão em Templates>VMs>CentOS7 e selecionamos Update.
4. Trocaremos a imagem em storage e clicamos em update.
5. Por fim vamos a Instances>VMs e clicamos em +, selecionamos nosso template e criamos a máquina. Obs: aqui eu troquei o nome do template para ttylinux.
6. E por fim a felicidade, temos nossa máquina rodando!
