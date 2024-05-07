# Atividade-AWS-Docker-Compasso Uol
Sobre a atividade

    Instalação e configuração do DOCKER ou CONTAINERD no host EC2.

    Ponto adicional para o trabalho que utilizar a instalação via script de Start Instance (user_data.sh)

    Efetuar Deploy de uma aplicação Wordpress com:

    Container de aplicação
    RDS database MySQL

    Configuração da utilização do serviço EFS AWS para estáticos do container de aplicação WordPress

    Configuração do serviço de Load Balancer AWS para a aplicação Wordpress
![arq](https://github.com/felipeoliveira1212/atividade-aws-docker-felipe/assets/165864916/f26c5d8a-ed40-4973-a618-a6406d5c419b)

Etapas de execução
Configuração da Network:

    Acessei o console AWS e entrei no serviço VPC.
    No menu lateral esquerdo, na seção de Virtual private cloud selecionei Your VPCs.
    Dentro de Your VPCs cliquei no botão Create VPC.
    Alterei as seguintes configurações:
        Em Resources to create selecionei VPC and more.
   

        Em Number of Availability Zones (AZs) selecionei 2.
        Em NAT gateways selecionei In 1 AZ.
        
    Cliquei em Create VPC.
![Screenshot from 2024-05-07 09-28-45](https://github.com/felipeoliveira1212/atividade-aws-docker-felipe/assets/165864916/aa190895-a844-4216-8b9b-cfbf90289664)

Configuração dos Security Groups:

Acessei o console AWS e entrei no serviço EC2.

No menu lateral esquerdo, na seção de Network & Security, selecionei Security Groups.

Dentro de Security Groups, cliquei no botão Create security group.

Criei e configurei os seguintes security groups usando a VPC criada anteriormente:

Configuração dos Security Groups:

Acessei o console AWS e entrei no serviço EC2.

No menu lateral esquerdo, na seção de Network & Security, selecionei Security Groups.

Dentro de Security Groups, cliquei no botão Create security group.

Criei e configurei os seguintes security groups usando a VPC criada anteriormente:

SGLOAD - Inbound rules

Type 	Protocol 	Port Range 	Source
HTTP 	TCP 	80 	0.0.0.0/0

SGEC2 - Inbound rules
Type 	Protocol 	Port Range 	Source
SSH 	TCP 	22 	EC2 ICE
HTTP 	TCP 	80 	Load Balancer

SGEC2ICE - Outbound rules
Type 	Protocol 	Port Range 	Source
SSH 	TCP 	22 	EC2 Web Server

SHRDS - Inbound rules
Type 	Protocol 	Port Range 	Source
MYSQL/Aurora 	TCP 	3306 	EC2 Web Server

SGNFS - Inbound rules
Type 	Protocol 	Port Range 	Source
NFS 	TCP 	2049 	EC2 Web Server

Criando o Elastic File System:

Na tela do Elastic File System cliquei no botão Create file system.

Depois cliquei no botão Customize.

Executei a seguinte configuração:

    Step 1 - File system settings:

    No campo Name digitei "EFS-DOCKERSERVER10".
    Cliquei em Next.

Step 2 - Network access:

    No campo Virtual Private Cloud (VPC) selecionei a VPC que foi criada anteriormente.
    No campo Subnet ID selecionei as subnets privadas de cada AZ.
    No campo Security groups selecionei o grupo "EFS" que foi criado anteriormente.
    Cliquei em Next.

Step 3 - optional - File system policy:

    Cliquei em Next.

    Revisei e cliquei em Create para finalizar.


Criando o Relational Database Service:

    Acessei o console AWS e entrei no serviço de RDS.
    Na tela Dashboard cliquei no botão Create database.
    Executei a seguinte configuração:
        Na seção Engine options selecionei MySQL.
        Na seção Templates selecionei Free tier.
        Na seção Credentials Settings adicionei uma Master password e confirmei.
        Na seção Conectivity, no campo Virtual private cloud selecionei a VPC criada anteriormente.
        No campo Existing VPC security groups selecionei o grupo "SHRDS" que foi criado anteriormente.
        Na seção Additional configuration, no campo Initial database name coloquei o nome "databasedocker10".
    Revisei e cliquei em Create database para finalizar.
![Screenshot from 2024-05-07 08-15-38](https://github.com/felipeoliveira1212/atividade-aws-docker-felipe/assets/165864916/e81cd0c0-98af-44ab-871e-b731151f77a3)
![Screenshot from 2024-05-07 08-16-11](https://github.com/felipeoliveira1212/atividade-aws-docker-felipe/assets/165864916/fb4dbf30-627f-414c-b53e-8118ea067fd3)


Criando o Classic Load Balancer:

    Acessei o console AWS e entrei no serviço EC2.
    No menu lateral esquerdo, na seção de Load Balancing selecionei Load Balancers.
    Dentro de Load Balancers cliquei no botão Create load balancer.
    Em Load balancer types cliquei em Classic Load Balancer e depois em Create.
    No campo Load balancer name digitei "LOADDOCKER".
    Na seção Network mapping, no campo VPC selecionei a VPC criada anteriormente.
    No campo Mappings selecionei as duas AZ's e suas respectivas subnets públicas.
    No campo de Security groups selecionei o grupo "SGLOAD" que foi criado anteriormente.
   ![Screenshot from 2024-05-07 08-48-11](https://github.com/felipeoliveira1212/atividade-aws-docker-felipe/assets/165864916/7f67d199-1ddc-49c2-b6f1-dce8a270290f)

Criando o Launch Template:
Acessei o console AWS e entrei no serviço EC2.
No menu lateral esquerdo, na seção Instances selecionei Launch Templates.
Dentro de Launch Templates cliquei no botão Create launch template.
No campo Launch template name digitei "templatedocker".
No campo Template version description digitei "templaforatedocker.
Em Application and OS Images cliquei em Quick Start, depois cliquei em Amazon Linux e selecionei a Amazon Linux 2023 AMI.
Na seção Instance type selecionei o tipo t3.small.
No campo Key pair name selecionei a key pair criada anteriormente.
Em Network settings, no campo Security groups selecionei o grupo "EC2 Web Server" que foi criado anteriormente.


Criando o Auto Scaling Groups:
Acessei o console AWS e entrei no serviço EC2.
No menu lateral esquerdo, na seção de Auto Scaling selecionei Auto Scaling Groups.
Dentro de Auto Scaling groups cliquei no botão Create Auto Scaling group.
Executei a seguinte configuração:

    Step 1 - Choose launch template:

    No campo Auto Scaling group name digitei "SCALINGDOCKER".
    Na seção Launch template selecionei o template criado anteriormente.
    Cliquei em Next.

Step 2 - Choose instance launch options:

    Na seção Network, no campo VPC selecionei a VPC criada anteriormente.
    No campo Availability Zones and subnets selecionei as duas subnets privadas criadas previamente.
    Cliquei em Next.

Step 3 - Configure advanced options:

    Na seção Load balancing selecionei Attach to an existing load balancer.
    Na seção Attach to an existing load balancer cliquei em Choose from Classic Load Balancers e selecionei o load balancer criado anteriormente.
    
Step 4 - Configure group size and scaling:

    No campo Desired capacity digitei "2".
    Em Scaling, no campo Min desired capacity digitei "2".
    No campo Max desired capacity digitei "4".
    Em Automatic scaling selecionei a opção Target tracking scaling policy
    No campo Metric type deixei selecionado Average CPU utilization.
    No campo Target value digitei "75".
    Cliquei em Next.

